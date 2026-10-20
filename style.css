/* ---------- Knowledge documents (same text as the data/ files) ---------- */
const DOCS = {
  "stay_policy.txt": `Standard check-in begins at 2 PM.
Standard checkout is at 11 AM.

Guests must provide the identification requested by reception.
Early check-in depends on room availability.

A late-checkout estimate does not extend a reservation.
Reception must approve any extension.`,
  "amenities_guide.txt": `Breakfast is served from 7 AM to 10 AM.
The fitness room is open from 6 AM to 9 PM.

Wi-Fi access instructions are available at reception.
Guests should contact reception for missing room supplies.

The assistant cannot check the current availability
of hotel services.`,
  "checkout_policy.txt": `Checkout at or before 11 AM has no late-checkout charge.

After 11 AM and up to 2 PM, the charge is 25 percent
of the nightly room rate.

After 2 PM and up to 6 PM, the charge is 50 percent
of the nightly room rate.

After 6 PM, the charge is 100 percent of the nightly rate.

These bands apply to checkout on the scheduled departure day.`
};

/* ---------- Tool: calculate_late_checkout ---------- */
function calculateLateCheckoutFee(nightlyRate, checkoutTime) {
  const rateText = String(nightlyRate).trim().replace(/,/g, "");
  if (!/^\d+(\.\d+)?$/.test(rateText) || Number(rateText) <= 0 || !isFinite(Number(rateText)))
    throw new Error("Nightly rate must be a number greater than zero.");
  const t = String(checkoutTime).trim();
  const m = /^([01]\d|2[0-3]):([0-5]\d)$/.exec(t);
  if (!m) throw new Error("Checkout time must be a valid HH:MM time on the 24-hour clock (00:00-23:59).");
  const minutes = Number(m[1]) * 60 + Number(m[2]);

  let pct = 100;
  if (minutes <= 11 * 60) pct = 0;
  else if (minutes <= 14 * 60) pct = 25;
  else if (minutes <= 18 * 60) pct = 50;

  const rateCents = Math.round(Number(rateText) * 100);
  const chargeCents = Math.floor((rateCents * pct + 50) / 100); // round half up, integer maths
  return {
    checkout_time: t,
    charge_percentage: pct,
    additional_charge: chargeCents / 100,
    approval_required: minutes > 11 * 60
  };
}

/* ---------- RAG: sentence-level keyword retrieval ---------- */
const STOP = new Set("a an the is are was be to of in on at for and or do does can i my me you we it this that what when where which who how there any up by with from as if".split(" "));
const OUT_OF_SCOPE = /\b(reservations?|bookings?|confirmation|assigned|assign|my room|room number|which room|upgrade me|cancel|refund)\b/i;
const tokens = text => (text.toLowerCase().match(/[a-z0-9]+/g) || [])
  .filter(w => !STOP.has(w)).map(w => (w.length > 3 && w.endsWith("s")) ? w.slice(0, -1) : w);

const UNITS = [];
for (const [source, body] of Object.entries(DOCS))
  for (const para of body.split(/\n\s*\n/)) {
    const flat = para.split(/\s+/).join(" ");
    for (const s of flat.match(/[^.]+\.?/g) || [])
      if (s.trim()) UNITS.push({ text: s.trim(), source, tokens: new Set(tokens(s)) });
  }
const DF = {};
UNITS.forEach(u => u.tokens.forEach(t => DF[t] = (DF[t] || 0) + 1));
const idf = t => Math.log(1 + UNITS.length / (1 + (DF[t] || 0)));

function answerQuestion(question) {
  question = (question || "").trim();
  if (!question) return { status: "empty", answer: "Type a question about check-in, checkout, or hotel amenities.", sources: [] };
  if (OUT_OF_SCOPE.test(question))
    return { status: "out_of_scope", answer: "I can't look up reservations or room assignments. Please ask reception for help with your booking.", sources: [] };
  const q = new Set(tokens(question));
  const none = { status: "no_match", answer: "I couldn't find that in the hotel guides. Please ask reception.", sources: [] };
  if (!q.size) return none;
  const scored = [];
  for (const u of UNITS) {
    const hit = [...q].filter(t => u.tokens.has(t));
    if (hit.length) scored.push({ score: hit.reduce((a, t) => a + idf(t), 0), frac: hit.length / q.size, u });
  }
  scored.sort((a, b) => b.score - a.score);
  if (!scored.length || scored[0].frac < 0.4) return none;
  const best = scored[0].score;
  const picks = scored.slice(0, 2).filter(s => s.score >= 0.6 * best && s.frac >= 0.4).map(s => s.u);
  return { status: "ok", answer: picks.map(u => u.text).join(" "), sources: [...new Set(picks.map(u => u.source))].sort() };
}
/* ---------- end of logic ---------- */

if (typeof document !== "undefined") {
  const $ = id => document.getElementById(id);
  ["When is breakfast served?", "Is early check-in guaranteed?", "Which room is assigned to my reservation?"].forEach(q => {
    const li = document.createElement("li"), b = document.createElement("button");
    b.type = "button"; b.textContent = q;
    b.onclick = () => { $("question").value = q; $("askForm").requestSubmit(); };
    li.appendChild(b); $("hints").appendChild(li);
  });

  $("askForm").addEventListener("submit", e => {
    e.preventDefault();
    const r = answerQuestion($("question").value);
    const box = $("answer");
    box.className = "answer " + r.status;
    box.replaceChildren();
    const p = document.createElement("p"); p.textContent = r.answer; box.appendChild(p);
    if (r.sources.length) { const s = document.createElement("small"); s.textContent = "From " + r.sources.join(", "); box.appendChild(s); }
    box.hidden = false;
  });

  $("feeForm").addEventListener("submit", e => {
    e.preventDefault();
    ["feeError", "receipt", "feeNote"].forEach(id => $(id).hidden = true);
    try {
      const r = calculateLateCheckoutFee($("rate").value, $("time").value);
      $("rTime").textContent = r.checkout_time;
      $("rPct").textContent = r.charge_percentage + "% of nightly rate";
      $("rFee").textContent = "INR " + r.additional_charge.toLocaleString("en-IN", { minimumFractionDigits: 2, maximumFractionDigits: 2 });
      $("feeNote").textContent = r.approval_required
        ? "Reception approval is required for any checkout after 11:00 AM. This is an estimate only and does not extend your reservation."
        : "No late-checkout charge and no extension approval needed.";
      $("receipt").hidden = false; $("feeNote").hidden = false;
    } catch (err) {
      $("feeError").textContent = err.message; $("feeError").hidden = false;
    }
  });
}

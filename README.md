# 🏨 Hotel Guest Information Assistant

A web-based application designed to help hotel guests access hotel information and calculate additional checkout charges easily.

## 📌 Project Overview

The Hotel Guest Information Assistant provides a simple interface for guests to explore hotel policies, learn about amenities, and calculate additional charges based on their checkout time.

The project combines a hotel information assistant with a checkout charge calculator to improve convenience and simplify access to hotel information.

## 🎯 Objectives

- Provide quick access to hotel policies and amenities.
- Answer common questions about hotel services.
- Calculate additional checkout charges based on checkout time.
- Validate user inputs to avoid invalid calculations.
- Provide a simple and user-friendly interface.

## ✨ Features

### 💬 Hotel Information Assistant
- Answers questions about hotel policies and amenities.
- Provides information about breakfast timings.
- Explains early check-in and checkout policies.
- Uses available hotel information to respond to relevant questions.

### 🧮 Additional Checkout Charge Calculator
- Accepts the nightly room rate and checkout time.
- Calculates additional charges according to predefined rules.
- Validates the room rate and checkout time.
- Displays the calculated additional charge.

### 📋 Checkout Charge Policy

| Checkout Time | Additional Charge |
|---------------|------------------:|
| Up to 11:00 AM | 0% |
| After 11:00 AM to 2:00 PM | 25% |
| After 2:00 PM to 6:00 PM | 50% |
| After 6:00 PM | 100% |

**Formula:**

Additional Charge = Nightly Room Rate × Charge Percentage / 100

**Example:**

For a nightly room rate of ₹4,000 and checkout at 12:00 PM:

Additional Charge = ₹4,000 × 25 / 100 = ₹1,000

Late checkout approval may be required according to hotel policy.

## 🛠️ Technologies Used

- HTML – Webpage structure
- CSS – Styling and layout
- JavaScript – Interactive features and calculations

## 📂 Project Structure

```text
Hotel-Guest-Information-Assistant/
├── index.html
├── style.css
├── script.js
└── README.md

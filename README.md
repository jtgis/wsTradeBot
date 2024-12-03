# Wealthsimple Day Trading Bot

A Python script for automated day trading using Wealthsimple Trade. The script fetches real-time stock data, calculates technical indicators, and executes trades based on predefined strategies.

## Features

- **Real-time Intraday Trading**:
  - Fetches 1-minute interval stock data.
  - Calculates technical indicators: VWAP, SMA, and RSI.
- **Automated Trading Logic**:
  - Executes buy/sell trades based on VWAP, SMA crossovers, and RSI levels.
- **Two-Factor Authentication (2FA)**:
  - Supports secure login using dynamic 2FA codes.
- **Account Selection**:
  - Automatically selects the desired Wealthsimple account (e.g., TFSA, RRSP, or Personal).
- **Risk Management**:
  - Stops trades based on VWAP and SMA indicators.

---

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/your_username/wealthsimple-day-trading-bot.git
   cd wealthsimple-day-trading-bot


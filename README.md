Features
Fetches Intraday Data:

Uses 1-minute intervals for intraday trading data.
Calculates VWAP, SMA, and RSI for trading signals.
Handles Multiple Accounts:

Retrieves and selects the correct account using the account name.
Trading Logic:

Buy Signal: Price crosses above VWAP and SMA short-term crosses above SMA long-term.
Sell Signal: Price drops below VWAP or SMA short-term crosses below SMA long-term.
Two-Factor Authentication:

Uses pyotp to generate 2FA codes dynamically.
Risk Management:

Uses defined indicators and stop-loss logic (VWAP and SMA).
Setup
Install dependencies:

bash
Copy code
pip install yfinance pyotp wealthsimple-trade-python pandas
Replace placeholders:

USERNAME and PASSWORD: Your Wealthsimple login credentials.
AUTH_SECRET_KEY: Secret key from your authenticator app.
ACCOUNT_NAME: Name of the Wealthsimple account to use.
Run the script:

bash
Copy code
python your_script_name.py
Next Steps
Backtest: Run the logic on historical data to validate performance.
Simulate: Test in a simulated environment before deploying live.
Optimize: Adjust SMA, RSI, and VWAP thresholds based on your trading goals.

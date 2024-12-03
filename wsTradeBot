import yfinance as yf
import pandas as pd
import pyotp
from wealthsimple import Wealthsimple
import time

# --- CONFIGURATION ---
USERNAME = "your_email@example.com"
PASSWORD = "your_password"
AUTH_SECRET_KEY = "YOUR_AUTHENTICATOR_APP_SECRET_KEY"  # Replace with your authenticator app's secret key
ACCOUNT_NAME = "Personal Account"  # Name of the account to use (e.g., "TFSA", "Personal Account")
SECURITY = "AAPL"  # Stock ticker symbol
TRADE_AMOUNT = 100  # Amount in dollars to trade
SHORT_WINDOW = 10  # Short moving average window
LONG_WINDOW = 30  # Long moving average window
RSI_OVERBOUGHT = 70
RSI_OVERSOLD = 30

# --- FUNCTIONS ---
def generate_2fa_code():
    """Generate the current 2FA code using the secret key."""
    totp = pyotp.TOTP(AUTH_SECRET_KEY)
    return totp.now()

def fetch_stock_data(ticker, period="1d", interval="1m"):
    """Fetch intraday stock data."""
    try:
        data = yf.download(ticker, period=period, interval=interval)
        # Add VWAP (Volume-Weighted Average Price)
        data['VWAP'] = (data['Close'] * data['Volume']).cumsum() / data['Volume'].cumsum()
        return data
    except Exception as e:
        print(f"Error fetching stock data: {e}")
        return None

def calculate_indicators(data):
    """Calculate technical indicators like moving averages and RSI."""
    data["SMA_Short"] = data["Close"].rolling(window=SHORT_WINDOW).mean()
    data["SMA_Long"] = data["Close"].rolling(window=LONG_WINDOW).mean()
    delta = data["Close"].diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=14).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=14).mean()
    rs = gain / loss
    data["RSI"] = 100 - (100 / (1 + rs))
    return data

def get_account_id(ws, account_name):
    """Retrieve the account ID based on the account name."""
    accounts = ws.get_accounts()
    for account in accounts:
        if account["name"] == account_name:
            return account["id"]
    raise ValueError(f"Account with name '{account_name}' not found.")

def trade_logic(data, ws, account_id, ticker):
    """Implement the trading logic for intraday trading."""
    if len(data) < max(SHORT_WINDOW, LONG_WINDOW):
        return

    # Access the latest data
    latest = data.iloc[-1]

    try:
        price = float(latest["Close"])
        sma_short = float(latest["SMA_Short"])
        sma_long = float(latest["SMA_Long"])
        vwap = float(latest["VWAP"])
    except KeyError as e:
        print(f"KeyError: Missing indicator {e}. Ensure all indicators are calculated.")
        return

    # Print latest information
    print(f"{ticker} | Price: {price:.2f} | SMA Short: {sma_short:.2f} | SMA Long: {sma_long:.2f} | VWAP: {vwap:.2f}")

    # Entry: Price crosses above VWAP
    if price > vwap and sma_short > sma_long:
        print("Buy signal triggered!")
        try:
            ws.place_order(account_id=account_id, symbol=ticker, amount=TRADE_AMOUNT, order_type="buy")
        except Exception as e:
            print(f"Error placing buy order: {e}")

    # Exit: Price drops below VWAP or SMA Short crosses below SMA Long
    elif price < vwap or sma_short < sma_long:
        print("Sell signal triggered!")
        try:
            ws.place_order(account_id=account_id, symbol=ticker, amount=TRADE_AMOUNT, order_type="sell")
        except Exception as e:
            print(f"Error placing sell order: {e}")

# --- MAIN ---
try:
    # Initialize Wealthsimple session with 2FA
    ws = Wealthsimple(USERNAME, PASSWORD, two_factor_callback=generate_2fa_code)
    print("Logged in successfully!")

    # Retrieve account ID
    account_id = get_account_id(ws, ACCOUNT_NAME)
    print(f"Using account: {ACCOUNT_NAME} (ID: {account_id})")

    # Start the trading loop
    while True:
        stock_data = fetch_stock_data(SECURITY, period="1d", interval="1m")
        if stock_data is not None and not stock_data.empty:
            stock_data = calculate_indicators(stock_data)
            trade_logic(stock_data, ws, account_id, SECURITY)

        time.sleep(60)  # Wait 1 minute before fetching new data

except Exception as e:
    print(f"Error: {e}")

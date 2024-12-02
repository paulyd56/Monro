import pandas as pd
import requests

# Function to fetch historical price data for Monero
def fetch_monero_data():
    url = 'https://api.coingecko.com/api/v3/coins/monero/market_chart?vs_currency=usd&days=30&interval=daily'
    response = requests.get(url)
    data = response.json()
    prices = [item[1] for item in data['prices']]
    return prices

# Function to calculate SMA
def calculate_sma(prices, n):
    return pd.Series(prices).rolling(window=n).mean()

# Function to generate buy/sell signals
def generate_signals(prices, sma):
    signals = []
    for i in range(len(prices)):
        if prices[i] > sma[i]:
            signals.append('Buy')
        elif prices[i] < sma[i]:
            signals.append('Sell')
        else:
            signals.append('Hold')
    return signals

# Fetch Monero price data
prices = fetch_monero_data()

# Calculate SMA with a window of 3 periods
sma = calculate_sma(prices, 3)

# Generate buy/sell signals
signals = generate_signals(prices, sma)

# Create a DataFrame to display the results
df = pd.DataFrame({
    'Price': prices,
    'SMA': sma,
    'Signal': signals
})

print(df)

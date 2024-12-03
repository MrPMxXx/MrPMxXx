# Required Libraries
import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt

# Fetch Historical Data
def fetch_data(symbol, start_date, end_date):
    data = yf.download(symbol, start=start_date, end=end_date)
    return data

# Calculate Moving Averages
def calculate_moving_averages(data, short_window, long_window):
    data['Short_MA'] = data['Close'].rolling(window=short_window).mean()
    data['Long_MA'] = data['Close'].rolling(window=long_window).mean()
    return data

# Generate Buy/Sell Signals
def generate_signals(data):
    data['Signal'] = 0
    data.loc[data['Short_MA'] > data['Long_MA'], 'Signal'] = 1  # Buy Signal
    data.loc[data['Short_MA'] <= data['Long_MA'], 'Signal'] = -1  # Sell Signal
    return data

# Visualize Data
def plot_trends(data, symbol):
    plt.figure(figsize=(12, 6))
    plt.plot(data['Close'], label=f"{symbol} Close Price", alpha=0.5)
    plt.plot(data['Short_MA'], label="Short Moving Average", alpha=0.8)
    plt.plot(data['Long_MA'], label="Long Moving Average", alpha=0.8)
    plt.scatter(data.index, data['Close'], c=data['Signal'].map({1: 'green', -1: 'red'}), alpha=0.6, label='Signals')
    plt.title(f"{symbol} Trend Following Strategy")
    plt.xlabel("Date")
    plt.ylabel("Price")
    plt.legend()
    plt.grid()
    plt.show()

# Main Function
def main():
    symbol = "AAPL"  # Example: Apple Inc.
    start_date = "2022-01-01"
    end_date = "2023-01-01"
    short_window = 10  # Short-term MA window
    long_window = 50   # Long-term MA window

    # Fetch and process data
    data = fetch_data(symbol, start_date, end_date)
    data = calculate_moving_averages(data, short_window, long_window)
    data = generate_signals(data)

    # Visualize results
    plot_trends(data, symbol)

# Run the bot
if __name__ == "__main__":
    main()

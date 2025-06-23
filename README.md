import time
import requests

API_KEY = '476df49e97da4f398e93205a47467039'
TOKEN = '7724128489:AAFG8XHgakB4EUlL7qPxQNst-CybCGp6lrk'
CHAT_ID = '2041894604'

def get_rsi(symbol):
    url = f"https://api.twelvedata.com/rsi?symbol={symbol}&interval=1min&time_period=14&apikey={API_KEY}"
    response = requests.get(url).json()
    return float(response['values'][0]['rsi'])

def send_signal(symbol_name, rsi):
    if rsi < 30:
        msg = f"📊 الزوج: {symbol_name}\n🔼 إشارة: شراء (RSI = {rsi})"
    elif rsi > 70:
        msg = f"📊 الزوج: {symbol_name}\n🔽 إشارة: بيع (RSI = {rsi})"
    else:
        msg = f"❌ الزوج: {symbol_name} لا توجد فرصة واضحة (RSI = {rsi})"
    
requests.post(
        f"https://api.telegram.org/bot{TOKEN}/sendMessage",
        data={"chat_id": CHAT_ID, "text": msg}
    )

symbols = {
    "EUR/USD": "EUR/USD",
    "GBP/JPY": "GBP/JPY"
}

while True:
    for name, code in symbols.items():
        send_signal(name, get_rsi(code))
    time.sleep(300)

import asyncio
import aiohttp
import csv
import time
import os

# إعداد المسار (في السحابة يُفضل استخدام مجلد مؤقت أو مسار العمل الحالي)
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
save_path = os.path.join(BASE_DIR, "market_data")

if not os.path.exists(save_path):
    os.makedirs(save_path)

symbols = ["BTCUSDT", "ETHUSDT", "SOLUSDT", "ADAUSDT", "DOTUSDT", 
           "AVAXUSDT", "LINKUSDT", "MATICUSDT", "XRPUSDT", "LTCUSDT"]

async def fetch_and_save(session, symbol):
    url = f"https://api.bybit.com/v5/market/recent-trade?category=linear&symbol={symbol}&limit=1"
    filename = os.path.join(save_path, f"{symbol}_data.csv")
    
    try:
        async with session.get(url, timeout=10) as response:
            if response.status == 200:
                data = await response.json()
                if data.get('retCode') == 0 and data.get('result') and 'list' in data['result']:
                    trade = data['result']['list'][0]
                    
                    file_exists = os.path.isfile(filename)
                    with open(filename, mode='a', newline='', encoding='utf-8') as f:
                        writer = csv.writer(f)
                        if not file_exists:
                            writer.writerow(['timestamp', 'symbol', 'price', 'size'])
                        writer.writerow([trade['time'], symbol, trade['price'], trade['size']])
            else:
                print(f"فشل الاتصال بـ {symbol}: كود {response.status}")
    except Exception as e:
        print(f"خطأ في جلب {symbol}: {e}")

async def main():
    print("البوت بدأ العمل بنجاح...")
    async with aiohttp.ClientSession() as session:
        while True:
            tasks = [fetch_and_save(session, s) for s in symbols]
            await asyncio.gather(*tasks)
            # التأخير بين كل عملية سحب (لتقليل استهلاك الـ API)
            await asyncio.sleep(5)

if __name__ == "__main__":
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("تم إيقاف البوت.")

# EURUSDi — Range Breakout TradingView Indicator (Pine v5)

A custom TradingView indicator designed to capture a specific **M15 range candle in IST**, lock the range, and detect **M5 breakout confirmations** with automated alert formatting (PineConnector-compatible).  
Built for precision, automation, clean logic — and proven on historical data.

> 🚀 **Performance Highlight:**  
> Internal backtests show **~400% return over 3 years** with disciplined execution and fixed risk-per-trade settings.  
> *(Past performance does not guarantee future results. Always test before trading live.)*

---

## 📌 Features

- Detects **M15 range candle** at a configurable IST time (Hour + Minute).
- Locks the high/low range once the candle closes.
- Uses **M5 candle breakout logic** to filter early fake moves.
- Computes:
  - Risk in price units  
  - Risk in pips  
  - Take Profit in pips as a multiple (2× by default)
- Sends fully formatted alert messages for automation with:
  - **PineConnector**
  - Custom webhook scripts  
  - Any algo execution engine
- Plots clean range lines on the chart.

---

## 📦 Indicator Code (Pine v5)

EURUSDi.pine


You can paste it directly into TradingView → Pine Script Editor → Save & Add to Chart.

---

## ⚙️ How It Works

### 1. **Range Detection**
At the specified IST time (default: **18:45 IST**) the script:
- requests the M15 candle  
- captures its `high` and `low`  
- locks the range for the session  

### 2. **Breakout Logic**
Breakout is confirmed when:
- **M5 close** goes beyond the range  
- **M5 open** is on the opposite side (filters spikes)

Works for both **long** and **short** positions.

### 3. **Alert Message Format**

The script automatically sends alert text like:

LICENSEID,buy,EURUSD.x,risk=50,sl=42.5,tp=85


Format:
1. License ID (for PineConnector or your custom parser)
2. direction: `buy` / `sell`
3. symbol (auto-appended with `.x`)
4. risk (USD)
5. sl (pips)
6. tp (pips)

Just set up a TradingView alert with:
- Condition: *This Indicator → Any alert()*
- Frequency: *Once Per Bar Close*
- Webhook (optional): your execution engine URL

---

## 🧠 Inputs

| Input | Meaning |
|------|---------|
| License ID | PineConnector / custom auth key |
| Risk per Trade (USD) | Fixed dollar risk |
| Take Profit Multiple | TP = risk × multiple |
| Range Candle Hour (IST) | 0–23 |
| Range Candle Minute | 0–59 (15-min steps recommended) |
| Line color / width | Range visual styling |

Defaults are optimized for EURUSD behavior around evening session volatility.

---

## 🖥️ Example Webhook Receiver (Optional)

If you're not using PineConnector, here’s a simple Flask parser:

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/webhook", methods=["POST"])
def webhook():
    data = request.get_data(as_text=True)
    parts = data.split(",")

    license_id = parts[0]
    direction  = parts[1]
    symbol     = parts[2]

    kv = { p.split("=")[0]: p.split("=")[1] for p in parts[3:] }

    print("ALERT:", direction, symbol, kv)
    return jsonify({"status": "ok"}), 200

app.run(port=5000)


The full indicator script is inside this repository:


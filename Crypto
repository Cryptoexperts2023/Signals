from telethon.sync import TelegramClient, events
import re
import json
import requests  # Import the 'requests' library for HTTP requests


api_id = 1556745
api_hash = '7e0d5cf8945fb60a5c52cd229db489b5'
phone_number = '919780237816'

client = TelegramClient('session', api_id, api_hash)

channel_username = 'signalstestsignal'

message = {
    "name": "CoinSignals",
    "secret": "weps7kj6w9",
    "symbol": "",
    "side": "",
    "open": {
        "scaled": {
            "price1": {
                "mode": "value",
                "value": 1
            },
            "price2": {
                "mode": "value",
                "value": 2
            }
        }
    },
    "sl": {
        "price": ""
    },
    "tp": {
        "orders": {
            "0": {
                "price": "",
                "piece": "25.0"
            },
            "1": {
                "price": "",
                "piece": "25.0"
            },
            "2": {
                "price": "",
                "piece": "25.0"
            },
            "3": {
                "price": "",
                "piece": "25.0"
            }
        }
    }
}

@client.on(events.NewMessage(chats=channel_username))
async def handle_new_channel_message(event):
    signal_message = event.message.text
    print("Received Signal Message:", signal_message)

    if "💲NEW VIP SIGNAL💲" not in signal_message:
        print("Not a new VIP signal. Ignoring.")
        return

    ticker_match = re.search(r":\s*([A-Za-z0-9/]+)\s*/USDT", signal_message)
    if ticker_match:
        message["symbol"] = ticker_match.group(1)

    if "short" in signal_message.lower():
        message["side"] = "sell"
    elif "long" in signal_message.lower():
        message["side"] = "buy"

    entry_price_range = re.search(r"ENTRY(?:\s+ZONE)?\s+([\d.]+)\s*-\s*([\d.]+)", signal_message)
    entry_price_start = entry_price_range.group(1) if entry_price_range else ""
    entry_price_end = entry_price_range.group(2) if entry_price_range else ""
    stop_loss_match = re.search(r"STOP\s+([\d.]+)", signal_message)
    stop_loss = stop_loss_match.group(1) if stop_loss_match else ""
    take_profit_matches = re.findall(r"TP(\d+)\s+([\d.]+)", signal_message)
    take_profit_prices = [match[1] for match in take_profit_matches]

    message["open"]["scaled"]["price1"]["value"] = entry_price_start
    message["open"]["scaled"]["price2"]["value"] = entry_price_end
    message["sl"]["price"] = stop_loss
    for i, tp_order in enumerate(message["tp"]["orders"].values()):
        tp_order["price"] = take_profit_prices[i]

    formatted_message = json.dumps(message, indent=4)
    
    print(formatted_message)

    url = 'https://hook.finandy.com/sHptK-qNMMwktOIaqVUK'  # Remove extra indentation
    payload = formatted_message  # Remove parentheses

    headers = {
        'Content-Type': 'application/json'
    }

    response = requests.post(url, data=payload, headers=headers)  # Use 'data' for JSON payload
    print(response.json())
    print(payload)

    if response.status_code == 200:
        print("Webhook message sent successfully!")
    else:
        print("Failed to send webhook message. Status code:", response.status_code)
    # Extract Take Profit and Stop Loss values
    tp_matches = re.findall(r"Target (\d+) : ([\d.]+)", signal_message)
    sl_match = re.search(r"SL : ([\d.]+)", signal_message)

    # Initialize TP and SL variables
    tp_values = {}
    sl_value = ""

    # Populate TP and SL values
    for match in tp_matches:
        tp_values[f"Target {match[0]}"] = match[1]

    if sl_match:
        sl_value = sl_match.group(1)

    # Determine the strategy (SHORT or LONG) based on keywords
    if "SHORT" in signal_message:
        strategy_type = "SHORT"
    elif "LONG" in signal_message:
        strategy_type = "LONG"
    else:
        strategy_type = "UNKNOWN"

    # Use regular expressions to extract values
    pair_match = re.search(rf'{strategy_type} : (.+)', signal_message)
    if pair_match:
        pair = pair_match.group(1)
    else:
        pair = "Unknown"
    entry_zone_match = re.search(r'(?:ENTRY(?:\s+ZONE)?) (.+)', signal_message)
    if entry_zone_match:
        entry_zone = entry_zone_match.group(1)
    else:
        entry_zone = "Unknown"  # Set a default value or handle the case where no match is found.

    entry_zone_start, entry_zone_end = map(float, re.findall(r'(\d+\.\d+)- (\d+\.\d+)', entry_zone)[0])
    stop_loss = re.search(r'STOP (.+)', signal_message).group(1)
    target_prices = re.findall(r'TP(\d+) (\d+\.\d+)', signal_message)

    # Create the formatted strategy message
    strategy_message = f"🐮 Strategy: {strategy_type}\n👉 Exchange: BINANCE\n👉 Account: Futures\n👉 Pair: {pair}\n👉 Invest: 10%\n👉 Leverage: 10x CROSS\n🎯 Exit:\n"
    for target_num, target_price in target_prices:
        strategy_message += f"⎿ Target {target_num} : {target_price}\n"

    strategy_message += f"💰 Entry Zone: {entry_zone_start:.2f}-{entry_zone_end:.2f}\n💰 Stoploss : {stop_loss}\n"

    # Print the formatted strategy message
    #await client.send_message('cryptoxoerts2023', strategy_message)
    print(strategy_message)

with client:
    print("Telegram client started. Listening for new messages...")
    client.run_until_disconnected()

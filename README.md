import requests
from datetime import datetime

API_URL = "https://api.etherscan.io/v2/api"

API_KEY = "YOUR_ETHERSCAN_API_KEY"
 = "0x0000000000000000000000000000000000000000"

def get_transactions(wallet):
    params = {
        "chainid": 1,
        "module": "account",
        "action": "txlist",
        "address": wallet,
        "startblock": 0,
        "endblock": 99999999,
        "page": 1,
        "offset": 20,
        "sort": "desc",
        "apikey": API_KEY,
    }

    response = requests.get(
        API_URL,
        params=params,
        timeout=15
    )

    response.raise_for_status()
    data = response.json()

    if data.get("status") != "1":
        raise RuntimeError(data.get("message", "API request failed"))

    return data["result"]


def format_eth(value):
    return int(value) / 10**18


def main():
    transactions = get_transactions(WALLET)

    print(f"Wallet: {WALLET}")
    print(f"Transactions found: {len(transactions)}\n")

    for tx in transactions:
        timestamp = datetime.fromtimestamp(
            int(tx["timeStamp"])
        )

        value = format_eth(tx["value"])

        direction = (
            "OUT"
            if tx["from"].lower() == WALLET.lower()
            else "IN"
        )

        print("-" * 60)
        print(f"Time:      {timestamp}")
        print(f"Direction: {direction}")
        print(f"Value:     {value:.6f} ETH")
        print(f"From:      {tx['from']}")
        print(f"To:        {tx['to']}")
        print(f"Hash:      {tx['hash']}")


if __name__ == "__main__":
    main()

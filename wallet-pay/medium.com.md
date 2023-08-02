# Accepting cryptocurrency payments in the Telegram bot via WalletPay and Python/Flask.

![Cryptocurrency](tcoin.jpg)

Hi all!

The Telegram @wallet bot recently provided an [API for accepting payments](https://pay.wallet.tg/) in third-party Telegram bots. Of the cryptocurrencies, BTC, TON, USDT are supported.

You need to register on the site, provide information about the bot, that will be  connected to the API, go through the identification procedure (biometrics for individuals), wait for the application to be approved and setting the amount of commission for your payments.
My procedure took a little over a day.

After the application is approved, you get access to your personal account, where you need to generate a key to access the WalletPay API.

After that, you can start selling.
The buyer needs a link to pay the invoice for the goods/services of your bot through WalletPay. The code to get this link could be like this.

```python
import requests

def get_pay_link():
    headers = {
     'Wpay-Store-Api-Key': 'YOUR-API-KEY',
     'Content-Type': 'application/json',
     'Accept': 'application/json',
    }

    payload = {
      'amount': {
        'currencyCode': 'USD',  # invoice in US dollars
        'amount': '1.00',
      },
      'description': 'Goods and service.',
      'externalId': 'XXX-YYY-ZZZ',  # invoice ID (in your bot) for payment
      'timeoutSeconds': 60 * 60 * 24,  # invoice expiration time in seconds
      'customerTelegramUserId': '999666999',  # buyer's Telegram account ID
      'returnUrl': 'https://t.me/mybot',  # after successful payment redirect the buyer to your bot
      'failReturnUrl': 'https://t.me/wallet',  # leave the buyer in @wallet if there is no payment
    }

    response = requests.post(
      "https://pay.wallet.tg/wpay/store-api/v1/order",
      json=payload, headers=headers, timeout=10
    )
    data = response.json()

    if (response.status_code != 200) or (data['status'] not in ["SUCCESS", "ALREADY"]):
        logging.warning("# code: %s json: %s", response.status_code, data)
        return ''

    return data['data']['payLink']
```

The invoice can be issued in US dollars or euros. Conversion to BTC, TON, USDT will be performed at the WalletPay rate.

To ensure that the payment of an invoice was made, WalletPay provides two options:

-   periodic polling of the [invoice status](https://docs.wallet.tg/pay/#tag/Order/operation/getPreview)
-   [POST request](https://docs.wallet.tg/pay/#section/Webhook) at the `https` address you specified in your WalletPay dashboard (webhook in terms of WalletPay)

I used the second option. The webhook handler code could look like this.

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/tgwallet/ipn', methods=['POST'])
def ipn_tgwallet():
    for event in request.get_json():
        if event["type"] == "ORDER_PAID":
            data = event["payload"]
            print("Paid invoice N {} for the amount {} {}. Payment {} {}.".format(
              data["externalId"],  # Invoice ID (in your bot), which we specified when creating the payment link
              data["orderAmount"]["amount"],  # Invoice amount specified when creating the payment link
              data["orderAmount"]["currencyCode"],  # Invoice currency
              data["selectedPaymentOption"]["amount"]["amount"],  # How much did the buyer pay
              data["selectedPaymentOption"]["amount"]["currencyCode"]  # In what cryptocurrency
            ))

    # should always return code 200 to prevent WalletPay from calling the webhook again
    return 'OK'
```

Before transferring a paid product or service, you need to make sure that the webhook call came from WalletPay, and not from attackers who are eager to get our goods for free.

To do this, WalletPay offers two options:

-   checking whether the IP call belongs to the pool of WalletPay IP addresses
-   verifying the hash calculated from the webhook call data and your API key

The list of WalletPay IP addresses can be found in the [documentation](https://docs.wallet.tg/pay/#section/Webhook), and the following code can be used to verify the hash.

```python
import base64, hashlib, hmac

ENCODING = 'utf-8'

def is_valid(flask_request):
    text = '.'.join([
      flask_request.method,  # 'POST'
      flask_request.path,  # you need to use part of the address without the domain name, '/tgwallet/ipn' in our case
      flask_request.headers.get('WalletPay-Timestamp'),
      base64.b64encode(flask_request.get_data()).decode(ENCODING),
    ])
    signature = base64.b64encode(hmac.new(
      bytes('YOUR-API-KEY', ENCODING),
      msg=bytes(text, ENCODING),
      digestmod=hashlib.sha256
    ).digest())

    return flask_request.headers.get('Walletpay-Signature') == signature.decode(ENCODING)
```

Then use the `is_valid` function to validate call in the webhook handler.

Thank you for your attention.

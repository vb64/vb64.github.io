# Прием платежей в криптовалютах в Telegram боте через WalletPay и Python/Flask.

![Криптовалюты в Telegram](tcoin.jpg)

Приветствую сообщество.

Бот Telegram @wallet недавно предоставил [API для приема платежей](https://pay.wallet.tg/) в сторонних Telegram ботах. Из криптовалют поддерживаются BTC, TON, USDT.

Необходимо зарегистрироваться на сайте, предоставить сведения о подключаемом к API боте, пройти процедуру идентификации (биометрия для физических лиц), дождаться одобрения заявки и назначения размера комиссии для ваших платежей.
У меня процедура заняла чуть более суток.

После одобрения заявки получаете доступ в личный кабинет, где нужно сгенерировать ключ для доступа к API WalletPay.

После этого можно приступать к продажам.
Покупателю нужно предоставить ссылку для оплаты через WalletPay товаров/услуг вашего бота. Код для получения этой ссылки может быть таким.

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
        'currencyCode': 'USD',  # выставляем счет в долларах США
        'amount': '1.00',
      },
      'description': 'Goods and service.',
      'externalId': 'XXX-YYY-ZZZ',  # ID счета на оплату в вашем боте
      'timeoutSeconds': 60 * 60 * 24,  # время действия счета в секундах
      'customerTelegramUserId': '999666999',  # ID аккаунта Telegram покупателя
      'returnUrl': 'https://t.me/mybot',  # после успешной оплаты направить покупателя в наш бот
      'failReturnUrl': 'https://t.me/wallet',  # при отсутствии оплаты оставить покупателя в @wallet
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

Счет можно выставлять в долларах США, евро или рублях. Пересчет в BTC, TON, USDT будет выполнен по курсу WalletPay.

Для подтверждения факта оплаты счета, WalletPay предоставляет две опции: 

-   периодический опрос [состояния счета](https://docs.wallet.tg/pay/#tag/Order/operation/getPreview) с вашей стороны
-   [POST-запрос](https://docs.wallet.tg/pay/#section/Webhook) на указанный вами в личном кабинете `https` адрес (webhook в терминах WalletPay)

Я использовал второй вариант. Код обработчика вебхука может быть таким.

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/tgwallet/ipn', methods=['POST'])
def ipn_tgwallet():
    for event in request.get_json():
        if event["type"] == "ORDER_PAID":
            data = event["payload"]
            print("Оплачен счет N {} на сумму {} {}. Оплата {} {}.".format(
              data["externalId"],  # ID счета в вашем боте, который мы указывали при создании ссылки для оплаты
              data["orderAmount"]["amount"],  # Сумма счета, указанная при создании ссылки для оплаты
              data["orderAmount"]["currencyCode"],  # Валюта счета
              data["selectedPaymentOption"]["amount"]["amount"],  # Сколько оплатил покупатель
              data["selectedPaymentOption"]["amount"]["currencyCode"]  # В какой криптовалюте
            ))

    # нужно всегда возвращать код 200, чтобы WalletPay не делал повторных вызовов вебхука
    return 'OK'
```

Прежде чем передавать оплаченный товар или услугу, нужно убедиться, что вызов вебхука пришел из WalletPay, а не от злоумыщленников, жаждущих заполучить наш товар бесплатно.

Для этого WalletPay предлагает две опции:

-   проверка принадлежности IP вызова к пулу IP адресов WalletPay
-   проверка хеша, вычисленного на основе данных вызова вебхука и вашего ключа API

Список IP адресов WalletPay можно найти в [документации](https://docs.wallet.tg/pay/#section/Webhook), а для проверки хеша можно использовать следующий код.

```python
import base64, hashlib, hmac

ENCODING = 'utf-8'

def is_valid(flask_request):
    text = '.'.join([
      flask_request.method,  # 'POST'
      flask_request.path,  # нужно использовать часть адреса без имени домена, '/tgwallet/ipn' в нашем случае
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

Затем использовать функцию `is_valid` для проверки в обработчике вебхука.

Спасибо за внимание.

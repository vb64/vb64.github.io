# Виталий Богомолов

Разработка Telegram ботов с 2017 года. Используется Python/Flask и [немного Go](https://github.com/xelaj/mtproto) (в стадиии изучения и опробования).

## Собственные действующие проекты

- [EmailGateBot](https://vb64.github.io/telegram.email.notify/docs/ru/guide.html) присваивает каналам и группам Telegram специальный адрес email и публикует в них сообщения, отправленные на этот адрес. EmailGateBot не требует доступа к почтовым аккаунтам и прав администратора в группах. Бот бесплатен для персонального использования.
- [AlicaTalkBot](https://zen.yandex.ru/media/id/5a7c88094bf16140b018eb53/razgovor-s-telegoi-iandeksalisa-i-telegram-5cdbef3273f29b00b2d98a13) публикует в Telegram в виде текста фразы, продиктованные навыку "Разговор с телегой" голосового помощника Яндекс.Алиса и отправляет ответы из Telegram в Алису.
- [PotTeleBot](https://zen.yandex.ru/media/id/5a7c88094bf16140b018eb53/sovmestnye-rashody-5b3e609e9d936000a8dcc08b) ведет "общаую кассу" совместных расходов в группах Telegram.
- [BusinessSectorBot](https://vb64.github.io/telegram.business.sector/docs/ru/guide.html) ведет расписание для людей и организаций, которые работают с клиентами по предварительной записи. Мини CRM полностью внутри Telegram.

Все перечисленные боты реализованы на Python/Flask на платформе [Google App Engine Standard Environment](https://cloud.google.com/appengine/docs/standard/).

## Проекты с открытым исходным кодом

- [Навык для Яндекс.Алиса](https://github.com/vb64/bulls_cows) с развертыванием в облачной платформе GoogleAppEngine на примере детской математической игры ["быки и коровы"](https://en.wikipedia.org/wiki/Bulls_and_Cows).
- [Пример](https://github.com/vb64/telegram.voice_echo_bot) использования [Yandex SpeechKit Cloud](https://developer.tech.yandex.ru/) и библиотеки [pyTelegramBotAPI](https://github.com/eternnoir/pyTelegramBotAPI). Вы отправляете голосовое сообщение боту Telegram, запущенному на вашем ПК. Бот в ответ присылает текст этого сообщения.
- [Telegram бот](https://github.com/vb64/Tg2Vk) для публикации сообщений в соцсети ВКонтакте.

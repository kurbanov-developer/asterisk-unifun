
# Запуск Asterisk 18 в Docker

## Описание
Этот репозиторий содержит конфигурационные файлы для запуска Asterisk 18 с помощью Docker образа `andrius/asterisk`.

---

## Требования
- Установлен Docker (https://docs.docker.com/get-docker/)
- Порт 5060 (UDP и TCP) и диапазон портов 10000-10100 UDP свободны для проброса
- Конфигурационные файлы расположены в папке `config/`
- Звуковые файлы для IVR в папке `sounds/`

---

## Конфигурация SIP (`config/sip.conf`)

```ini
[general]
context=ivr
bindport=5060
bindaddr=0.0.0.0
disallow=all
allow=ulaw
allow=alaw
allow=wav
language=ru
localnet=192.168.0.112/255.255.255.0
nat=force_rport,comedia

[kurbanov]
type=friend
secret=1234
host=dynamic
context=ivr
nat=force_rport,comedia
canreinvite=no
qualify=yes
dtmfmode=rfc2833
```

> При необходимости в `localnet` можно указать внутренний IP вашей сети, а также в конфигурацию добавить внешний IP, если он есть.

---

## Команда запуска Docker контейнера

```bash
docker run -d \
  --name asterisk \
  -p 5060:5060/udp \
  -p 5060:5060/tcp \
  -p 10000-10100:10000-10100/udp \
  -v $(pwd)/config/sip.conf:/etc/asterisk/sip.conf \
  -v $(pwd)/config/extensions.conf:/etc/asterisk/extensions.conf \
  -v $(pwd)/config/rtp.conf:/etc/asterisk/rtp.conf \
  -v $(pwd)/config/modules.conf:/etc/asterisk/modules.conf \
  -v $(pwd)/sounds:/var/lib/asterisk/sounds/ \
  andrius/asterisk
```

---

## Проверка

- После запуска контейнера, подключитесь к консоли Asterisk:

```bash
docker exec -it asterisk asterisk -rvvv
```

- Проверьте зарегистрированных SIP-пиров командой:

```
sip show peers
```

- Для теста звонка используйте:

```
channel originate SIP/kurbanov extension 7001@ivr
```

---

## Полезные советы

- Убедитесь, что ваши звуковые файлы находятся в `/var/lib/asterisk/sounds/` внутри контейнера.
- Проверьте, что порты 5060 (UDP/TCP) и диапазон RTP (10000-10100 UDP) проброшены корректно.
- Для доступа с внешних сетей добавьте правильные настройки NAT в `sip.conf`.

---

## Лицензия
Данная инструкция и конфигурации предоставлены "как есть" без гарантий.

---

Если есть вопросы, обращайтесь!

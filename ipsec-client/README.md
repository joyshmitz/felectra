# Образ Docker для клієнта IPsec VPN

ipsec-client — це клієнт VPN, який може допомогти легко налаштувати клієнт IPSec VPN у Docker і *використовується* хостом, керуючи IP-маршрутом за замовчуванням.

Це зображення створено з [налаштування клієнта Linux VPN за допомогою командного рядка](https://github.com/hwdsl2/setup-ipsec-vpn/blob/master/docs/clients.md#configure-linux-vpn-clients-using-the-command-line) і тестується за допомогою [IPsec VPN Server на Docker](https://github.com/oblikonline/docker-ipsec-vpn-server).

Використовуючи "привілейовану" і "хост" мережу Docker, контейнер оновить маршрут за замовчуванням в Linux після успішного запуску. Налаштування маршрутизатора буде відновлено після зупинки Docker.

Натисніть це, щоб розгорнути цей репозиторій у balenaCloud:

[![](https://balena.io/deploy.svg)](https://dashboard.balena-cloud.com/deploy?repoUrl=https://github.com/oblikonline/docker-ipsec-vpn-client)

## Як використовувати це зображення Docker

### Змінні середовища

Це зображення Docker використовує такі змінні, і ним можна легко керувати за допомогою файлу `env`:
```
VPN_SERVER_IP=ваш_vpn_server_public_ip
VPN_PSEC_PSK=ваш_ipsec_pre_shared_key
VPN_USER=ваше_vpn_ім’я користувача
VPN_PASSWORD=ваш_vpn_password)
VERBOSE=true|false
```

### Запустіть клієнт IPSec VPN

Підготуйте файл env `vpn.env` (рекомендований спосіб) або використовуйте змінні середовища безпосередньо для створення контейнера Docker:

```
docker run --rm --name vpn-client --env-file=./vpn.env -d --privileged --net=host fengzhou/ipsec-vpn-client
```

Щоб побачити більше інформації про налагодження, встановіть `VERBOSE=true` у змінній середовища у файлі env.

### Зупиніть IPSec VPN-клієнт

Використання команди `docker stop` може негайно зупинити VPN-клієнт:

```
docker stop vpn-client
```

Правила маршрутизації VPN за замовчуванням будуть видалені після зупинки, а також буде видалено тимчасовий контейнер Docker.

### Вирішення проблем

Використовуйте таку команду, щоб перевірити журнали підключень під час роботи контейнера:
```
docker logs vpn-client
```

Використовуйте таку команду, щоб перевірити, чи створено мережевий інтерфейс `ppp0`:
```
ip a show ppp0
```

Якщо мережевий маршрут не відновлено повністю, скористайтеся такою командою, щоб видалити будь-яке порушене правило маршруту:

```
route del default dev ppp0
```

### Обмеження

* Docker-ipsec-vpn-server і цей клієнт vpn не можуть використовуватися разом на одному хості через конфлікти портів 500/udp, 4500/udp
* Усі існуючі маршрути за замовчуванням будуть перенаправлені на VPN-сервер, і для розділення тунелю знадобиться ручне правило маршруту.

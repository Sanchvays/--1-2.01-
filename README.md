# Практична робота № 1

**Дисципліна:** Основи побудови інформаційних систем та мереж

**Тема:** Спостереження за процесом звернення до вебресурсу. Побудова власної моделі рівнів взаємодії

| | |
|---|---|
| **Прізвище, ім'я** |Сопран Олександр|
| **Група** |F2 - 2.01|
| **Номер варіанта** |26|
| **Домен варіанта** |ethz.ch|
| **Середовище виконання** | Windows |
| **Версія curl** | curl 8.13.0 (Windows) libcurl/8.13.0 Schannel zlib/1.3.1 WinIDN |
| **Дата виконання** |21.09.2026|

---

## Частина A. Збір експериментальних даних

### A.1. Запит із діагностичним виводом

**Команда:**

```
curl -v https://ethz.ch
```

**Вивід:**

```
* Host ethz.ch:443 was resolved.
* IPv6: (none)
* IPv4: 129.132.19.216
*   Trying 129.132.19.216:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to ethz.ch (129.132.19.216) port 443
* using HTTP/1.x
> GET / HTTP/1.1
> Host: ethz.ch
> User-Agent: curl/8.13.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301
< Date: Mon, 21 Sep 2026 06:08:01 GMT
< Server: Varnish
< X-Varnish: 747324401
< location: https://ethz.ch/de.html
< Content-Length: 0
< Connection: keep-alive
<
* Connection #0 to host ethz.ch left intact
```

---

### A.2. Запит без захисту з'єднання

**Команда:**

```
curl -v http://ethz.ch
```

**Вивід:**

```
* Host ethz.ch:80 was resolved.
* IPv6: (none)
* IPv4: 129.132.19.216
*   Trying 129.132.19.216:80...
* Connected to ethz.ch (129.132.19.216) port 80
* using HTTP/1.x
> GET / HTTP/1.1
> Host: ethz.ch
> User-Agent: curl/8.13.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301
< Date: Mon, 21 Sep 2026 06:09:14 GMT
< Server: Varnish
< X-Varnish: 747362900
< location: https://ethz.ch/de.html
< Content-Length: 0
< Connection: keep-alive
<
* Connection #0 to host ethz.ch left intact
```

---

### A.3. Запит до служби доменних імен

*Windows: `Resolve-DnsName ВАШ_ДОМЕН`*

**Команда (перше виконання):**

```
Resolve-DnsName ethz.ch
```

**Вивід:**

```
Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
ethz.ch                                        AAAA   3114  Answer     2001:67c:10ec:254::216
ethz.ch                                        A      291   Answer     129.132.19.216
```

**Команда (повторне виконання через 5–7 хвилин):**

```
Resolve-DnsName ethz.ch
```

**Вивід:**

```
Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
ethz.ch                                        AAAA   3600  Answer     2001:67c:10ec:254::216
ethz.ch                                        A      300   Answer     129.132.19.216
```

**Зафіксовані значення:**

| Параметр | Перше виконання | Повторне виконання |
|---|---|---|
| Час виконання (год:хв) |8:50|9:00|
| IP-адреса |2001:67c:10ec:254::216, 129.132.19.216|2001:67c:10ec:254::216, 129.132.19.216|
| Значення TTL |3114, 291|3600, 300|

> Якщо друге значення TTL виявилося більшим за перше — це нормально: кеш резолвера встиг оновитися. Зафіксуйте як є.

---

### A.4. Контрольний ресурс

**Команда:**

```
curl -v https://google.com
```

**Вивід:**

```
* Host google.com:443 was resolved.
* IPv6: (none)
* IPv4: 142.251.98.100, 142.251.98.113, 142.251.98.139, 142.251.98.101, 142.251.98.138, 142.251.98.102
*   Trying 142.251.98.100:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to google.com (142.251.98.100) port 443
* using HTTP/1.x
> GET / HTTP/1.1
> Host: google.com
> User-Agent: curl/8.13.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< Location: https://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-tkBG5R2EkeXdlgovG1OtgA' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
< Date: Mon, 21 Sep 2026 06:13:46 GMT
< Expires: Wed, 21 Oct 2026 06:13:46 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 220
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000
<
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com left intact
```

---

### A.5. Ресурси з некоректною конфігурацією сертифіката

**Випадок 1**

```
curl -v https://expired.badssl.com
```

```
* Host expired.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
* closing connection #0
curl: (35) schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
```

**Випадок 2**

```
curl -v https://wrong.host.badssl.com
```

```
* Host wrong.host.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
* closing connection #0
curl: (60) schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.

```

**Випадок 3**

```
curl -v https://self-signed.badssl.com
```

```
* Host self-signed.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
* closing connection #0
curl: (60) schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
```

> Якщо використано альтернативний спосіб із параметром `--resolve` — зазначити це та навести фактичну команду.

---

## Частина B. Власна модель рівнів

**Кількість виділених груп:** ___

| № | Назва групи (власне формулювання) | Рядки виводу, віднесені до групи | Обґрунтування |
|---|---|---|---|
| 1 |Пошук IP-адреси сайту (DNS)|Host ethz.ch:80 was resolved.`<br>`* IPv6: (none)`<br>`* IPv4: 129.132.19.216|Комп'ютер знаходить IP-адрес 129.132.19.216|
| 2 |Встановлення TCP|* Trying 129.132.19.216:80..., Connected to ethz.ch (129.132.19.216) port 80, using HTTP/1.x|Підключення до сервера через 80-й порт|
| 3 |Відправка HTTP-запиту|GET / HTTP/1.1, Host: ethz.ch, User-Agent: curl/8.13.0, Accept: */*,  Request completely sent off|Запит головної сторінки|
| 4 |Отримання HTTP|HTTP/1.1 301,  Date: Mon, 21 Sep 2026 09:49:55 GMT, Server: Varnish,  X-Varnish: 751981137,  location: https://ethz.ch/de.html, Content-Length: 0, Connection: keep-alive|Відповідь сервера HTTP-301, Перенаправлення на безпечну HTTPS|

*Групи впорядковано від найближчої до користувача (№ 1) до найближчої до апаратного забезпечення. Зайві рядки вилучити, за потреби — додати.*

**Рядки, які не вдалося віднести до жодної групи:**

| Рядок виводу | Причина утруднення |
|---|---|
| | |
| | |
| | |

---

## Контрольні питання

**1. Скільки рядків діагностичного виводу передує отриманню даних сторінки (завдання A.1)?**

> 15

**2. Які рядки наявні у виводі A.1 і відсутні у виводі A.2? Чим це зумовлено?**

> schannel: disabled automatic use of client certificate, ALPN: curl offers http/1.1, ALPN: server accepted http/1.1. А1 - використовує HTTPS A2 - HTTP

**3. Звідки у виводі з'явилося значення `443`, якщо його не було вказано в адресі?**

> Це порт за замовчуванням для HTTPS

**4. Як змінилося значення TTL між двома запитами (A.3)? Що означає це число?**

> Значення зросло з 3114 до 3600 та з 291 до 300. Це час який інформація буде зберігатися у памяті

**5. Чим відрізняються між собою три причини помилок із завдання A.5? Сформулювати кожну однією фразою.**

| Випадок | Причина недовіри |
|---|---|
| `expired` |Термін дії сертифіката сервера закінчився|
| `wrong.host` |Головне кінцеве ім'я неправильне|
| `self-signed` |Ланцюжок сертифікатів випущений центром сертифікації, який не має довіри|

**6. Три рядки з власних виводів, про які не йшлося на лекції 1:**

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 |X-Varnish: 751981137|А1|
| 2 |schannel: disabled automatic use of client certificate|А1|
| 3 |ALPN: server accepted http/1.1|А1|

*Пояснення до цих рядків не потрібне.*

---

## Висновки

*150–300 слів. Спиратися на власні спостереження, а не на матеріал лекції.*

**D.1. Що виявилося неочевидним або несподіваним**

*Назвати конкретно, з посиланням на рядок виводу.*

> Коли я вводив Resolve-DnsName ethz.ch (А.3), cmd вивів помилку " "Resolve-DnsName" не является внутренней или внешней
командой, исполняемой программой или пакетным файлом. ". Після цього я звернувся до штучного інтелекту з цією помилкою. Виявилось це потрібно було писати у PowerShell

**D.2. Чому саме така кількість груп у частині B**

*На якій підставі ухвалено рішення. Що змусило б його змінити.*

> У частині Б виділено 4 групи які поділенні на послідовні етапи. 1) Пошук IP адресу 2) Встановлення TCP З'єднання 3) Відправка HTTP запиту 4) Отримання HTTP

**D.3. Питання, яке залишилося без відповіді**

> 

---

## Використання штучного інтелекту

*Розділ обов'язковий. Заповнюється незалежно від того, чи використовувався ШІ. Детальні вимоги — у документі «Політика використання технологій штучного інтелекту».*

**Факт використання:** використано 

**Установлений рівень для цієї роботи:** Р3 — ШІ як співвиконавець

**Фактичний рівень використання:** P3

### Використані системи

| Система | Версія або модель | Період використання |
|Claude|sonnet 5|21.09.2026|

### Промпти

*Наводити дослівно, у тому вигляді, у якому запит було надано системі. Переказ не приймається.*

| № | Розділ роботи | Текст промпта |
|---|---|---|
| 1 |Початок|"Как посмотреть curl версию виндовс"|
| 2 |А.3|"Ошибка в cmd при вводе Resolve-DnsName ethz.ch"|
| 3 |Контрольні запит.|Скільки рядків діагностичного виводу передує отриманню даних сторінки. Чому стільки?|

### Дії з отриманим результатом

| № промпта | Що перевірено | Що змінено | Що відхилено і чому |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |

### Підтвердження

Підтверджую, що всі наведені в цьому звіті виводи команд отримано мною особисто внаслідок фактичного виконання відповідних дій, а відомості цього розділу є повними та достовірними.

> Виводи `curl`, `dig` та інші артефакти не можуть бути згенеровані. Це стосується будь-якого рівня використання ШІ.

---

## Примітки виконавця

*(необов'язковий розділ: що не спрацювало, які команди довелося змінити, які виникли труднощі)*

> 

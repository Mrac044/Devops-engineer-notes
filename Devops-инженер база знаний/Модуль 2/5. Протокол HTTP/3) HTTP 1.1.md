Предыдущая версия требует наличия только _request line_. Отсюда можно сделать вывод, что понятие домена (доменного имени) при использовании HTTP 1.0 неважно.

HTTP 1.1 ввел такое понятие как виртуальные хосты. Кроме request line стал обязательным еще и заголовок, который называется #HTTP-запрос host. Он определяет, какой именно домен должен быть возвращен с этого IP адреса.

```http
HEAD /http-protocol/example HTTP/1.1
host: http.hexlet.app
```

>Виртуальные хосты являются неотъемлемым атрибутом HTTP 1.1. Их нельзя игнорировать.

Сейчас мы можем сделать GET-запрос с использованием протокола версии 1.1 и посмотреть, что он нам вернет:

```http
telnet http.hexlet.app 80

GET /http-protocol/example HTTP/1.1
host: http.hexlet.app

HTTP/1.1 200 OK
Date: Wed, 02 Oct 2024 15:10:36 GMT
Content-Type: text/html; charset=ISO-8859-1
Transfer-Encoding: chunked
Connection: keep-alive
Accept-Ranges: none
Cache-Control: private, max-age=0
Expires: -1
P3p: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Set-Cookie: 1P_JAR=2020-01-18-09; expires=Mon, 17-Feb-2020 09:24:50 GMT; path=/; domain=.hexlet.app; Secure
Set-Cookie: NID=196=wsHLMAMfnAaSyF7zduokI8TJeE5UoIKPHYC58HYH93VMnev9Nc2bAjhRdzoc4UhmuOd7ZVCorDnzGDe51yPefsRMeVyOFnYdHYYgQNqI8A1dYuk4pDK4OJurQgL4lX8kiNGSNi_kkUESFQ-MqLCB_YspxA9JRejhZdkTRtGyHNk; expires=Sun, 19-Jul-2020 09:24:50 GMT; path=/; domain=.hexlet.app; HttpOnly
Vary: Accept-Encoding
X-Frame-Options: SAMEORIGIN
X-Xss-Protection: 0
CF-Cache-Status: DYNAMIC
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v4?s=2QsnLl8DD1DODoL9WfVTr4zKcTLjXiETV%2Ft2Gx%2Bn2lwO%2BWgPNetNdo1hgGVnYp%2B%2Bc5ZS3zRGJ7imR1Sw7oaZcjwD49sMGI0S8R9xpdnVxeFv6LnIN7oDB6uQv2ZkIgbirwA%3D"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Speculation-Rules: "/cdn-cgi/speculation"
Server: cloudflare
CF-RAY: 8cc59ec47f8c36c4-YYZ

Done!
```

>Здесь есть еще одна интересная деталь, которая введена в HTTP 1.1. После выполнения запроса мы не выпали из telnet и не оказались в bash.

Сделаем HEAD-запрос на тот же самый домен:

```http
HEAD /http-protocol/example HTTP/1.1
host: http.hexlet.app

HTTP/1.1 200 OK
Date: Wed, 02 Oct 2024 15:12:51 GMT
Content-Type: text/html; charset=ISO-8859-1
Connection: keep-alive
Accept-Ranges: none
Cache-Control: private, max-age=0
Expires: -1
P3p: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Set-Cookie: 1P_JAR=2020-01-18-09; expires=Mon, 17-Feb-2020 09:24:50 GMT; path=/; domain=.hexlet.app; Secure
Set-Cookie: NID=196=wsHLMAMfnAaSyF7zduokI8TJeE5UoIKPHYC58HYH93VMnev9Nc2bAjhRdzoc4UhmuOd7ZVCorDnzGDe51yPefsRMeVyOFnYdHYYgQNqI8A1dYuk4pDK4OJurQgL4lX8kiNGSNi_kkUESFQ-MqLCB_YspxA9JRejhZdkTRtGyHNk; expires=Sun, 19-Jul-2020 09:24:50 GMT; path=/; domain=.hexlet.app; HttpOnly
Vary: Accept-Encoding
X-Frame-Options: SAMEORIGIN
X-Xss-Protection: 0
cf-cache-status: DYNAMIC
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v4?s=CfQMI5s%2BjZDgxqUZ757F4zVLf8TIt7jbqtnIpFHtyMQ6WEkSZk0ErK%2Fao70%2FlELOHjnMP6fl28R30tK1VkX53xvd4bkKHkBCBXZ07c8EwiVOYUjrtSpP6nbQeOOFIufDDP4%3D"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Speculation-Rules: "/cdn-cgi/speculation"
Server: cloudflare
CF-RAY: 8cc5a20e6aed36c4-YYZ
```

Мы снова получили ответ, но уже без body (Слово "Done!"), поскольку использовали HEAD, а не GET-запрос:
![[Pasted image 20260223235740.png]]

HTTP 1.1 вводит еще одно понятие по умолчанию, которое называется _keep-alive_. _Keep-alive_ означает, что соединение TCP, по которому ходит HTTP, не закрывается.

Основная цель введения этой возможности в том, чтобы сократить использование ресурсов, уменьшить нагрузку на процессор, открывать меньше TCP-соединений (установка каждого TCP-соединения занимает время), уменьшить время ожидания (latency).

_Keep-alive_ позволяет открывать и использовать одно соединение, которое не будет закрыто до тех пор, пока это не будет указано явно, либо не произойдет таймаут. Таймаут зависит от того, какой браузер и какой веб-сервер используется.

Мы также можем указать, что хотим закрыть соединение. Для этого после установки соединения и передачи стандартных заголовков нужно передать еще один заголовок. Он называется `connection: close`.

## Дополнительные материалы

- [Стандарт HTTP 1.1](https://www.ietf.org/rfc/rfc2616.txt)
    
- [Википедия / Постоянное HTTP-соединение](https://ru.wikipedia.org/wiki/%D0%9F%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%BD%D0%BE%D0%B5_HTTP-%D1%81%D0%BE%D0%B5%D0%B4%D0%B8%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5)
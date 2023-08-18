# Root-Me Challenges: Web - Server

---

## Task 1: HTML - Source code (5 points)

Переходим на сайт:

![ScreenShot](screenshots/1.png)

Нам нужен пароль для входа, который мы не знаем, но находим его в исходном коде страницы

![ScreenShot](screenshots/2.png)

---

## Task 2: Weak password (10 points)

Переходим на страницу с таском, где нам предлагают пройти аутентификацию, чтобы попасть на страницу. Применяем базовые логин/пароль (admin:admin) для входа и попадаем на страницу:

![ScreenShot](screenshots/3.png)

Можно было сделать и через **hydra** (hydra -L userList.txt -P passwordsList.txt 212.129.38.224 http-head /web-serveur/ch3/), а также другие инструменты.

---

## Task 3: PHP - Command Injection (10 points)

Переходим по предоставленной ссылке:

![ScreenShot](screenshots/4.png)

Сервис предоставляет возможность проверки доступности какого-нибудь ресурса через утилиту ping - нужно ввести в поле ввода только адрес. Но что будет, если ввести IP-адрес и через `;` ввести вторую команду?

![ScreenShot](screenshots/5.png)

Как видим, вторая команда также исполняется. Получим исходный код файла **index.php** в base64 и декодируем его:

![ScreenShot](screenshots/6.png)

```html
<html>
<head>
<title>Ping Service</title>
</head>
<body>
<form method="POST" action="index.php">
        <input type="text" name="ip" placeholder="127.0.0.1">
        <input type="submit">
</form>
<pre>
<?php 
$flag = "".file_get_contents(".passwd")."";
if(isset($_POST["ip"]) && !empty($_POST["ip"])){
        $response = shell_exec("timeout -k 5 5 bash -c 'ping -c 3 ".$_POST["ip"]."'");
        echo $response;
}
?>
</pre>
</body>
</html>
```

Узнаем, что флаг находится в `.passwd` и достаем его:

![ScreenShot](screenshots/7.png)

Можно было решить и по-другому:

![ScreenShot](screenshots/8.png)

![ScreenShot](screenshots/9.png)

---

## Task 4: HTTP - Directory indexing (15 points)

При переходе на сайт перед нами красуется пустая страница:

![ScreenShot](screenshots/10.png)

Заглядываем в исходный код и находим **/admin/pass.html**:

![ScreenShot](screenshots/11.png)

```sh
http://challenge01.root-me.org/web-serveur/ch4/admin/pass.html
```

Переходим по найденном пути:

![ScreenShot](screenshots/12.png)

Очередная наеб**. Поднимемся выше:

```sh
http://challenge01.root-me.org/web-serveur/ch4/admin/
```

Находим еще одну директорию **/backup**:

![ScreenShot](screenshots/13.png)

Внутри нее находится файл **admin.txt**, где расположен ответ на таск:

```sh
http://challenge01.root-me.org/web-serveur/ch4/admin/backup/admin.txt
```

![ScreenShot](screenshots/14.png)

---

Task 5: HTTP - User-agent (10 points)

При переходе на сайт нам сообщают о том, что мы используем неправильный **User-Agent**:

![ScreenShot](screenshots/15.png)

При помощи утилиты **curl** и параметра **-A** можно легко поменять требуемый параметр:

![ScreenShot](screenshots/16.png)

Ответ успешно получен

---

Task 6: HTTP - IP restriction bypass (10 points)

От нас требуется авторизоваться на сайте относительно локального IP-адреса. Гуглим HTTP-заголовки, который могут манипулировать IP-адресами:

![ScreenShot](screenshots/17.png)

Опытным путем узнаем, что нам необходим заголовок **X-Forwarded-For**:

```sh
C:\Users\Ivan->curl -H "X-Forwarded-For:127.0.0.1" http://challenge01.root-me.org/web-serveur/ch68/
<!DOCTYPE html>
<html>
<head>
        <title>Secured Intranet</title>
</head>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
                        <span>Your IP <strong>127.0.0.1</strong> do not belong to the LAN.</span>
                <h1>Intranet</h1>
                <form method="post">
                        <p>
                                <label for="login">Login:</label>
                                <input type="text" name="login">
                        </p>
                        <p>
                                <label for="pass">Password:</label>
                                <input type="text" name="mdp">
                        </p>
                        <p>
                                <input type="submit" value="login">
                        </p>
                        <p>
                                <small>You should authenticate because you're not on the LAN.</small>
                        </p>
                </form>
        </body>
</html>
```

Как видим, IP изменился, что видно в ответе от сервера. Далее вспоминаем (или смотрим стандарт RFC 1918) диапазоны локальных IP-адресов. Первое, что приходит на ум 192.168.0.0/16

```sh
RFC 1918

10.0.0.0        -   10.255.255.255  (10/8 prefix)
172.16.0.0      -   172.31.255.255  (172.16/12 prefix)
192.168.0.0     -   192.168.255.255 (192.168/16 prefix)
```

Пробуем подставить IP-адрес из вышеупонянутого диапазона:

![ScreenShot](screenshots/18.png)

---

Task 7: HTTP - Open redirect (10 points)

При переходе на страницу видим три кнопки, которые под собой скрывают ссылки на указанные ресурсы:

![ScreenShot](screenshots/19.png)

Сами ссылки можно посмотреть через исходный код страницы:

![ScreenShot](screenshots/20.png)

Например, разберем редирект на facebook:

```html
<a href="?url=https://facebook.com&h=a023cfbf5f1c39bdf8407f28b60cd134">facebook</a>
```

Здесь мы видим, что в параметр **url** передается сама ссылка на ресурс, а также у нас есть и второй параметр **h**, который является хэшем, а хэш (формат MD5) представляет собой все ту же самую ссылку:

![ScreenShot](screenshots/21.png)

Формируем новую ссылку (редирект на google.com):

```sh
http://challenge01.root-me.org/web-serveur/ch52/?url=https://google.com&h=99999ebcfdb78df077ad2727fd00969f
```

И переходим по ней. Ловим флаг)

![ScreenShot](screenshots/22.png)

---

Task 8: Backup file (15 points)

Переходим на сайт и видим перед собой форму авторизации:

![ScreenShot](screenshots/23.png)

Как мы уже выяснили, исходная страница - **index.php**. Тогда остается узнать расширение, которое обозначает backup. Сайт: **https://kb.iu.edu/d/affo**

Применяем:

```sh
http://challenge01.root-me.org/web-serveur/ch11/index.php~
```

Скачался файл! Открываем его:

![ScreenShot](screenshots/24.png)

Собственно, применяем искомые логин и пароль:

![ScreenShot](screenshots/25.png)

---

Task 9: HTTP - Headers (15 points)

При переходе на сайт нас просят обратить внимание на то, что ответ от сервера имеет не только содержимое:

![ScreenShot](screenshots/26.png)

Смотрим, какие заголовки нам приходят от сервера при нашем запросе:

![ScreenShot](screenshots/27.png)

Находим **Header-RootMe-Admin: none**. Отправляем запрос с данным заголовком в значении **true**:

![ScreenShot](screenshots/28.png)

В итоге нам приходит ответ с паролем

---

Task 10: HTTP - POST (15 points)

В очередной раз переходим на сайт и видим перед собой некую игру, где необходимо выбить число >999999:

![ScreenShot](screenshots/29.png)

![ScreenShot](screenshots/30.png)

Посмотрим, в чем же состоит подвох:

![ScreenShot](screenshots/31.png)

Основной проблемой тут является малый множитель. Изменим его прямо в исходном коде - припишем немного нулей:

![ScreenShot](screenshots/32.png)

А теперь, нажмем на **Give a try!**:

![ScreenShot](screenshots/33.png)

Вот мы и выиграли!

---

Task 11: HTTP - Improper redirect (15 points)

В PHP код http_redirect() или header('Location: ...') не выполняет остановку выполнения кода. В результате вместе со статусом 301 или 302 клиенту отправляется полное содержимое HTML страницы. Для захвата содержимого можно использовать различные инструменты, например, Burp Suite или curl.

Переходим на сайт:

![ScreenShot](screenshots/34.png)

В таске сказано, что необходимо найти страницу **index** (Get access to index.). Посмотрим, существует ли страница index.php. Чтобы это проверить, необходимо вместо `/login.php?redirect` подставить `/index.php`:

![ScreenShot](screenshots/35.png)

Как видим, **302 Found**, значит, страница существует. Попробуем получить содержимое через curl - там редирект страбатывать не будет:

![ScreenShot](screenshots/36.png)

Собственно, страницу мы перехватили и получили ответ

---

Task 12: HTTP - Verb tampering (15 points)

Уязвимость HTTP Verb Tampering – ошибка настройки контроля доступа для методов протокола HTTP. Тупо аутентификация включена не для всех разрешённых на web-сервере HTTP методов.

При переходе на страницу система просит пройти авторизацию:

![ScreenShot](screenshots/37.png)

При обычном переходе на страницу, мы по факту реализуем параметр GET:

![ScreenShot](screenshots/38.png)

Здесь стоит пароль, но если сменить HTTP-метод:

![ScreenShot](screenshots/39.png)

Вот мы и получили доступ к странице, используя другой HTTP-метод.

Некоторые HTTP-методы:

1. GET — используется для получения информации от сервера по заданному URI.
2. HEAD — тот же GET, но сервер посылает только заголовки и статусную строку без тела HTTP сообщения.
3. POST — используется для отправки данных на сервер.
4. PUT — используется для загрузки содержимого запроса на заданный URI.
5. DELETE — удаляет указанный в URI ресурс.
6. CONNECT — преобразует существующее соединение в тоннель.
7. OPTIONS — используется для получения параметров текущего HTTP соединения.
8. TRACE — создает петлю, благодаря которой клиент может увидеть, что происходит с сообщением на всех узлах передачи.

Бывают и другие, например: PATCH, PROPFIND, PROPPATCH, MKCOL, COPY, MOVE, LOCK, UNLOCK, SEARCH, POLL, NOTIFY и прочие.

---

Task 13: Install files (15 points)

В таске говорится о некой **phpBB** - `https://github.com/phpbb/phpbb`. Также упоминается **установка**. Постепенно находим данный путь, например, при помощи фаззера, где и лежит флаг:

```sh
http://challenge01.root-me.org/web-serveur/ch6/phpbb/install/install.php
```

![ScreenShot](screenshots/40.png)

---

Task 14: 

---

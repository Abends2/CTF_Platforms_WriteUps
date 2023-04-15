# CTFlearn: Forensics

### Taking LS (+10 points)
Скачиваем архив и открываем его. Внутри находится папка "The Flag", в которой лежат следующие файлы:

![ScreenShot](screenshots/1.png)

В папке ".ThePassword" лежит пароль от PDF-файла:

![ScreenShot](screenshots/2.png)

Открываем PDF при помощи найденного пароля:

![ScreenShot](screenshots/3.png)


### Rubber Duck (+10 points)
Утилита **file** способна показывать комментарий файла:

![ScreenShot](screenshots/4.png)


### I'm a dump (+10 points)
Для начала проверим файл при помощи утилиты **file**:

![ScreenShot](screenshots/5.png)

Видим, что это исполняемый файл - естественно не запускаем его. Смотрим его через **strings**:

![ScreenShot](screenshots/6.png)

> Note: Из флага надо убрать лишние буквы H


### WOW.... So Meta (+20 points)
Флаг находится в разделе "Camera Serial Number". Просмотр осуществляется при помощи улититы **exiftool**:

![ScreenShot](screenshots/7.png)


### Exif (+20 points)
Флаг находится в разделе "Owner Name". Просмотр осуществляется при помощи улититы **exiftool**:

![ScreenShot](screenshots/8.png)


### Snowboard (+20 points)
Смотрим файл через **strings**:

![ScreenShot](screenshots/9.png)

Первый флаг - обман. Под ним что-то закодированное в base64, судя по '=='. Декодируем:

![ScreenShot](screenshots/10.png)

### PikesPeak (+20 points)
Тут на внимательность (формат флага):

![ScreenShot](screenshots/11.png)


### Tux! (+20 points)
Через **exiftool** находим закодированный комментарий. Сразу его декодируем:

![ScreenShot](screenshots/12.png)

С помощью **binwalk** пытаем извлечь спрятанные данные:

![ScreenShot](screenshots/13.png)

Открываем найденный архив. Внутри файл *flag*, который пустой. Архив защищен паролем, но пароль мы уже нашли:

![ScreenShot](screenshots/14.png)



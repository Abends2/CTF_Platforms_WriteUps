# PicoCTF (PicoCym) Forensics

---

## Task 1: Wireshark doo dooo do doo... (50 points)

Description:

> Can you find the flag? **shark1.pcapng**

Открываем файл и сразу замечаем при пролистывании множество HTTP-объектов:

![ScreenShot](screenshots/1.png)

Просматриванием список HTTP-объектов через "Экспорт объектов" в Wireshark:

![ScreenShot](screenshots/2.png)

Среди всего однообразия находим пакет под номером 827:

![ScreenShot](screenshots/3.png)

Смотрим более детально данный пакет

![ScreenShot](screenshots/4.png)

Находим нечто похожее на флаг. Расшифруем:

![ScreenShot](screenshots/5.png)

Флаг: picoCTF{p33kab00_1_s33_u_deadbeef}

---

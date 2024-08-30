# Обход замедления YouTube + выборочный проброс сайтов через AmneziaVPN на роутере Keenetic

**Эта статья является компиляцией статей и решений, ссылки на которые я привел в конце статьи, а также моих опытов.**

## Оглавление

## Вводная информация

В этой статье я опишу три возможных на данный момент решения по обходу блокировок и замедлений на роутере Keenetic:

1) Обход замедлений и блокировок через **DPI** (Dynamic Packet Inspection) при помощи [Zapret](https://github.com/bol-van/zapret).

2) Обход блокировок и замедлений сайтов с помощью [AmneziaVPN](https://amnezia.org/) как для всех сайтов, так и для конкретных, не затрагивая другие ресурсы.

3) Комбинация двух решений, избавляющая от необходимости смотреть рекламу на YouTube и разгружающая AmneziaVPN от тонны трафика с YouTube. **Рекомендуемое решение.**

## Минимальные требования к роутеру

- Для **Zapret**: Оперативная память 128мб или более, а так же наличие USB порта с поддержкой USB-накопителей, а не только модемов (Например, KN-1210, KN-1211 не поддерживают USB-накопители).

     > *Для владельцев моделей: Giga (KN-1010/1011), Ultra (KN-1810/1811), Viva (KN-1910/1912), DSL (KN-2010), Launcher DSL (KN-2012), Duo (KN-2110), Skipper DSL (KN-2112), Hero 4G (KN-2310), Hero 4G+ (KN-2311), Giga SE (KN-2410), Ultra SE (KN-2510), Giant (KN-2610), Peak (KN-2710), Skipper 4G (KN-2910), Hopper DSL (KN-3610), Hopper (KN-3810) возможна установка Entware на встроенную память роутера, для них поддержка USB-накопителя не требуется. Установка описана [здесь](https://help.keenetic.com/hc/ru/articles/360021888880-Установка-OPKG-Entware-на-встроенную-память-роутера)*

- Для **AmneziaVPN**: Ваш роутер должен быть в [списке поддерживаемых моделей](https://forum.keenetic.com/topic/17772-%D0%B6%D1%83%D1%80%D0%BD%D0%B0%D0%BB-%D0%B8%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D0%B9-42/)

- Для комбинированного метода роутер должен соответствовать требованиям к **Zapret** и **AmneziaVPN**

## Zapret

> **Важно!** Есть проблемы, а именно, [утечка памяти в модуле ядра Netfilter](https://github.com/bol-van/zapret/issues/311) и [случайные перезагрузки роутера](https://github.com/bol-van/zapret/issues/306), разработчики Keenetic в курсе о [проблеме](https://forum.keenetic.com/topic/18656-утечка-памяти-kn-1811-417/?do=findComment&comment=187392) и решат ее в следующем релизе 4.2 beta 3. Есть временное решение, а именно, отключение [сетевого ускорителя](https://help.keenetic.com/hc/ru/articles/214470905-Сетевой-ускоритель) в компонентах.

- [Следуем данной инструкции, предварительно прочитав пункты ниже](https://github.com/nikrays/Zapret-on-Keenetic/blob/master/readme.md).
- В пункте 14 выбираем **один** вариант, если он не заработает, позже его можно изменить.
- В пункте 15 выбираем **два** варианта — один из трех первых и последний. В случае если выбрали комбинированный метод обхода, то первый и последний.

    > **Важно!** В моем случае команда из пункта 16 не помогла, обход заработал только после перезагрузки роутера.
    > **Важно!** Загрузка Zapret выполняется длительное время, перед тестом после перезагрузки советую подождать минут пять.

- Проверяем что разблокированные сайты работают. В случае если сайт все еще заблокирован/замедлен, то исполняем другую команду из пункта 14 на роутере, после чего исполняем команду `/opt/zapret/init.d/sysv/zapret restart`, после чего ждем пару минут и снова проверяем.

- В случае если после установки пропал интернет выполняем на роутере команду `/opt/zapret/init.d/sysv/zapret stop`, исполняем другую команду из пункта 14 на роутере, после чего исполняем команду `/opt/zapret/init.d/sysv/zapret start`, после чего ждем пару минут и снова проверяем.

## AmneziaVPN

1) Обновите прошивку роутера согласно [инструкции](https://help.keenetic.com/hc/ru/articles/4402712117138-Установка-тестовой-сборки-KeeneticOS)
2) Настраиваем DNS по [инструкции](https://help.keenetic.com/hc/ru/articles/360007687159-Прокси-серверы-DNS-over-TLS-и-DNS-over-HTTPS-для-шифрования-DNS-запросов), если еще не сделали этого при настройке **Zapret**
3) Проверьте что DNS установлены для любого интерфейса
4) На роутере перейдите в настройки вашего интернет-соединения (обычно **Кабель Ethernet**)
5) В группе **Параметры IPv4** установите галочку **Игнорировать DNSv4 интернет-провайдера**
6) В группе **Параметры IPv6** в выпадающем списка **Настройка IPv6** выберите **Не используется** (Или добавьте защищенные DNS для IPv6 и проигнорируйте DNS провайдера).
7) Сохраните изменения
8) Для настройки защищенных туннелей по протоколу **AmneziaVPN** на роутере Keenetic нужно установить компонент системы **WireGuard VPN**. Сделать это можно в веб-конфигураторе на странице **Общие настройки** в разделе **Обновления и компоненты**, нажав на **Изменить набор компонентов**.
![WireGuard](https://help.keenetic.com/hc/article_attachments/360006616999)
После чего нажмите кнопку **Обновить KeeneticOS**, роутер перезагрузится.
9) Получаем файл конфигурации **AmneziaVPN** в телеграм-боте [@blacktemple_space_bot](https://t.me/blacktemple_space_bot):
    - Приобретаем подписку, если еще не подписались
    - Заходим в меню **🧧Мини кабинет**. - раздел **📺 TV, Роутер**
    ![Кабинет](https://telegra.ph/file/c28d61478c4fb3ff4ad6a.png)
    - Скачайте файл `config.conf`
    ![config](https://telegra.ph/file/c7f22b9f88c2dfbf2c86e.png)
10) Возвращаемся к веб-интерфейсу роутера. Перейдите в раздел **Интернет** > **Другие подключения** - здесь должен появиться установленный компонент WireGuard.
![WireGuard настройка](https://help.keenetic.com/hc/article_attachments/360006601060)
11) Выберите вариант **Загрузить из файла** и загрузите файл конфигурации. (Может занять несколько минут, дождитесь появления подключения в списке)
12) Отредактируйте созданное соединение.
    - Название задайте по своему усмотрению или оставьте как есть.
    - Удалите DNS 1 и 2.
    - Задайте **Имя пира** (например, **Peer**)
13) В браузере перейдите по ссылке <http://192.168.1.1/webcli/parse> (Если у вашего роутера другой IP, используйте его)
![CLI](https://telegra.ph/file/811927fdf040358bd8750.png)
14) В телеграм-боте выделите и скопируйте указанную команду после двоеточия
![Команда](https://telegra.ph/file/41b4ad6cd4d29e892f529.png)
15) Вставьте ее в поле Команда в браузере
![Вставлено](https://telegra.ph/file/21eb5150810ffb575626b.png)
16) Нажмите Отправить запрос.
    В конце должны получить вот такое сообщение:
    ![Сообщение](https://telegra.ph/file/9cf1bc6ff8b382c30488a.png)
    **Важно!** В некоторых случаях, при вводе команды может возникнуть ошибка:

    ```json
    "prompt": "(config)", "status": [ { "status": "error", "code": "7405602", "ident": "Command::Base", "source": "name", "message": "argument parse error."
    ```

    - Лечится вводом команды: `show interface | grep interface-name`
    - Далее найдите имя вашего vpn-интерфейса и замените им имя интерфейса в команде.
17) После этого в поле **Команда** введите команду: `system configuration save`
18) Вернитесь в раздел: **Интернет** > **Другие подключения** и активируйте соединение. После подключения, должен появиться зеленый индикатор в Peer если все сделали правильно.
![Соединение](https://telegra.ph/file/8a0a45c3f98d6d166cbd9.png)
19) Перейдите в раздел **Сетевые правила** > **Маршрутизация**
20) Нажмите **Добавить маршрут**, выберите **Маршрут по умолчанию** и интерфейс вашего провайдера и сохраните.
![Маршрут](https://telegra.ph/file/f06961102f5720df07b44.png)
21) Далее нам необходимо добавить маршруты для обхода блокировок:
    - В случае если **Zapret** не применяется, скачиваем подходящий вам **bat** файл [отсюда](https://rockblack.su/vpn/dopolnitelno/diapazon-ip-adresov) (внизу страницы)
    - Если применяем **Zapret** - скачиваем **bat** файл [отсюда](/no_youtube_insta_meta_torrent_0.0.7.bat) (Это `Youtube + TV + insta+ torrent+ meta + m.youtube_0.0.7` из источника выше, из которого убраны адреса Google и YouTube)
    - Либо собираем свой список в файл с расширением **bat**:

    ```bat
    @REM Flibusta
    route ADD 179.43.150.83 MASK 255.255.255.255 0.0.0.0
    @REM LinkedIn
    route ADD 152.199.21.118 MASK 255.255.255.255 0.0.0.0
    route ADD 150.171.22.12 MASK 255.255.255.255 0.0.0.0
    route ADD 13.107.42.14 MASK 255.255.255.255 0.0.0.0
    ```

22) Нажмите **Загрузить из файла** и загрузите адреса заблокированных ресурсов. **Внимание! Обязательно выбрать интерфейс VPN с названием config (либо ваше название, в случае если вы его переименовали)**

## Комбинированное решение

Выполняйте инструкции для **Zapret** и **AmneziaWG** в таком порядке: сначала **Zapret**, потом **AmneziaWG**. В самих инструкциях указано что делать для комбинированного решения.

## Источники и полезные ссылки

- [Подробная обновляемая инструкция настройки репозитория Zapret от bol-van на Keenetic](https://github.com/nikrays/Zapret-on-Keenetic/tree/master)
- [Автономное, без задействования сторонних серверов, средство противодействия DPI Zapret](https://github.com/bol-van/zapret)
- [Настройка Amnezia WireGuard на роутере с KeeneticOS. 🧧BLACKTEMPLE.SPACE](https://telegra.ph/Nastrojka-Amnezia-WireGuard-na-routere-s-KeeneticOS-BLACKTEMPLESPACE-08-19)
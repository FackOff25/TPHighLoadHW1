# Яндекс.Такси

## Содержание
- [1. Тема и целевая аудитория](#11-тема)
    - [1.1 Тема](#11-тема)
    - [1.2 Целевая аудитория](#12-целевая-аудитория)
- [2. Расчет нагрузки](#2-расчет-нагрузки)
    - [2.1 Расчёт запросов](#21-расчёт-количества-запросов)
    - [2.2 Расчёт БД](#22-расчёт-бд)
- [3. Логическая схема](#3-логическая-схема-бд)
- [4. Физическая схема](#4-физическая-схема-бд)
- [5. Технологии](#5-технологии)
- [6. Схема проекта](#6-схема-пректа)
- [Список литературы](#список-литературы)

## 1.1 Тема

**Яндекс.Такси** - одна из самостоятельных бизнес-единиц «Яндекса», предлагающая сервисы такси.

### MVP:

1. Профиль таксиста и пользователя.
2. Заказ такси на адрес.
3. Отображение списка заказов таксистам.
4. Отображение местоположения таксистов.
5. Построение маршрута и расчёт стоимости по этому маршруту.

## 1.2 Целевая аудитория

Клиентами компаний становятся потребители в возрасте от 18 до 65 лет с разным уровнем достатка. Наиболее частыми клиентами такси являются люди в возрастной категории от 25
до 45 лет.

Наиболее часто пользуются услугами такси следующие категории граждан:
- люди, которые не располагают собственным транспортом;
- люди, которые имеют в собственности личный автомобиль, но не имеют возможности использовать его;
- люди, опаздывающие куда-либо и не рискующие воспользоваться общественным транспортом;
- люди с маленькими детьми; с тяжелыми сумками или другим крупногабаритным грузом или багажом;
-  клиенты, нуждающиеся в транспорте в ночное время суток; клиенты, находящиеся в состоянии алкогольного опьянения.[^1]

Работает в 17 странах [^2]

Доли[^3]:

| Страна        | Доля запросов       |
|---------------|---------------------|
|Россия         |88.47%|
|Беларусь       |4.78%|
|Киргизстан     |3.14%|
|Казахстан      |1.18%|
|Узбекистан     |0.21%|
|Остальное      |2.22%|

### Посетители

| Месяц | Посетителей в месяц |
| ------------- | ------------|
| Ноябрь 2022г  | 4.0 млн     |
| Декабрь 2022г | 4.1 млн     |
| Январь 2023г  | 3.8 млн     |
| Февраль 2023г | 3.7 млн     |

Подтверждение данных через SimilarWeb за ноябрь и декабрь 2022г и январь 2023[^3]
![Подтверждение данных через SimilarWeb](./images/similarweb1.png)
![Подтверждение данных через SimilarWeb](./images/similarweb2.png)

По данным 2020г. в Яндекс.Такси работает 700000 водителей (также пользователей сервиса)[^4]

Яндекс.Такси обрабатывает около 70% заказов такси в России[^5], при этом по Росcии в 2021г было произведенео 2620 млн поездок[^6].
Таким образов на базе Яндекс.Такси в России произволится 2620*0.7/365 ~ 5 млн поездок в день.

## **2. Расчет нагрузки**

Количество поездок в день: ~5 млн

Пользователей в месяц: ~4 млн

Исходя из этого грубо примем, что большинство из них - постоянные клиенты и заказывают такси каждый день, то есть по 5 млн. поездок / 4 млн пользователей = 1.25 раз в день.

По данным сайта SimilarWeb пользователь проводит на сайте до 3-х минут, что примерно совпадает со средним временем ожидания такси по личному опыту.

Каждому пользователю, который зашел в приложение, показывается карта с расположением автомобилей, эта информация обновляется примерно раз в 5 секунд исходя из собственных замеров.

Пользователь может заходить проверять сервис, чтобы сравнить цены на поездку, но не вызывая такси, учитывая это, примем значение открытия приложения без заказа такси в день примем равным 1.

Из данных выше выходит, что пользователь получает информацию о свободных машинах (1.25 заказов в день + 1 заход без заказа в день) * 3 минуты в сервисе за раз * (60 секунд в минуте / 5 секунд кулдаун на получение новой информации) = 81 раз в день.

Пользователь использует подсчёт цены 1.25 раз в день при заказе + 1 раз в день без заказа = 2.25 раз в день.

При слежке за водителями отображается карта, которая скорее всего получается от сервиса карт Яндекса. Для отображения одной локации в яндекс картах используется около 6 тайлов (Проверено самостоятельно опытным путём) и нахождение пользователя на одном месте при просмотре карты как правило не потребуется больше 16 (Т.к. скорее всего не отобразится больше чем по 3 тайла сверху и снизу и ещё 4 с одной из боковых сторон) тайлов - 2.25 * 16 = 36 раз.

|Заказ такси|Отслеживание ближайших машин|Подсчет цены, примерного времени поездки для заданного маршрута|Получение тайлов для карт|
|--|--|--|--|
|1.25|81|2.25|36|

### 2.1 Расчёт количества запросов

#### Расчет количества запросов в день:
- заказ такси - 5 млн
- местоположение автомобилей - 81 запрос в день * 4 млн пользователей = 324 млн
- подсчет цены, примерного времени поездки для заданного маршрута - 2.25 * 4 млн = 9 млн
- получение тайлов для карт = 36 * 4 млн = 144 млн

Заказ такси требует передачи не требует существенных данных

Запрос местоположения автомобилей не требует существенных данных

Запрос подсчета цены и примерного времени поездки для заданного маршрута  не требует существенных данных

Подтверждение заказа требует следующих существенных данных:

Размер аватара водителя примем в среднем равным 1Мб.

1Мб на аватар * 5 млн заказов в день = 4.77 Тб в день

Запрос тайлов для карт и отображения местностипри трекинге водителей требует следующих существенных данных:

Размер одного тайла ~20Кб(проверено на Яндекс. Картах)
Итог: 20Кб на тайл * 36 запросов тайлов в день у пользователя * 4 млн пользователей = 2880 млн Кб ~ 2.68 Тб в день

#### Суммарный суточный трафик (Тб/сутки):

4.77 + 2.68 = 7.45 Тб в сутки

#### Пиковое потребление в течении суток (Гб/c):

Пиковое время для сервиса такси - вечера пятницы и субботы с 18:00 до 23:00 - в этот период совершается в 2 раза больше заказов, чем в рабочие часы по будням[^7]

7.45 Тб в обычный день / 24 часа в сутках / 60 минут в часе / 60 секунд в минуте * 2 раза больше нагрузка = 0,17 ГБ/с

### 2.2 Расчёт БД

Всего пользователей скачало приложение - 100 млн[^8], однако это сервис Яндекса и большая часть информации хранится в отдельной базе Яндекса, а не базе сервиса, поэтому рассмотрим данные нужные конкретно сервису такси. Приложение содержит не только услуги такси, но так как они объединены в одно приложение предположим, что имеют общую базу и попробуем расчитать часть, которую на себя берёт Яндекс.Такси.
Для каждого пользователя требуется хранить следующие свойства:
- email - для связи с основным аккаунтом (почти не обновляется);
- рейтинг пользователя (обновляется несколько раз в день);
- координаты: (обновляется очень часто)
- избранные места (обновляется редко);
- история заказов (обновляется несколько раз в день)
- данные платежной карты (обновляется редко);

Всего водителей в сервисе - 700 000 человек.

Для водителя требуется хранить следующие данные:
- фото (обновляется редко);
- имя (почти не обновляется);
- email (почти не обновляется);
- номер телефона (почти не обновляется);
- рейтинг водителя (обновляется несколько раз в день);
- категория прав (почти не обновляется)
- координаты: (обновляется очень часто)
- Информация о машине (почти не обновляется)
- история заказов (обновляется много раз в дент)
- данные платежной карты (почти не обновляется);

##  3. Логическая схема БД
### Основная БД
#### Пользователи:
|id   |mainid|rating|crd_id|coords|
|-----|------|------|------|------|
|uid  |uid   |uid   |uid   |uid   |
|pk   |fk    |fk    |fk    |fk    |

#### Водители:
|id |email |rating|crd_id|avatar|phone |rightscat|coords|car_id|
|---|------|------|------|------|------|---------|------|------|
|uid|string|uid   |uid   |string|string|string   |uid   |uid   |
|pk |      |fk    |fk    |      |      |         |fk    |fk    |

#### Координаты:
|id |coord_x|coord_y|
|---|-------|-------|
|uid|float  |float  |
|pk |       |       |

#### Рейтинг пользователя:

|id |value|
|---|-----|
|uid|float|
|pk |     |

#### Рейтинг водителя:

|id |value|
|---|-----|
|uid|float|
|pk |     |

#### Карты:
|id |number|cvv|name  |surname|
|---|------|---|------|-------|
|uid|string|int|string|string |
|pk |      |   |      |       |

#### Избранные места:
|id |user|name  |address|
|---|----|------|-------|
|uid|uid |string|string |
|pk |fk  |      |       |

#### Машины:
|id |model |manuf |year|state |
|---|------|------|----|------|
|uid|string|string|int |string|
|pk |

#### Поездки:
|id |driver|car|driver_rating|user|user_rating|price|start_addr|end_addr|date|status|
|---|------|---|-------------|----|-----------|-----|----------|--------|----|------|
|uid|uid   |uid|int          |uid |float      |float|string    |string  |Date|int   |
|pk |fk    |fk |             |fk  |

#### Сессии:
|id |email |token |
|---|------|------|
|uid|string|string|
|pk |fk    |      |

### Таблицы из БД других сервисов

#### Пользователи:
|id |email |avatar|phone |name  |
|---|------|------|------|------|
|uid|string|string|string|string|
|pk |

### Схема
![Схема БД](./images/DBScheme.jpg)

## 4. Физическая схема БД

### PostgreSQl, редко обновляемые и читаемые:
Пользователи, машины, карты, избранные места

### Tarantool, часто обновляемые и читаемые:
водители, координаты

### ClickHouse, аналитика:
рейтинг, поездки

### Redis
Сессии

## 5. Технологии

|Технология|Применение|Аргументация|
|--|--|--|
|С++|Back-end|Самый низкоуровневый и быстрый из ООП языков, а быстродействие в приоритете|
|PostgreSQL|База данных|используется в местах, которые требуют join запросов и при этом редк меняются|
|Tarantool|База данных|используется в местах, которые сильно ограничены временем, идеально подходит засчет своей производительности|
|ClickHouse|База данных и обработка аналитических запросов|Хорошая БД для работы с аналитическими запросами|
|Redis|ib-memory хранение данных|Быстрая БД для данных, которые не опасно потерять|
|Nginx|балансировка нагрузки, кэширование|широко распространенный, производительный веб-сервер, имеющий функции балансировщика L7 и кеширования|
|Apache Kafka|брокер сообщений|отправка асинхронных сообщений между сервисами|
|Яндекс.Карты (или их подсистема)|обработка географических данных, хранение и раздача тайлов карт. Внешний API|Другой сервис компании, поэтому точно находится под контролем, взаимодействие можно тонко настроить|

## 6. Схема пректа

![Схема проекта](./images/projectScheme.jpg)

## Список литературы

[^1]: [Проект по сдаче автомобилей на базе Яндекс. Такси](https://damu.kz/poleznaya-informatsiya/biznes-plani/2020/1.%D0%91%D0%B8%D0%B7%D0%BD%D0%B5%D1%81%20%D0%BF%D0%BB%D0%B0%D0%BD%20_%D1%82%D0%B0%D0%BA%D1%81%D0%B8.pdf)
[^2]: [Отчёт Яндекса за 2019г](https://company-docs.s3.yandex.net/prospectus/annual_2019.pdf)
[^3]: [Страница с метриками Яндекс.Такси SimilarWeb](https://www.similarweb.com/website/taxi.yandex.ru/#traffic)
[^4]: [Статья автокредитования для водителей в Яндекс.Такси](https://www.vedomosti.ru/business/articles/2020/10/01/841894-yandekstaksi-zapustilo)
[^5]: [Сравнение конкурентов Яндекса на фоне побега одного из них из Росии](https://www.cnews.ru/news/top/2021-11-08_estonskij_konkurent_yandeks)
[^6]: [Численность поездок на такси в России 2021г](https://marketing.rbc.ru/articles/13500/)
[^7]: [Исследования Яндекса о такси в Москве](https://yandex.ru/company/researches/2015/moscow/taxi)
[^8]: [Приложения Яндекс.Go](https://play.google.com/store/apps/details?id=ru.yandex.taxi&hl=ru&gl=US#:~:text=5%2C91%C2%A0%D0%BC%D0%BB%D0%BD%20%D0%BE%D1%82%D0%B7%D1%8B%D0%B2%D0%BE%D0%B2-,100%C2%A0%D0%BC%D0%BB%D0%BD%2B,-%D0%9A%D0%BE%D0%BB%D0%B8%D1%87%D0%B5%D1%81%D1%82%D0%B2%D0%BE%20%D1%81%D0%BA%D0%B0%D1%87%D0%B8%D0%B2%D0%B0%D0%BD%D0%B8%D0%B9)
[^9]: [Первый миллиард Яндекс.Такси](https://taxi.yandex.ru/blog/billion-trips)

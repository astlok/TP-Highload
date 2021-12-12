# Telegram
## MVP
- Отправка личного сообщения
- Прикрепление фотографии в сообщении
- Груповые чаты
## Целевая аудитория:
- 500 млн человек активных пользователей в месяц
- 55 млн человек активных пользователей в день
- 15 миллиардов сообщений каждый день
- 175 миллионов фотографий ежедневно
- Северная Америка, Россия, Европа
## Рассчет нагрузки:
1. #### Рассчет нагрузки на отправку сообщений
Каждый день пользователь отправляет 300 млн человек оправляют 30 миллиардов сообщений.
Считать нагрузку будем среднюю за сутки и в часы наибольшей активности аудитории, это приблизительно с 9 до 23 итого 14 часов

Среднее за сутки:

    15 000 000 000 / (24 * 3600) = 173 612 запросов в секунду
    
В период наибольшей активности:

    15 000 000 000 / (14 * 3600) = 297 620 запросов в секунду

Средняя длинна сообщения в мессенджере 70 символов, для небольшого запаса возьмем 100.
Один символ в unicode занимайт 2 байта + 1 байт служебной информации, посчитаем траффик на получение сообщений

Среднее за сутки:

    3 * 100 * 15 000 000 000 / (24 * 3600) = 0.39 Гбит/с
    
В период наибольшей активности:

    3 * 100 * 15 000 000 000 / (14 * 3600) = 0.67 Гбит/с
    
Для расчета траффика на отправку необходимо учесть, что часть сообщений будет отправленно в групповые чате,
в результате чего доставлять их нужно будет всем пользователям в данном группе.
Предположим, что в среднем 1/3 от всех сообщений отправляется в групповые чаты. На начальных этапах до сбора полноценной статистики
ограничим количество людей в одной группе, сделаем равным 100 человек, считать будем по максимальному значению людей в одной группе

Среднее за сутки:

    3 * 100 * 10 000 000 000 / (24 * 3600) + 3 * 100 * 5 000 000 000 * 99 / (24 * 3600) = 13.06 Гбит/с
    
В период наибольшей активности:

    3 * 100 * 10 000 000 000 / (14 * 3600) + 3 * 100 * 5 000 000 000 * 99 / (14 * 3600) = 22.4 Гбит/с
    
Зная, что одно сообщение в среднем весит 300 байт, то нам нужно следующего размера хранилище на пользователей в месяц:

    300 * 15 000 000 000 * 30 = 122.8 Тбайт

2. #### Рассчет нагрузки на отправку фото
Ежедневно отправляется около 600 миллионов фото, считаем RPS в период наибольше активности

Среднее за сутки:

    175 000 000 / (24 * 3600) = 2 026 запросов в секунду
    
В период наибольшей активности:

    175 000 000 / (14 * 3600) = 3 472 запросов в секунду
    
Отправленые фото будем сжимать, считая, что в среднем вес одной картинки 500 Кбайт получаем, считаем траффик на получение
Среднее за сутки:

    500 * 175 000 000 / (14 * 3600) = 7.73 Гбит/с
    
В период наибольшей активности:
    
    500 * 175 000 000 / (14 * 3600) = 13.25 Гбит/с
    
Для рассчета траффика отправку, воспользуемся аналогичным методом из пункта про сообщения

Среднее за сутки:

    500 * 116,666,667 / (24 * 3600) + 500 * 58,333,333 * 99 / (24 * 3600) = 260.13 Гбит/с
    
В период наибольшей активности:

    500 * 116,666,667 / (14 * 3600) + 500 * 58,333,333 * 99 / (14 * 3600) = 445.93 Гбит/с
    
Тогда аналогично расчету выше посчитаем размер хранилища на всех пользователей в месяц:

    500 * 175 000 000 * 30 = 2.39 Тбайт

3. #### Рассчет авторизация пользователей:
 
Будем считать, что в среднем пользователь авторизуется 0.25 раз за день, тк у большинства пользователей

Считаем RPS:

В среднем за сутки:

    55 000 000 * 0.25 / (24 * 3600) = 160 запросов в секунду
    
В период наибольшей активности:

    55 000 000 * 0.25 / (14 * 3600) = 273 запроса в секунду
    
Пользователи будут авторизироваться по логину + паролю, которые отправляются в http запросе
Такой запрос состоящий из двух полей будет весить не больше 1 Кбайта
На каждый входящий запрос будет один исходящий, поэтому и на принятие и на отправку нагрузка на сеть будет одинаковой:

В среднем за сутки:

    1 * 55 000 000 * 0.25 / (24 * 3600) = 0.00122 Гбит/с

В период наибольше активности:

    1 * 55 000 000 * 0.25 / (14 * 3600) = 0.0021 Гбит/с

Для хранения будем использовать NoSQL базу данных, в которой будет хранится ключ + значение (логин + пароль)
В таком базе поле будет весить тоже не более 1 КБайта. В этом случае для хранения базы всех пользователей нам потребуется:

    500 000 000 * 1 = 0.466 Тбайт
    
4. #### Расчет получения профиля пользователя
Предположим, что каждый ползователь в день один раз загружает свой профиль и 2 раза открывает чей-то чужой. Те в сумме 3 запроса
Более точные данные будем брать согласно нашей статистике после какого-то времени эксплуатации

Считаем RPS:

В среднем за сутки:
    
    55 000 000 * 3 / (24 * 3600) = 1 909 запросов в секунду

В период наибольше активности:

    55 000 000 * 3 / (14 * 3600) = 3 274 запроса в секунду
    
Размер профиля возьмем равным одной фотографии + логин + описание
Размер одной картинки в среднем - 500 КБайт, всех полей не более 2 КБайт и того 502 Кбайта на 1 профиль
Тут количество траффика на получение и отправку будет так же одинаковое

В среднем за сутки:
 
    55 000 000 * 3 * 502 / (24 * 3600) = 7.3 Гбит/с
    
В период наибольше активности:
    
    55 000 000 * 502 / (24 * 3600) = 12.6 Гбит/с
    
Для хранения всех профилей нам потребуется:

    55 000 000 * 502 = 25.72 Тбайт

Итоговая таблица:
|| RPS в среднем| RPS пиковое значение | Пиковый трафик получения в среднем, Гбит/с | Пиковый трафик получения пиковый, Гбит/с | Пиковый трафик отдачи в среднем, Гбит/с | Пиковый трафик отдачи пиковый, Гбит/с | Размер хранилища в месяц, Тбайт |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Сообщения | 173 612 | 297 620  | 0.39 | 0.67 |13.06  |22.4 |122.8|
| Картинки | 2 025 | 3 472 | 7.73 | 13.25| 260.13|445.93|2.39|
| Авторизация | 160|273 | 0.00122| 0.0021|0.00122 |0.0021|0.466|
| Получение профиля | 1 909| 3 274| 7.3| 12.6|7.3 |12.6|25.72|

## Логиеская схема:
<img width="521" alt="Снимок экрана 2021-12-11 в 23 10 07" src="https://user-images.githubusercontent.com/64102363/145690223-dbf8eb4d-bd49-48c4-9714-6f3331a0d1fc.png">

## Физическая схема:
<img width="599" alt="Снимок экрана 2021-12-11 в 23 09 44" src="https://user-images.githubusercontent.com/64102363/145690217-e0303d60-49f1-4f47-9c21-49eb1ddfd997.png">

Для хранения всех сущностей будем использовать Postgresql. Она достаточно надежна и из коробки поддерживает шардирование и репликацию

Один сервер, вряд ли сможет выдержать нашу нагрузку, поэтому необходимо делать шардинг, 
Шардинг таблицы сообщений будем производить по полю chat_id
Для чатов и пользователей можно сделать шардинг по id
Помимо шардинга имеет смысл сделать master-slave репликацию, во-первых для большей отказоустойчивости, во-вторых, те данные, что не записываются и не требуют моментального чтения можно будет читать во slave сервера, сейчас у нас нет таких данных, но при расрастании сервиса и добавления новых фич, они могут появиться
Так же для уменьшения походов по нескольким базам, произведем денормализацию: в таблицу сообщений добавим поля имя отправителя, и линк фотографии (при необходимости), потому что эти поля всегда будут идти вместе, а значит стоит их хранить вместе.

Для хранения фотографий будем использовать amazom S3 хранилище, а в базе будем хранить лишь ссылку, которую и будем отдавать на клиент

Для ускорения запросов, стоить сделать индексы, все id проиндексированны по умолчанию тк они являются primary key, все foreign key, тоже проиндексированы,
поэтому дополнительных индексов на данном этапе не требуется, если в процессе работы нашего сервиса, мы будем понимать, что какие-то конкретные запросы проседают, то будет уже вводить индексы на их основе

Для хранения сессий будем использовать noSql in-memory базу данных tarantool, посколько в ней данные хранятся в оперативной памяти и сама база noSql, то это очень сильно ускорит работы данной таблицы, по сравнению с любой реляционной базой. 
Tarantool прекрасно шардируется, так же поддерживает master-slave репликацию

## Технологии

| Технология| Область применения | Мотивационная часть |
| :---: | :---: | :--- |
| TypeScript | Frontend | Фактически стандарт для веба сейчас, преимущество перед JS в наличие статической типизации|
| GO | Backend | Асинхронность из коробки в виде горутин, которыми легко управлять, быстрая скорость компиляции, простота разработки, в случае какого-то узкого можно - возможность использования cgo для написании куска кода на другом языке (например C)  |
| C++ QT| Клиент для windows и linux | Хорошее кроссплатформенное решения для написания клиентских приложений, которые необходимо иметь на всех платформах|
| Swift | IOS | Тут альтернатив немного, использовать ненативную разработку на мой взгляд - боль для конечного потребителя | 
| Kotlin | Android | Аналагичная ситутаяция как и со swift |
| Swift | MacOS | Очень удобно будет для конечного пользователя адаптировать клиент под macOS путем небольшой правки исходного кода IOS приложения, приложение будет гораздо приятнее смотреться и лучше работать, нежели клиент на QT (такую ситуацию сейчас можно наблюдать с telegram)|
| Nginx | Веб-сервер, L7 балансировка, отдача статики | Быстрый асинхронный веб-сервер, легко настраивается |
| Postgres | База данных | Мотивация по выбору описана в предыдущем пункте |
| Tarantool | База данных | Мотивация по выбору описана в предыдущем пункте |
| Amazon S3 | Хранение файлов | Удобное апи для загрузки/отдачи файлов, скорость работы, масштабируемость |
| Centos | Серверная ОС | Имеет ряд преимуществ перед стандартной ubuntu (например rpm пакеты, которые удобно выкатывать в прод) подробное сравнение в [7]|

## Источники:
1. [https://vc.ru/social/195967-auditoriya-telegram-prevysila-500-mln-polzovateley-v-mesyac-eto-na-100-mln-bolshe-chem-v-aprele-2020-goda](https://vc.ru/social/195967-auditoriya-telegram-prevysila-500-mln-polzovateley-v-mesyac-eto-na-100-mln-bolshe-chem-v-aprele-2020-goda)
2. https://backlinko.com/telegram-users#telegram-statistics
3. https://telegram.org/blog/15-billion
4. https://www.businessofapps.com/data/telegram-statistics/
5. https://siteefy.com/telegram-statistics/
6. https://ichip.ru/sovety/7-faktov-o-whatsapp-kotorykh-vy-ne-znaete-191822
7. https://www.openlogic.com/blog/centos-vs-ubuntu#best-choice

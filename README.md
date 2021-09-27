# Telegram
## MVP
- Отправка личного сообщения
- Прикрепление фотографии в сообщении
## Целевая аудитория:
- 500 млн человек активных пользователей в месяц
- 300 млн человек активных пользователей в день
- 30 миллиардов сообщений каждый день
- 600 миллионов фотографий ежедневно
- Северная Америка, Россия, Европа
## Рассчет нагрузки:
1. #### Рассчет нагрузки на отправку сообщений
Каждый день пользователь отправляет 300 млн человек оправляют 30 миллиардов сообщений.
Считать нагрузку необходима в часы наибольшей активности аудитории, это приблизительно с 9 до 23 итого 14 часов

    30 000 000 000 / (14 * 3600) = 595 240 запросов в секунду

Средняя длинна сообщения в мессенджере 70 символов, для небольшого запаса возьмем 100.
Один символ в unicode занимайт 2 байта + 1 байт служебной информации

    3 * 100 * 30 000 000 000 / (14 * 3600) = 1.34 Гбит/с
    
Зная, что одно сообщение в среднем весит 200 байт, то нам нужно следующего размера хранилище на пользователей в месяц:

    200 * 30 000 000 000 * 30 = 163 Тбайт

2. #### Рассчет нагрузки на отправку фото
Ежедневно отправляется около 600 миллионов фото, считаем RPS в период наибольше активности

    600 000 000 / (14 * 3600) = 11 904 запросов в секунду
    
Отправленые фото будем сжимать, считая, что в среднем вес одной картинки 500Кбайт получаем:

    500 * 600 000 000 / (14 * 3600) = 45.41 Гбит/с
    
Тогда аналогично расчету выше посчитаем размер хранилища на всех пользователей в месяц:

    500 * 30 000 000 000 * 30 = 420 Тбайт
    
Итоговая таблица:
|| RPS | Пиковый трафик, Гбит/с | Размер хранилища в месяц, Тбайт |
| :---: | :---: | :---: | :---: |
| Сообщения | 595 240 | 1.34 | 163 |
| Картинки | 11 904 | 45.41 | 420 |

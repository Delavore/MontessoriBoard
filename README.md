# MontessoriBoard

# Проблема
Есть множество обычных детей и детей с ДЦП, которым надо развивать мелкую моторику. Для такой проблемы сущесвуют доски Монтесори (в данном проекте имеется ввиду доска-лабиринт). Я подумал, что данные доски можно улучшить добавив в них микроконтроллер: в текущей реализации добавлен секундомер, который засекает время прохождения лабиринта. Такой подход может привлечь детей не бросать лабиринт, а пытаться улучшить свой результат за счет более быстрого выполнения и развивать свою моторику.

# Описание 
Данный проект реализован на микроконтроллере atmega328p. В нем используются такие датчики как 74hc595pw (сдвиговый регистр), cc56-12srwa (семисегментный дисплей), герконовые датчики магнитного поля. Кнопка запускает микроконтроллер. Далее, когда магнит проходит около первого геркона, цепь замыкается, и начинается отсчет времени. Время вначале выводится с секундами и милиссекундами, а далее уже с минутами и секундами. Как только магнит пересек второй датчик, время останавливается - это результат прохождения лабиринта. Чтобы заново проходить надо нажать на кнопку для сброса текущего результата.

# Ход работы
Вначале была реализована принципиальная схема. Схема предствлена в json формате в папке EasyEDA/sch, ее можно открыть в EasyEda. Готовая разводка платы находится в папке EasyEDA/pcb. Тут предствлена не первая версия платы. Изначально плата была намного больше, в ней использовалась atmega328p в DIP корпусе.
Для упрощения управления семисегментным датчик использовался сдвиговый регистр: он позволяет задействовать меньше ножек у микроконтроллера и упростить выставление определенных сегментов дисплея. Мы постоянно читаем пины, к которым подключены герконовые датчики, и если они замкнуты в нужное время (в нужный для FSM момент), то мы переходим к следующему состоянию FSM. Как только был замкнут второй датчик (обязательно после первого) время на дисплее сохраняется. Если ничего не делать, то датчик уйдет в сон.
Сон - очень важный аспект данного проекта, потому что энергия - деньги. Если датчик не уйдет в сон, то батарейка быстро разрядится и нужна будет другая. Я тестировал устройство с батарейкой-таблеткой, типа 2032, емкость у нее 200мА. При полной работе микроконтроллера батарейки хватило примерно на 5 дней - можно подумать довольно мало. Однако - это 5 дней беспрерывной работы. В дополнении к этому, как только микроконтроллер видит, что им никто не пользуется, то он переходит в режим сна. В режиме сна потребления тока практически нет, что увеличивает его работу колосально. Режим сна реализован в функции sleepMode(). В ней мы обращаемся напрямую к регистрам микроконтроллера, настраивая его на сон. Для пробуждения из сна необходимо нажать на кнопку. Таким образом, мы очень эффективно расходуем энергию.

# Развитие идеи
Как и любой другой бизнес-проект существуют некие опасения: а будет ли им кто-то пользоваться? Если им будут пользоваться, то будет ли он приносить пользу? Рассуждая над этими вопросами я пришел к выводу, что просто засечения времени не хватит: это слишком маленький функционал для того, чтобы долго удерживать ребенка у данного лабиринта. Как можно улучшить? Во-первых, можно добавить память, чтобы запоминать лучший результат. Во-вторых, можно добавить пьезоэлемент (потому что он дешевый, но можно и другой источник музыки). Дети любят музыку, любят чтобы что-то пищало. В отличие от чтения цифр на дисплее тут не нужны знания. В-третьих, можно добавить фоторезистор, для уменьшения яркости дисплея в зависимости от окружающего освещения: когда темно, датчик очень хорошо виден, и его можно сделать менее ярким (но эту идею надо тестировать, потому что возможно выгоднее и не подключать фоторезистор: может быть при включенном модуле АЦП будет более большое потребление, чем при выключенном).

# Плата
![board](https://github.com/user-attachments/assets/aa34f944-388c-47d2-98fa-96524988f4ab)

![board2](https://github.com/user-attachments/assets/d5608aab-3a13-4add-9552-bf5188b20a54)

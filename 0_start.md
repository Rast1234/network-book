# Поехали

Сеть в нашем случае - это способ связи между компьютерами. Для обеспечения связи нужно решить много разных задач и как-то договориться, как обмениваться данными по проводам, радио и т.п., чтобы все участники сети могли принимать и отправлять информацию. Договоренность, в каком виде и в каком порядке отправлять, принимать и обрабатывать информацию - это **протокол**. Протоколов много, каждый решает какие-то свои задачи, и все сетевое взаимодействие в основном сводится к пониманию протоколов, их комбинаций и параметров. **Стэк протоколов** - комбинация, которая используется вместе для работы сети. Стэк, т.е. стопка, потому что протоколы надстроены друг над другом.

Кстати, важно заметить, что компьютерные сети в основном работают с помощью **пакетной передачи** данных, т.е. устройства по очереди обмениваются небольшими сообщениями (**пакетами**). Благодаря этому в одной сети могут общаться много участников-компьютеров. Такое разделение одной сети на нескольких участников называется **пакетной коммутацией (packet switching)**. В телефонных сетях в основном используется **канальная коммутация**, когда между двумя устройствами устанавливается канал связи (например, физически по проводу), и этот канал используется только для связи этих двух участников до конца сеанса.

Пакетная коммутация сложнее в реализации, требует цифровых устройств и поэтому появилась позже, чем канальная. Для канальной коммутации достаточно аналогового оборудования (проводные телефоны и телефонные станции), но нужно постоянно иметь много каналов связи, если нужно подключаться к разным устройствам одновременно.

Из предыдущих двух абзацев нужно запомнить, что все сетевые протоколы оперируют пакетами - небольшими кусками данных, которые могут быть отправлены разными устройствами друг другу в одной сети.

В описаниях протоколов понадобится как-то обозначать байты и биты. Бит - это просто 0 или 1, а байт можно представить по-разному:



*   как набор 8 бит, т..е двоичное число (BIN, binary), например **10101010**
*   как десятичное число (DEC, decimal) от 0 до 255, например **170**
*   как шестнадцатеричное число (HEX, hexadecimal) от 00 до FF, например **AA**

Чаще всего используются шестнадцатеричный вид, потому что байт всегда занимает два знака, и его удобнее записывать. Считать практически ничего не понадобится, поэтому не обязательно уметь складывать или конвертировать такие числа в десятичный вид, достаточно просто помнить, что шестнадцатеричные цифры - это 0123456789ABCDEF.


## OSI

Чтобы было легче ориентироваться во множестве протоколов и разрабатывать новые, есть общепринятая модель, можно сказать, классификация (как в биологии, животные-позвоночные-млекопитающие-...). **Сетевая модель OSI** (англ. _Open Systems Interconnection basic reference model_ — базовая эталонная модель взаимодействия открытых систем, сокр. **ЭМВОС**; 1978 год) — сетевая модель стека сетевых протоколов OSI/ISO (ГОСТ Р ИСО/МЭК 7498-1-99). В OSI 7 уровней - категорий, на которые условно можно разделить все протоколы. Классификация старая, не строгая, есть протоколы, которые нарушают уровни, или работают на нескольких. Но важно ее знать, потому что с ее помощью проще объяснить, как все работает _вместе_. OSI состоит из 7 уровней, причем принято начинать отсчет с нижнего:

    7. Прикладной уровень (application layer)
    6. Представительский уровень или уровень представления (presentation layer)
    5. Сеансовый уровень (session layer)
    4. Транспортный уровень (transport layer)
    3. Сетевой уровень (network layer)
    2. Канальный уровень (data link layer)
    1. Физический уровень (physical layer)

Дальше будем разбираться в сетях и протоколах, последовательно с первого уровня. 


## Инкапсуляция

Для понимания всего, что будет дальше, нужно понимать идею инкапсуляции. Проще всего будет объяснить ее на примере. Предположим, мы отправляем письмо в другую страну по обычной почте.

*   Текст письма - полезные данные, которые мы хотим доставить. 
*   Мы заворачиваем письмо в конверт, который подписываем по определенным правилам - это мы используем протокол нашей местной почты.
*   Почта в свою очередь, например, грузит конверт в контейнер и тоже как-то его подписывает для доставки на самолете в другую страну - это почта использует протокол международного почтового обмена.
*   Потом в стране назначения контейнер открывают
*   Читают что написано на конверте, и понимают, в какой город письмо отправить
*   Получатель в итоге открывает конверт и достает письмо.

Во всей этой цепочке письмо ничего не знало про конверт, а конверт ничего не знал про контейнер, их просто заворачивали целиком, а потом доставали. И никто не лез внутрь контейнера, чтобы посмотреть на текст письма. Это все вместе называется "**инкапсуляция**".

Применимо к нашей теме: протоколы разных уровней последовательно "завернуты" друг в друга, то есть данные, которые доставляет протокол 2-го уровня, вложены в протокол 1-го, и не зависят от него, поэтому на 1-м уровне может быть что угодно. А данные протокола 3-го уровня ничего не знают о 2-м и 1-м уровне, поэтому на 1 и 2 может быть что угодно. Как на почте: можно письмо положить в контейнер для самолета, а можно - в контейнер для корабля.

Очень упрощенный пример: мы хотим кому-то отправить какие-то данные по сети, например, картинку **image.jpg**.

* Это просто набор байт. Для наглядности обозначим все байты картинки так: `[image.jpg]`. Это текст нашего письма из примера выше.
* Нам придется разбить всю картинку на куски, потому что одним "письмом" не пролезет, есть ограничения. Обозначим куски так: `[imag]  [e.jp]  [g]`
* Каждый кусок будет завернут протоколом TCP, то есть к байтам картинки будут в начало добавлены какие-то технические данные, необходимые протоколу для работы, обозначу их как `T`: `[Timag]  [Te.jp]  [Tg]`. Это мы положили письмо в конверт. Там написано, из скольки кусков состоит полное послание, в каком порядке их собирать, как понять что они прибыли в целости и сохранности. А еще в какой дом доставить.
* Дальше заворачиваем все в протокол IP, обозначу его как `I`: `[ITimag]  [ITe.jp]  [ITg]`. Это мы положили конверты в мешки для городской почты. Там написаны город-улица-дом отправителя и получателя.
* Теперь все отдается протоколу Ethernet, у него обертка добавляется в начало и в конец: `[EITimagE]  [EITe.jpE]  [EITgE]`. Это мы запихали мешки в контейнеры для авиапочты, там есть страна отправителя и получателя, плюс информация для проверки целостности того, что находится внутри `[E...E]`.
* Наконец сетевая карта раскладывает все байты, которые мы тут насобирали, в электрический сигнал и отправляет по витой паре.
* На другом конце провода, пусть будет роутер. Его сетевая карта читает сигнал, собирает байты, читает `[E]`, понимает что это для него. Он у нас работает авиапочтой.
* Роутер смотрит в `[I]`, понимает что вообще-то конечный получатель где-то далеко, зато он знает, куда отправить дальше. Он собирает новый `[E]`, перепаковывает `[ITimag]` с новым `[E]` и отправляет куда-то.
* Получатель в итоге точно так же распечатывает `[E]`, потом `[I]`, понимает что это для него, смотрит в `[T]`, понимает как собрать послание целиком, дожидается получения всех частей, проверяет что они дошли без искажений, и в итоге у него получается `[image.jpg]`!

Вместо TCP мог быть UDP, вместо IP - IPv6, вместо Ethernet - DSL, вместо витой пары - Wi-Fi. Картинка не знала, едет она по частям или целиком, по воздуху или по проводу, как и провода не знали, что там доставляется и кому вообще. Каждое устройство на пути смотрело на матрешку из протоколов только минимально, чтобы передать дальше или понять "о, это мне" и полезть уже поглубже.

Теперь вы знаете инкапсуляцию! Можно двигаться дальше.

# Навигация

- [Пролог](README.md)
- `0. Поехали`
- [1. Физический уровень (physical)](1_physical.md)
- [2. Канальный уровень (data link)](2_data_link.md)
- [3. Сетевой уровень (network)](3_network.md)
- [4. Транспортный уровень (transport)](4_transport.md)
- [5. Сеансовый уровень (session)](5_session.md)
- [6. Уровень представления (presentation)](6_presentation.md)
- [7. Прикладной уровень (application)](7_application.md)
- [8. Что осталось за кадром](8_end.md)

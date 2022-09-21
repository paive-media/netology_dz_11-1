____

# Задание 1. СУБД

**Кейс**

Крупная строительная компания, которая так же занимается проектированием и девелопментом, решила создать правильную архитектуру для работы с данными. Ниже представлены задачи, которые необходимо решить для каждой предметной области.

Какие типы СУБД, на Ваш взгляд, лучше всего подойдут для решения этих задач и почему?

____

### 1.1 Бюджетирование проектов с дальнейшим формированием финансовых аналитических отчетов и прогнозирования рисков (СУБД должна гарантировать целостность и четкую структуру данных).
Реляционная СУБД. Например: `MSSql/Access + MS bi для отчетов` или `PgSQL + собственные дашборды`. 
Позволит соблюдать структуру, следить за целостностью и связностью данных


#### 1.1* Хеширование стало занимать длительно время, какое API можно использовать для ускорения работы?

Если правильно понял вопрос то: 
- pg_hint_plan
- Google Cloud SQL Insights
- explain.tensor.ru

Источник [на хабре](https://habr.com/ru/company/cloud_mts/blog/659455/)


### 1.2 Под каждый девелоперский проект создается отдельный лендинг и все данные по лидам стекаются в CRM к маркетологам и менеджерам по продажам. Какой тип СУБД лучше использовать для лендингов и для CRM? (СУБД должны быть гибкими и быстрыми).

**Реляционная СУБД** для бэкенда CRM

**key-value хранилище** для лендингов, прогреваемое из конфигов при загрузке:
`in-memory на сервере ( memcached / raddis )` и/или `в браузере клиента (local storage / IndexedDB)`


#### 1.2* Можно ли данную задачу закрыть одной СУБД? И если да, то какой именно СУБД и какой реализацией?

Конечно, можно все подвесить хоть даже и на MySQL с лендингами на php.


### 1.3 Отдел контроля качества решил создать базу по корпоративным нормам и правилам, обучающему материалу и так далее, сформированную согласно структуры компании (СУБД должна иметь простую и понятную структуру).

Небольшие таблицы связей, с итоговыми ссылками на файлы статических документов.


#### 1.3* Можно ли под эту задачу использовать уже существующую СУБД из задач выше и если да, то как лучше это реализовать?

Можно к бэкендоввой реляционной СУБД подключать через внешние ключи и таблицы-связки новые данные по ссылкам на нормативные документы в каждой рабочей ситуации согласно должностных инстуркций.


### 1.4 Департамент логистики нуждается в решении задач по быстрому формированию маршрутов доставки материалов по объектам и распределению курьеров по маршрутам с доставкой документов (СУБД должна уметь быстро работать со связями).

Связи &mdash; это снова к реляционным СУБД с индексами, счетчиками, агрегатными функциями и хранимыми процедурами.


#### 1.4* Можно ли к этой СУБД подключить отдел Закупок или для них лучше сформировать свою СУБД в связке с СУБД логистов?

Не можно, а нужно.   `Закупка + логистика = ❤`


#### 1.5* Можно ли все перечисленные выше задачи решить используя одну СУБД? Если да, то какой именно?

Реляционная СУБД, c использованием внешних ключей и таблиц-связок ключей для свойств разрозненных объектов


____


# Задание 2. Транзакции.

### 2.1 Пользователь пополняет баланс счета телефона, распишите пошагово какие действия должны произойти для того, чтобы транзакция завершилась успешно (ориентируйтесь на 6 действий).

1. Аутентификация
2. Авторизация
3. Проверка суммы текущего баланса на счете-источнике достаточного для перевода
4. Проверка существования счета пополняемого номера телефона
5. Отметка о списание суммы перевода
6. Пополнение баланса номера с ожиданием ответа о принятии перевода
7. Уменьшение текущего баланса на счете-источнике

<sup>callback в виде смс-ки о пополнении счета на моб телефоне, но к транзакции это не имеет отношения</sup>


#### 2.1* Какие действия должны произойти, если пополнение счета телефона происходило бы через автоплатеж?

1. Предупреждение о списании
2. Пауза, ожидание отмены автоплатежа
3. Запрос на достаточность суммы для списание со счета-источника, ожидание успешного ответа
4. Запрос на списание со счета-источника, ожидание успешного ответа (= списание со счета-источника)
5. Пополнение баланса
6. Отправка ответа с ожиданием ответа о получении уведомления
7. Уменьшение текущего баланса на счете-источнике

<sup>callback в виде смс-ки о пополнении счета на моб телефоне, но к транзакции это не имеет отношения</sup>


____


# Задание 3. SQL vs NoSQL

### 3.1 Напишите 5 преимуществ SQL систем по отношению к NoSQL.

1. Обязательная структура данных &mdash; сложнее навести бардак
2. Индексы, индексы, индексы
3. Легче поддерживать legacy за счет порядка
4. Математика, агрегирование, фильтрация и анализ данных средствами СУБД
5. Механизм Транзакций
6. Язык запросов &mdash; SQL


![NoSQL-SQL comparsion from highload](https://miro.medium.com/max/1400/0*ECAcVuAJul9U6HJh.png "NoSQL-SQL comparsion")

[Источник](https://highload.today/subd-kakie-byvayut-kak-vybrat/)
[Источник2](https://www.geeksforgeeks.org/difference-between-nosql-and-newsql/)


#### 3.1* Какие, на Ваш взгляд, преимущества у NewSQL систем перед SQL и NoSQL.

NewSQL стремятся совместить в себе преимущества NoSQL и транзакционные требования SQL СУБД. 
Опыта использования нет.


____


# Задание 4. Кластеры

### Необходимо производить большое количество вычислений при работе с огромным количеством данных, под эту задачу выделено 1000 машин. На основе какого критерия будете выбирать тип СУБД и какая модель распределенных вычислений здесь справится лучше всего и почему?

Опыта работы с bigdata нет. Предположу, что после анализа предполагаемых действий и распределения данных на оперативные-архивные. В примерно, том же соотношении строил с in-memory для быстрых и реляционными СУБД для архивных данных. На лекции была озвучена пропорция: `200 master + 500 slave для Raddis и 300 для PgSQL` машин, например. 


____

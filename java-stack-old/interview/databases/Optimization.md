<!-- TOC -->
* [Optimization of SQL queries](#optimization-of-sql-queries)
  * [Оптимизация запросов в SQL (небольшие подсказки)](#оптимизация-запросов-в-sql-небольшие-подсказки)
  * [Отключите автофиксацию транзакций](#отключите-автофиксацию-транзакций)
  * [Используйте COPY](#используйте-copy)
  * [Удалите индексы](#удалите-индексы)
  * [Удалите ограничения внешних ключей](#удалите-ограничения-внешних-ключей)
  * [Увеличьте maintenance_work_mem](#увеличьте-maintenance_work_mem)
  * [Увеличьте max_wal_size](#увеличьте-max_wal_size)
  * [Отключите архивацию WAL и потоковую репликацию](#отключите-архивацию-wal-и-потоковую-репликацию)
  * [Выполните в конце ANALYZE](#выполните-в-конце-analyze)
  * [Полезные ссылки](#полезные-ссылки)
<!-- TOC -->

# Optimization of SQL queries

## Оптимизация запросов в SQL (небольшие подсказки)

1. Используйте конкретные имена столбцов после оператора `select`, вместо `*` – это позволит увеличить скорость отработки запроса и уменьшению сетевого трафика.

2. Сведите к минимуму использование подзапросов.

Например, запрос
```sql
Select Column_A 
From Table_1
Where Column_B = (Select max (Column_B From Table_2)
And Column_C = (Select max (Column_C From Table_2)
And Column_D = ‘position_2’
```

выглядит значительно хуже на фоне аналогичного запроса:

```sql
Select Column_A 
From Table_1
Where (Column_B, Column_C) = (Select max (Column_B), max (Column_C) 
From Table_2)
```

3. Используйте оператор `IN` аккуратно, поскольку на практике он имеет низкую производительность и может быть эффективен только при использовании критериев 
фильтрации в подзапросе.

4. Соединение таблиц в запросе также является критичным: в случае, когда соединение таблиц происходит в правильном порядке, то общее число строк, необходимых к 
обработке, значительно сократится. При соединении основной и уточняющей таблиц убедитесь, что первой будет основная таблица, в противном случае вы рискуете 
получить обработку гораздо большего числа строк, чем необходимо.

5. При соединении таблиц `EXIST` предпочтительнее `distinct` (таблицы отношения `один-ко-многим`).

6. Избыточность при работе с `SQL` – это критичная необходимость, используйте в разделе `WHERE` как можно больше ограничивающих условий.

Например, если указан

```sql
WHERE Column_А=Column_В and Column_А=425
```

вы сможете вывести результат, где `Column_В=425`, однако при задании условий

```sql
WHERE Column_А=Column_В and Column_B=Column_C
```

оператор не сможет определить, что `Column_A=Column_C`.

7. Пишите простые запросы. Больше упрощайте. Оптимизатор может не справиться со слишком сложными операторами. Кроме того, иногда выполнение нескольких простых до 
невозможности операторов дает лучший результат по сравнению со сложными и позволяет добиться лучшей эффективности.

8. Помните, что одного и того же результата можно добиться разными способами. Например, оператор `MINUS` выполняется гораздо быстрее, чем запросы с оператором 
`WHERE NOT EXIST`. Запрос с данным оператором в самом общем виде выглядит следующим образом:

```sql
Select worker_id
From workers
MINUS
Select worker_id
From orders
```

Этот пример показывает все значения `worker_id`, которые содержаться в таблице `workers`, но не в таблице `orders`. Другими словами, если бы значение `worker_id` 
одновременно присутствовало в таблицах `workers` и `orders`, то значение `worker_id` не вывелось в результат, поскольку нет конкретики, содержание какой именно 
таблицы вывести как результат отработки запроса.

9. Оформляйте повторяющиеся коды в пользовательскую процедуру. Это может значительно ускорить работу, уменьшить сетевой трафик.

## Отключите автофиксацию транзакций

Выполняя серию команд `INSERT`, выключите автофиксацию транзакций и зафиксируйте транзакцию только один раз в самом конце. (В обычном `SQL` это означает, что нужно 
выполнить `BEGIN` до, и `COMMIT` после этой серии. Некоторые клиентские библиотеки могут делать это автоматически, в таких случаях нужно убедиться, что это так.) 
Если вы будете фиксировать каждое добавление по отдельности, `PostgreSQL` придётся проделать много действий для каждой добавляемой строки. Выполнять все операции в 
одной транзакции хорошо ещё и потому, что в случае ошибки добавления одной из строк произойдёт откат к исходному состоянию и вы не окажетесь в сложной ситуации с 
частично загруженными данными.

## Используйте COPY

Используйте `COPY`, чтобы загрузить все строки одной командой вместо серии `INSERT`. Команда `COPY` оптимизирована для загрузки большого количества строк; хотя она 
не так гибка, как `INSERT`, но при загрузке больших объёмов данных она влечёт гораздо меньше накладных расходов. Так как COPY — это одна команда, применяя её, нет 
необходимости отключать автофиксацию транзакций.

В случаях, когда `COPY` не подходит, может быть полезно создать подготовленный оператор `INSERT` с помощью `PREPARE`, а затем выполнять `EXECUTE` столько раз, 
сколько потребуется. Это позволит избежать накладных расходов, связанных с разбором и анализом каждой команды `INSERT`. В разных интерфейсах это может выглядеть 
по-разному.

Заметьте, что с помощью `COPY` большое количество строк практически всегда загружается быстрее, чем с помощью `INSERT`, даже если используется `PREPARE` и серия 
операций добавления заключена в одну транзакцию.

## Удалите индексы

Если вы загружаете данные в только что созданную таблицу, быстрее всего будет загрузить данные с помощью `COPY`, а затем создать все необходимые для неё индексы. 
На создание индекса для уже существующих данных уйдёт меньше времени, чем на последовательное его обновление при добавлении каждой строки.

Если вы добавляете данные в существующую таблицу, может иметь смысл удалить индексы, загрузить таблицу, а затем пересоздать индексы. Конечно, при этом надо 
учитывать, что временное отсутствие индексов может отрицательно повлиять на скорость работы других пользователей. Кроме того, следует дважды подумать, прежде чем 
удалять уникальные индексы, так как без них соответствующие проверки ключей не будут выполняться.

## Удалите ограничения внешних ключей

Как и с индексами, проверки, связанные с ограничениями внешних ключей, выгоднее выполнять «массово», а не для каждой строки в отдельности. Поэтому может быть 
полезно удалить ограничения внешних ключей, загрузить данные, а затем восстановить прежние ограничения. И в этом случае тоже приходится выбирать между скоростью 
загрузки данных и риском допустить ошибки в отсутствие ограничений.

Более того, когда вы загружаете данные в таблицу с существующими ограничениями внешнего ключа, для каждой новой строки добавляется запись в очередь событий 
триггера (так как именно срабатывающий триггер проверяет такие ограничения для строки). При загрузке многих миллионов строк очередь событий триггера может занять 
всю доступную память, что приведёт к недопустимой нагрузке на файл подкачки или даже к сбою команды. Таким образом, загружая большие объёмы данных, может быть не 
просто желательно, а необходимо удалять, а затем восстанавливать внешние ключи. Если же временное отключение этого ограничения неприемлемо, единственно возможным 
решением может быть разделение всей операции загрузки на меньшие транзакции.

## Увеличьте maintenance_work_mem

Ускорить загрузку больших объёмов данных можно, увеличив параметр конфигурации `maintenance_work_mem` на время загрузки. Это приведёт к увеличению быстродействия 
`CREATE INDEX` и `ALTER TABLE ADD FOREIGN KEY`. На скорость самой команды `COPY` это не повлияет, так что этот совет будет полезен, только если вы применяете 
какой-либо из двух вышеописанных приёмов.

## Увеличьте max_wal_size

Также массовую загрузку данных можно ускорить, изменив на время загрузки параметр конфигурации `max_wal_size`. Загружая большие объёмы данных, `PostgreSQL` 
вынужден увеличивать частоту контрольных точек по сравнению с обычной (которая задаётся параметром `checkpoint_timeout`), а значит и чаще сбрасывать «грязные» 
страницы на диск. Временно увеличив `max_wal_size`, можно уменьшить частоту контрольных точек и связанных с ними операций ввода-вывода.

## Отключите архивацию WAL и потоковую репликацию

Для загрузки больших объёмов данных в среде, где используется архивация `WAL` или потоковая репликация, быстрее будет сделать копию базы данных после загрузки 
данных, чем обрабатывать множество операций изменений в `WAL`. Чтобы отключить передачу изменений через `WAL` в процессе загрузки, отключите архивацию и потоковую 
репликацию, назначьте параметру `wal_level` значение `minimal`, `archive_mode` — `off`, а `max_wal_senders` — `0`. Но имейте в виду, что изменённые параметры 
вступят в силу только после перезапуска сервера.

Это не только поможет сэкономить время архивации и передачи `WAL`, но и непосредственно ускорит некоторые команды, которые могут вовсе не использовать `WAL`, если 
`wal_level` равен `minimal`. (Они могут гарантировать безопасность при сбое, не записывая все изменения в `WAL`, а выполнив только `fsync` в конце операции, что 
будет гораздо дешевле.) Это относится к следующим командам:

```sql
- CREATE TABLE AS SELECT
- CREATE INDEX (и подобные команды, как например ALTER TABLE ADD PRIMARY KEY)
- ALTER TABLE SET TABLESPACE
- CLUSTER
- COPY FROM, когда целевая таблица была создана или опустошена ранее в той же транзакции
```

## Выполните в конце ANALYZE

Всякий раз, когда распределение данных в таблице значительно меняется, настоятельно рекомендуется выполнять `ANALYZE`. Эта рекомендация касается и загрузки в 
таблицу большого объёма данных. Выполнив `ANALYZE` (или `VACUUM ANALYZE`), вы тем самым обновите статистику по данной таблице для планировщика. Когда планировщик 
не имеет статистики или она не соответствует действительности, он не сможет правильно планировать запросы, что приведёт к снижению быстродействия при работе с 
соответствующими таблицами. Заметьте, что если включён демон автоочистки, он может запускать `ANALYZE` автоматически.

## Полезные ссылки

[Как оптимизировать запросы в SQL? - vc.ru](https://vc.ru/newtechaudit/113408-kak-optimizirovat-zaprosy-v-sql)

[Оптимизация производительности - postgrespro.ru](https://postgrespro.ru/docs/postgresql/9.6/populate)

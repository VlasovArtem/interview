# NoSQL

## Краткое введение в различные типы баз данных

Программное обеспечение РСУБД различных производителей, основанное на SQL, традиционно использовалось как наиболее распространенная технология
хранения данных. Все системы СУБД состоят из таблиц, которые можно соединить друг с другом через общие значения в определенных столбцах или внешние
ключи.

Все системы, принадлежащие к этой категории, используют SQL в качестве языка, который извлекает и обрабатывает данные, а также диктует структуру самих
баз данных. Хотя в разных системах СУБД используются разные диалекты SQL (например, PL/SQL от Oracle и T-SQL от Microsoft), ядро синтаксиса абсолютно
одинаково.

Большинство передовых методов проектирования баз данных также применимы ко всем типам РСУБД. Наиболее известными примерами СУБД являются Microsoft SQL
Server, Oracle Database, MySQL и PostgreSQL.

NoSQL, с другой стороны, состоит из нескольких совершенно не связанных между собой технологий, каждая из которых состоит из собственного языка
манипулирования данными, возможностей и лучших практик. Условно их можно разделить на 4 отдельные категории. Тем не менее, эта категоризация очень
широка, поскольку никакие две системы NoSQL из одной и той же категории не настолько похожи друг на друга, что знание одной из них будет означать, что
для овладения другой системой потребуются относительно небольшие усилия по обучению. случай с СУБД.

## Категории NoSQL Database

* **Key-Value store:** данные хранятся в хэш-таблице, где каждый уникальный ключ соответствует определенному объекту данных. Примеры включают
  **_Redis_**, **_DynamoDB_** и **_InfinityDB_**.
* **Document-Based Database:** данные хранятся в форме объекта, написанного на декларативном языке, таком как **_JSON_** или **_XML_**. Примеры
  включают **_MongoDB_**, **_Elasticsearch_**, **_CounchDB_** и **_DocumentDB_**.
* **Column-oriented DBMS:** данные хранятся в таблицах, как и в традиционных СУБД, но они разделены по столбцам, а не по строкам. Примеры включают
  **_HBase_**, **_MariaDB_** и **_Metakit_**.
* **Graph database:** База данных представлена в виде сети, которую можно визуализировать. Примеры включают **_Neo4j_**, **_InfiniteGraph_** и
  **_ArangoDB_**.

Еще больше усложняет дело то, что некоторые системы управления базами данных имеют возможности как традиционных СУБД, так и различных типов NoSQL.
Microsoft SQL Server, например, имеет возможность управлять базами данных столбцов в сценариях хранения данных, а с 2017 года он имеет возможности
управления базами данных графов. Когда дело доходит до открытого исходного кода, у PostgreSQL есть возможность управлять хранилищем документов.

## Когда NoSQL является лучшим выбором чем, чем РСУБД, а когда нет

### NoSQL стоит выбирать когда:

* Когда вы ожидаете, что ваша **структура** данных **будет меняться** довольно часто
* Когда вам нужна система, которая часто используется и **ожидается значительный рост**
* Сбор простой аналитики

### SQL стоит выбирать когда:

* Когда вам нужна **небольшая система** или веб-сайт
* Когда вам нужна транзакционная система отвечающая стандартам **ACID**, где важна консистентность (согласованность) данных
* Сбор комплексной аналитики
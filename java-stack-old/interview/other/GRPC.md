<!-- TOC -->
* [GRPC](#grpc)
  * [Свойства](#свойства)
  * [Типичная реализация](#типичная-реализация)
  * [Фичи](#фичи)
      * [Cтрогая типизация](#cтрогая-типизация)
      * [Обратная совместимость](#обратная-совместимость)
      * [Клиент и сервер из коробки](#клиент-и-сервер-из-коробки)
      * [Отмена запроса и дедлайны](#отмена-запроса-и-дедлайны)
  * [gRPC unary call. Практика](#grpc-unary-call-практика)
  * [Работа с таймаутами](#работа-с-таймаутами)
  * [Grpc vs Rest](#grpc-vs-rest)
    * [Когда лучше использовать REST или gRPC?](#когда-лучше-использовать-rest-или-grpc)
    * [Когда использовать REST API](#когда-использовать-rest-api)
    * [Когда использовать gRPC API](#когда-использовать-grpc-api)
  * [Полезные ссылки](#полезные-ссылки)
<!-- TOC -->

# GRPC

Опенсорсный фреймворк для удаленного вызова процедур (`remote procedure call`).

## Свойства

- поддержка как одиночных вызовов, так и стриминга. То есть все сервисы, которые реализовывают эту спеку, поддерживают оба варианта.
- наличие метаданных, то есть чтобы вместе с полезной нагрузкой вы могли бы передать какие-то метаданные — условно, заголовки.
- поддержка отмены запроса и таймаутов из коробки.
- описание сообщений и самих сервисов осуществляется через некий `Interface Definition Language` или `IDL`. 
- спецификация описывает `wire`-протокол поверх `HTTP/2`, то есть `gRPC` предполагает работу только поверх `HTTP/2`

## Типичная реализация

- `proto` формат в качестве `IDL` по-умолчанию
- `grpc` плагин для `protoc` для компиляции сервисов
- `runtime` библиотеки для большинства языков
- `proto` сообщения для статусов и ошибок

## Фичи

#### Cтрогая типизация

- есть поддержка базовых примитивных типов и строк
- поддержка скаляров и векторов данных
- сообщения могут содержать другие сообщения 

#### Обратная совместимость

- `proto IDL` поддерживает широкий набор типов
- возможность значений по-умолчанию
- все поля опциональные - чтение удаленного поля не вызывает ошибки

Про обратную совместимость. Хочется заметить, что `proto IDL` это формат, в который заложена обратная совместимость из коробки, то есть он задумывался с 
заделом на обратную совместимость, и `Google` выпустил версию `proto3`, которая по сравнению с `proto2` еще больше улучшает обратную совместимость. Там, плюс, 
есть всякие спецификации, как и что можно менять так, чтобы обратную совместимость сохранять в каких-то нетривиальных кейсах.

Есть возможность значений по умолчанию, можно добавлять новые поля и у потребителя ничего не требуется, собственно, изменять. Все поля в `proto3` опциональные 
и их можно, допустим, удалять, и обращение к удаленному полю не вызывает ошибок на клиенте.

#### Клиент и сервер из коробки

- код клиента и шаблон для сервера генерируются на основе `proto`
- есть разные клиенты (синхронный, асинхронный)
- это возможность для любого языка

Еще одна фича `gRPC` — клиент и сервер генерируются при помощи `proto`-компилятора и `gRPC`-плагина на основе `proto`-описания. Есть возможность в моменте, 
когда пишется код, выбрать какой клиент будет использоваться. То есть выбрать асинхронный или синхронный клиент, в зависимости от того, какого рода код вы 
пишите.

#### Отмена запроса и дедлайны

- запрос можно отменить на клиенте и сервере
- есть возможность выставить таймаут на запрос на клиенте
- поддержан механизм обнаружения таймаута или отмены на сервере

## gRPC unary call. Практика

![Screenshot](../../resources/grpc.png)

Обратить внимание — поскольку `gRPC` работает по `HTTP/2`, то используется одно `TCP`-соединение. И дальше уже различные стримы проходят по нему. Здесь можно 
заметить, что соединение между клиентом и балансером устанавливается один раз и остается персистентным, а дальше балансер уже на каждый вызов балансирует 
нагрузку на разные бэкенды.

## Работа с таймаутами

```java
Response response = blockingStub.withDeadlineAfter(deadlineMs, TimeUnit.MILLISECONDS).sayHello(request);
```

Это устанавливает дедлайн на `100мс` с момента, когда клиентский `RPC` установлен до момента, когда клиент принимает ответ.

На стороне сервера сервер может запросить, нужно ли всё еще выполнять `RPC` вызов. Перед тем, как сервер начнет работу над ответом, очень важно проверить, есть 
ли еще клиент, ожидающий его. Это особенно важно сделать перед началом дорогостоящей обработки.

```java
if (Context.current().isCancelled()) {
  responseObserver.onError(Status.CANCELLED.withDescription("Cancelled by client").asRuntimeException());
  return;
}
```
 
Нужно ли серверу продолжать выполнение запроса, если вы знаете, что ваш клиент достиг отвалился по таймауту? Зависит от ситуации. Если ответ можно кэшировать 
на сервере, стоит его обработать и кэшировать; особенно если он требует больших ресурсов и требует денег за каждый запрос. Это ускорит выполнение будущих 
запросов, поскольку результат уже будет доступен.

Что, если вы установили таймаут, но новая версия или серверная версия вызывает плохой регресс? Таймаут может быть слишком маленьким, что приведет к тому, что 
все ваши запросы истекут с `DEADLINE_EXCEEDED`, или слишком большим, и задержка вашего пользовательского запроса теперь огромна. Вы можете использовать флаг, 
чтобы установить и отрегулировать таймаут.

```java
@Option(name="--deadline_ms", usage="Deadline in milliseconds.")
private int deadlineMs = 20*1000;

response = blockingStub.withDeadlineAfter(deadlineMs, TimeUnit.MILLISECONDS).sayHello(request);
```

Теперь таймаут можно отрегулировать, чтобы ждать дольше, чтобы избежать сбоя. Это позволяет смягчить проблему для пользователей до тех пор, пока регресс не 
будет устранен и устранен.

## Grpc vs Rest

![Screenshot](../../resources/rest_vs_grpc.png)

* **Построен на HTTP 2 вместо HTTP 1.1**
* **Protobuf (строгая типизация) вместо JSON / XML**
* **Встроенная генерация кода вместо использования сторонних (third-party) инструментов**
* **Двухсторонняя потоковая передача данных, наряду с традиционной (унарной)**
* **Передача сообщений в 7-10 раз быстрее**

### Когда лучше использовать REST или gRPC?

Давайте сравним, когда вам следует рассмотреть возможность использования REST API и gRPC API:

### Когда использовать REST API

Независимо от того, создаете ли вы внутреннюю систему или открытую систему, которая предоставляет свои ресурсы остальному миру, REST API, вероятно, останутся фактическим выбором для интеграции приложений в течение очень долгого времени. Кроме того, REST API идеально подходят, когда системе требуется высокоскоростная итерация и стандартизация протокола HTTP. Благодаря универсальной поддержке сторонних инструментов REST API должны быть вашим первым аргументом в пользу интеграции приложений, интеграции микросервисов и разработки веб-сервисов.

### Когда использовать gRPC API

Что касается gRPC, в большинстве сторонних инструментов по-прежнему отсутствуют встроенные функции для совместимости с gRPC. Таким образом, gRPC в основном используется для создания внутренних систем, то есть инфраструктуры, закрытой для внешних пользователей. С учетом этого предостережения, API-интерфейсы gRPC могут быть полезны в следующих случаях:

* Соединения с микросервисами: связь с низкой задержкой и высокой пропускной способностью gRPC делает его особенно полезным для подключения архитектур, состоящих из легких микросервисов, где эффективность передачи сообщений имеет первостепенное значение.
* Системы где используется несколько языков программирования: благодаря поддержке генерации собственного кода для широкого спектра языков разработки, gRPC отлично подходит для управления соединениями в среде с наличием нескольких языков.
* Потоковая передача в реальном времени: когда требуется связь в реальном времени, способность gRPC управлять двунаправленной потоковой передачей позволяет вашей системе отправлять и получать сообщения в режиме реального времени, не дожидаясь ответа отдельного клиента.
* Сети с низким энергопотреблением и низкой пропускной способностью: использование gRPC сериализованных сообщений Protobuf обеспечивает легкий обмен сообщениями, большую эффективность и скорость для сетей с ограниченным диапазоном пропускания и маломощных сетей (особенно по сравнению с JSON). Интернет вещей может быть примером такой сети, в которой могут быть полезны API gRPC.

## Полезные ссылки

[gRPC в качестве протокола межсервисного взаимодействия. Доклад Яндекса - habr](https://habr.com/ru/company/yandex/blog/484068/)

[gRPC and Deadlines - grpc.io](https://grpc.io/blog/deadlines/)

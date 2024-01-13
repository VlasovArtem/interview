## `Facade (Фасад)`

Фасад — это структурный паттерн проектирования, который предоставляет простой интерфейс к сложной системе классов, 
библиотеке или фреймворку.

### `Проблема`

Вашему коду приходится работать с большим количеством объектов некой сложной библиотеки или фреймворка. 
Вы должны самостоятельно инициализировать эти объекты, следить за правильным порядком зависимостей и так далее.

В результате бизнес-логика ваших классов тесно переплетается с деталями реализации сторонних классов. 
Такой код довольно сложно понимать и поддерживать.

### `Решение`

Фасад — это простой интерфейс для работы со сложной подсистемой, содержащей множество классов. 
Фасад может иметь урезанный интерфейс, не имеющий 100% функциональности, которой можно достичь, используя сложную 
подсистему напрямую. Но он предоставляет именно те фичи, которые нужны клиенту, и скрывает все остальные.

### `Структура`

![Alt text](https://refactoring.guru/images/patterns/diagrams/facade/example-2x.png)

### `Применимость`

- Когда вам нужно представить простой или урезанный интерфейс к сложной подсистеме.
- Когда вы хотите разложить подсистему на отдельные слои.

### `Источники`

- [Подробнее о паттерне на refactoring.guru](https://refactoring.guru/ru/design-patterns/composite)

- [Реализация на Java](https://refactoring.guru/ru/design-patterns/composite/java/example)
<!-- TOC -->
* [Enum](#enum)
  * [Может ли Enum наследовать (implement) интерфейс в Java?](#может-ли-enum-наследовать-implement-интерфейс-в-java)
  * [Может ли Enum наследовать (extends) класс?](#может-ли-enum-наследовать-extends-класс)
  * [Как создать Enum без экземпляров объектов? Возможно ли это без ошибки компиляции?](#как-создать-enum-без-экземпляров-объектов-возможно-ли-это-без-ошибки-компиляции)
  * [Можем ли мы переопределить метод toString() для Enum? Что будет, если не будем переопределять?](#можем-ли-мы-переопределить-метод-tostring-для-enum-что-будет-если-не-будем-переопределять)
  * [Можем ли мы создать экземпляр Enum вне Enum? Почему нет?](#можем-ли-мы-создать-экземпляр-enum-вне-enum-почему-нет)
  * [Можем ли мы указать конструктор внутри Enum?](#можем-ли-мы-указать-конструктор-внутри-enum)
  * [Какая разница сравнивать Enum при помощи == или метода equals()?](#какая-разница-сравнивать-enum-при-помощи--или-метода-equals)
      * [Использование == для сравнения Enum может предотвратить исключение NullPointerException](#использование--для-сравнения-enum-может-предотвратить-исключение-nullpointerexception)
      * [== метод обеспечивает безопасность типов во время компиляции](#-метод-обеспечивает-безопасность-типов-во-время-компиляции)
      * [== должен быть быстрее, чем метод equals](#-должен-быть-быстрее-чем-метод-equals)
  * [Что делает метод ordinal() в Enum?](#что-делает-метод-ordinal-в-enum)
  * [Можно использовать Enum с TreeSet или TreeMap в Java?](#можно-использовать-enum-с-treeset-или-treemap-в-java)
  * [Какая разница между ordinal() и compareTo() в Enum?](#какая-разница-между-ordinal-и-compareto-в-enum)
  * [Как пройтись по всему экземпляру Enum?](#как-пройтись-по-всему-экземпляру-enum)
  * [Какие плюсы и минусы использования Enum в качестве синглтона?](#какие-плюсы-и-минусы-использования-enum-в-качестве-синглтона)
  * [Полезные ссылки](#полезные-ссылки)
<!-- TOC -->

# Enum

`Enum` в Java - это ключевое слово, функция, которая используется для представления фиксированного числа заранее известных значений в Java.

## Может ли Enum наследовать (implement) интерфейс в Java?

Да, `Enum` может наследовать интерфейсы. Поскольку `Enum` тип схож с классом и интерфейсом, он может наследовать интерфейс и переопределить любой метод. 
Это даёт поразительную гибкость в использовании `Enum` в качестве специальной реализации в некоторых случаях. Также стоит отметить, что `Enum` в Java неявно 
реализует как `Serializable`, так и `Comparable` интерфейс. 

Вот неплохой пример использования `Enum` в таком качестве:

```java
public enum Currency implements Runnable{ 
  PENNY(1), NICKLE(5), DIME(10), QUARTER(25); 
  private int value; 

  @Override public void run() { 
    System.out.println("Enum in Java implement interfaces"); 
  } 
}
```

## Может ли Enum наследовать (extends) класс?

Нет, не может! Неожиданно, поскольку ранее говорилось что `Enum` тип похож на класс или интерфейс в Java. Ну, это главная причина, почему такой вопрос задают
сразу за предыдущим. Поскольку `Enum` уже наследуется от абстрактного класса `java.lang.Enum`, понятно, что другой класс наследовать не удастся, поскольку 
Java не поддерживает множественное наследование классов. Благодаря наследованию от `java.lang.Enum`, все перечисления имеют методы `ordinal()`, `values()` 
или `valueOf()`.

## Как создать Enum без экземпляров объектов? Возможно ли это без ошибки компиляции?

Это один из тех хитрых вопросов, которые так любят интервьюеры. Поскольку `Enum` видится коллекцией определённого количества объектов, как дни недели или 
месяцы в году получить `Enum` без ничего кажется подозрительным. Но да, вы можете создать `Enum` без экземпляров, например создавая утилитарный класс. 
Это ещё один инновационный способ использовать `Enum`:

```java
public enum MessageUtil{
  ;  // required to avoid compiler error, also signifies no instance
  
  public static boolean isValid() {
    throw new UnsupportedOperationException("Not supported yet.");
  }
}
```

## Можем ли мы переопределить метод toString() для Enum? Что будет, если не будем переопределять?

Конечно вы можете переопределить метод `toString()` у `Enum`, как и любого класса, наследующего `java.lang.Object` и имеющего метод `toString()` в доступности,
и даже если вы не станете этого делать, ничего не потеряете, поскольку абстрактная основа класса `Enum` сделает это за вас, и вернёт имя, являющееся именем
экземпляра `Enum`. Вот код метода `toString()` из класса `Enum`:

```java
public String toString() {
  return name;
}
```

`name` задано, когда компилятор выделяет код для создания перечисления в ответ на создание экземпляра в самом классе `Enum`, наравне с созданием порядкового
числительного в конструкторе из класса `java.lang.Enum`:

```java
protected Enum(String name, int ordinal) {
  this.name = name;
  this.ordinal = ordinal;
}
```

Это единственный конструктор для создания перечисления, который вызывается компилятором в ответ на декларирование `Enum` в программе.


## Можем ли мы создать экземпляр Enum вне Enum? Почему нет?

Вы не можете создавать экземпляры `Enum` вне границ `Enum`, поскольку у `Enum` нет `public` конструктора, и компилятор не позволит вам внести любой подобный
конструктор. Так как компилятор генерирует большинство кода в ответ на декларацию `Enum` типа, он не допускает `public` конструкторов внутри `Enum`, 
что заставляет объявлять экземпляры `Enum` внутри себя.

## Можем ли мы указать конструктор внутри Enum?

Этот вопрос часто следует за предыдущим. Да, вы можете, но помните, что подобное возможно лишь с указанием `private` или `package-private` конструкторов. 
Конструкторы с `public` и `protected` — не допустимы в `Enum`.

```java
public enum Currency {
        PENNY(1), NICKLE(5), DIME(10), QUARTER(25);
        private int value;

        private Currency(int value) {
                this.value = value;
        }
};  
```

## Какая разница сравнивать Enum при помощи == или метода equals()?

`equals()` метод `java.lang.Enum` использует оператор `==`, чтобы проверить, равны ли два перечисления. Это означает, что вы можете сравнивать `Enum`, 
используя как `==`, так и метод `equals()`. Также метод `equals()` объявлен как `final` внутри `java.lang.Enum`, поэтому существует риск переопределения 
метода `equals()`.

Метод equals из класса `Enum`:
```java
public final boolean equals(Object other) { 
  return this==other;
}
```

Между прочим, есть тонкая разница, когда вы сравниваете `enum` способом `==` или `equals()`, которая происходит от того, что `==` (оператор равенства) является
оператором, а `equals()` - методом. Некоторые из этих моментов уже обсуждаются в различиях между `equals()` и `==` в Java, но мы увидим их здесь при сравнении 
`Enums` в Java.

#### Использование == для сравнения Enum может предотвратить исключение NullPointerException

Если вы сравните любое `Enum` с `null`, используя оператор `==`, это приведет к `false`, но если вы используете метод `equals()` для этой проверки, вы можете 
получить исключение `NullPointerException`, если вы не используете equals правильным образом. Посмотрите на код ниже, здесь мы сравниваем неизвестный объект 
`Shape` с перечислением `Shape`, которое содержит `CIRCLE`, `RECTANGLE` и т. Д.

```java
private enum Shape{ 
  RECTANGLE, SQUARE, CIRCLE, TRIANGLE; 
} 

private enum Status{ ON, OFF; } 

Shape unknown = null; 
Shape circle = Shape.CIRCLE; 

boolean result = unknown == circle; //return false 
result = unknown.equals(circle); //throws NullPointerException
```

Я согласен, что этого можно избежать, просто сравнивая известное с неизвестным, то есть `circle.equals(unknown)`, но это одна из наиболее распространенных
ошибок кодирования, которые делают программисты Java. Используя `==` для сравнения enum, вы можете полностью избежать этого.

#### == метод обеспечивает безопасность типов во время компиляции

Еще одно преимущество использования `==` для сравнения `enum` - безопасность времени компиляции. Оператор равенства или `==` проверяет, относятся ли оба
объекта перечисления к одному типу перечисления или нет, во время самой компиляции, в то время как метод `equals()` также вернет `false`, но во время 
выполнения. Поскольку всегда лучше обнаруживать ошибки во время компиляции, `==` набирает больше очков в случае сравнения `enum`.

#### == должен быть быстрее, чем метод equals

Это больше из здравого смысла, оператор `==` должен выполняться быстрее, чем вызов метода, и вызов оператора `==` . Хотя я считаю, что современный JIT-
компилятор может встроить метод `equals()`, когда вы сравниваете два перечисления в Java. Это означает, что это не будет большой разницей с точки зрения
производительности, но я думаю, что без какой-либо хитрости со стороны компилятора или JVM `==` всегда должен работать быстрее.

## Что делает метод ordinal() в Enum?

Метод `ordinal()` возвращает порядок, в котором экземпляры `Enum` обозначены внутри `Enum`. Например, в `DayOfWeek Enum`, вы можете указать дни по порядку:

```java
public enum DayOfWeek{
  MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
}
```

и если мы вызовем метод `DayOfWeek.MONDAY.ordinal()`,  он вернёт `0` - что значит первый экземпляр. Этот метод весьма полезен для предоставления порядка в
соответствии с реальным положением вещей: т. е. указывая, что `TUESDAY` (вторник) идёт после `MONDAY` (понедельника), и перед `WEDNESDAY` (средой). Точно 
так же вы можете использовать перечисление для представления месяцев года, где `Февраль` идёт после `Января`, но предшествует `Марту`. Все пользовательские 
перечисления наследуют этот метод из абстрактного класса `java.lang.Enum`, и они устанавливаются компилятором, вызывая `protected` конструктор из 
`java.lang.Enum`, который принимает имя и порядковый номер.

## Можно использовать Enum с TreeSet или TreeMap в Java?

Это действительно интересный вопрос по `Enum`, и его любят задавать для проверки глубины знаний. Пока вы не загляните в код `java.lang.Enum`, вы скорей всего
не будете знать, что `Enum` наследует интерфейс `Comparable`, который является главным требованием для использования в упорядоченных коллекциях, как `TreeSet`
и `TreeMap`. Поскольку `Enum` по умолчанию наследует интерфейс `Comparable`, он может использоваться с `TreeSet` и `TreeMap`.

## Какая разница между ordinal() и compareTo() в Enum?

Это прямое следование из предыдущего вопроса: на самом деле, `compareTo()` имитирует порядок, предоставляемый методом `ordinal()`, являющийся естественным
порядком `Enum`. Если коротко, `Enum` ограничивает сравнения в порядке их объявления. Так же, стоит помнить, что данные константы сравнимы только с другими
константами того же типа — сравнение разных типов констант может привести к ошибке компилятора.

## Как пройтись по всему экземпляру Enum?

Если вы открывали `java.lang.Enum`, то знаете, что метод `values()` возвращает массив всех констант `Enum`. Поскольку каждое перечисление наследует 
`java.lang.Enum`, они имеют метод `values()`. Используя его, вы можете пройтись по всем константам перечисления определённого типа.

## Какие плюсы и минусы использования Enum в качестве синглтона?

`Enum` предоставляет быстрый ярлык для воплощения паттерна синглтона, и поскольку об этом сказано даже в книге «Эффективная Java», такой выбор весьма 
популярен. На первый взгляд, синглтон `Enum` многообещающ и весьма удобен, например  контролирует создание экземпляра, безопасно сериализуется и прежде всего,
легко создать потокобезопасный синглтон с использованием `Enum`. Вам не нужно больше заботиться о двойной проверке волатильности переменных. Более подробно о 
плюсах и минусах использования такого подхода [тут](https://javarevisited.blogspot.com/2012/07/why-enum-singleton-are-better-in-java.html).

## Полезные ссылки

[How to Compare Two Enum in Java - Equals vs == vs CompareT - javarevisited](https://javarevisited.blogspot.com/2013/04/how-to-compare-two-enum-in-java-equals.html)

[Java Enum Tutorial: 10 Examples of Enum in Java - javarevisited](https://javarevisited.blogspot.com/2012/07/why-enum-singleton-are-better-in-java.html)

[Enum singleton - javarevisited](https://javarevisited.blogspot.com/2012/07/why-enum-singleton-are-better-in-java.html)

[15 вопросов для собеседования разработчиков, относительно Enum - Javarush](https://javarush.ru/groups/posts/1353-15-voprosov-dlja-sobesedovanija-razrabotchikov-otnositeljhno-enum-v-dzhave-s-otvetami)
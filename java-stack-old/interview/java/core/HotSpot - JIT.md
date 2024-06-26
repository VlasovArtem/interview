<!-- TOC -->
* [HotSpot - JIT (Just-in-time) compiler](#hotspot---jit-just-in-time-compiler)
  * [Что такое JIT-компилятор?](#что-такое-jit-компилятор)
  * [HotSpot JIT компилятор](#hotspot-jit-компилятор)
  * [Друге реализации JVM](#друге-реализации-jvm)
  * [Полезные ссылки](#полезные-ссылки)
<!-- TOC -->

# HotSpot - JIT (Just-in-time) compiler

## Что такое JIT-компилятор?
Язык Java предлагает смешанный подход, нечто среднее между компилируемыми и интерпретируемыми языками. 
Приложения на языке Java компилируются в промежуточный низкоуровневый код — байт-код (bytecode).

Когда вы запускаете команду `javac` или `compile-on-save` в `IDE`, ваша программа на `Java` компилируется из `Java-кода` в `байткод JVM`, который является бинарным 
представлением программы. Он более компактен и прост, чем исходный `Java-код`. Однако, обычный процессор вашего ноутбука или сервера не может просто так выполнить 
`байткод JVM`.

Для работы вашей программы `JVM` интерпретирует этот байткод. Интерпретаторы, обычно, значительно медленнее, чем машинный код запускаемый на процессоре. По этой 
причине `JVM`, во время работы программы, может запустить еще один компилятор, который преобразует ваш байткод в машинный код, выполнить который процессор уже в 
состоянии.

Этот компилятор, обычно, намного более изощрённый, чем `javac`, выполняет сложные оптимизации чтобы в результате выдать высококачественный машинный код.

## HotSpot JIT компилятор

В различных реализациях JVM JIT компилятор может быть реализован по-разному. 

В данной статье мы рассматриваем `Oracle HotSpot JVM` и ее реализацию JIT компилятора. 
Название HotSpot происходит от подхода, используемого в JVM для компиляции байт-кода. 

Обычно в приложении только небольшие части кода выполняются достаточно часто и производительность приложения в основном зависит от скорости выполнения именно этих частей. 
Эти части кода называются горячими точками (hot spots), их и компилирует JIT компилятор. В основе этого подхода лежит несколько суждений. 

Если код будет исполнен всего один раз, то компиляция этого кода — пустая трата времени. 
Другая причина — это оптимизации. Чем больше раз JVM исполняет какой либо код, тем больше статистики она накапливает, используя которую можно сгенерировать более оптимизированный код. 

К тому же компилятор разделяет ресурсы виртуальной машины с самим приложением, поэтому ресурсы затраченные на профилирование и оптимизацию могли бы быть использованы для исполнения самого приложения, что заставляет соблюдать определенный баланс. 

Единицей работы для `HotSpot` компилятора является метод и цикл.

> Единица скомпилированного кода называется nmethod (сокращение от native method).

Hotspot JIT (Just-In-Time) является реализацией виртуальной машины Java (JVM) и является частью пакета разработки Java (JDK), начиная с версии JDK 1.3. 

Он является стандартной реализацией JVM в JDK от Sun Microsystems (теперь Oracle Corporation) и является частью большинства популярных дистрибутивов JDK.

Версии Java, которые используют Hotspot JIT реализацию JVM, включают в себя:

* Java SE 1.3.x
* Java SE 1.4.x
* Java SE 5.0 (также известный как Java 1.5)
* Java SE 6 (также известный как Java 1.6)
* Java SE 7
* Java SE 8
* Java SE 9
* Java SE 10
* Java SE 11
* Java SE 12
* Java SE 13
* Java SE 14
* Java SE 15
* Java SE 16
* Java SE 17

Таким образом, все основные версии Java, выпущенные в последние двадцать лет, используют Hotspot JIT реализацию JVM.

## Друге реализации JVM

Помимо Hotspot JIT, существует несколько других реализаций виртуальной машины Java (JVM), которые были разработаны различными компаниями и сообществами. 
Некоторые из них включают в себя:

1. `OpenJ9 JVM`: OpenJ9 это реализация виртуальной машины Java с открытым исходным кодом, которую разработала компания IBM. Она была создана для увеличения производительности и снижения потребления памяти. OpenJ9 используется в различных продуктах IBM, таких как WebSphere Application Server и IBM Cloud.
2. `Azul Zing`: Azul Zing это JVM, разработанная компанией Azul Systems. Она специализируется на работе с крупными и сложными приложениями Java, где высокая производительность и предсказуемость работы критически важны. Azul Zing также имеет механизмы оптимизации памяти и управления сборкой мусора.
3. `GraalVM`: GraalVM это экспериментальная реализация виртуальной машины Java, разработанная компанией Oracle. Она имеет ряд инновационных функций, таких как возможность запуска различных языков программирования на одной платформе и возможность компиляции Java-кода в нативный код.
4. `JRockit`: JRockit это JVM, которую разработала компания BEA Systems. Позже BEA была приобретена Oracle, и JRockit стала частью пакета Oracle Fusion Middleware. JRockit была специально создана для обеспечения высокой производительности и предсказуемости работы Java-приложений в крупных корпоративных средах.

Это только некоторые из возможных реализаций JVM, которые могут быть использованы для запуска Java-приложений.

## Полезные ссылки

* [Java HotSpot JIT компилятор — устройство, мониторинг и настройка (часть 1) - habr](https://habr.com/ru/post/536288/)
* [Java HotSpot JIT компилятор — устройство, мониторинг и настройка (часть 2) - habr](https://habr.com/ru/post/536514/)

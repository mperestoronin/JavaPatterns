# Стратегия strategy
### Краткое описание
Этот шаблон предназначен для определения группы классов, которые представляют собой набор возможных вариантов поведения. Это дает возможность гибко подключать те или
иные наборы вариантов поведения во время работы приложения, меняя его функциональность "на ходу".

Простым языком - этот шаблон дает возможность в процессе выполнения выбрать стратегию (алгоритм, инструмент, подход) решения задачи. Он позволяет вынести семейство схожих алгоритмов, решающих конкректную задачу. Реализация алгоритмов выносится в отдельные классы и предоставляется возможность выбирать алгоритмы во время выполнения программы

![](https://habrastorage.org/r/w780/getpro/habr/post_images/8d8/303/cdb/8d8303cdbc70de33f376454c2eb6934a.jpg)
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/strategy1.png)

### Классы и интерфейсы
- Client - объект, меняющий свое поведение в зависимости от стратегии. Имеет поле под нужную стратегию, а также метод performOperation, который вызывает метод operation той стратегии, которая на момент вызова метода лежит в поле этого класса.
- интерфейс strategy - обязующий реализовать метод operation (выполнение какого-то действия)
- ConcreteStrategyA, ConcreteStrategyB ... (стратегий может быть сколько угодно) - реализует интерфейс strategy, содержит свою уникальную реализацию метода operation.

### Применение 
Данный шаблон проектирования следует применять когда:
- У вас есть множество похожих реализаций отличающихся незначительным поведением. Можно вынести отличающее поведение в классы-стратегии, а повторяющий код свести к единому классу-контекста. 

- Ваш алгоритм реализован в супер-классе с множественными условными операторами. Выделите блоки условных операторов в отдельные классы-стратегии, а управление вызовов нужных доверьте классу-контекста. 

- Конкретные стратегии позволяют инкапсулировать алгоритмы в своих конкретных классах. Используйте этот подход для снижения зависимостей от других классов. 

- В зависимости от ситуации вы можете менять стратегию выполнения задачи в процессе выполнения программы. Например, в зависимости от скорости интернета использовать разные стратегии-поведения, возвращающие разный набор данных для отображения страницы.

### Детальный разбор
Представим, что нам необходимо написать программу, обрабатывающую изображения. В этой программе необходимо реализовать несколько фильтров, каждый фильтр фильтрует изображение по разному. Если бы мы использовали наследование, то нам бы пришлось делать родительский класс Filter с методом filterImg, от которого бы потом наследоватлись конкретные фильтры. Но т.к. каждый фильр имеет разный алгоритм, реализация filterImg для каждого наследника будет разной (каждый наследник будет переопределять этот метод). Таким образом мы полностью теряем одно из главных приемуществ ООП - возможность не дублировать код. Рассмотрим эту задачу под другим углом. Пусть функция фильтрации будет частью поведения объекта. Мы можем создать интерфейс FilterStrategy, содержащий метод обработки изображения filterImg. Каждый конкретный фильтр должен будет этот интерфейс реалирзовывать (описывать свой уникальный метод обработки filterImg). Потом создадим класс Filter и дадим ему поле типа FilterStategy, описывающее тип фильтрации (стратегию), а также единственный метод - applyFilter. applyFilter вызывает filterImg того фильтра, который сейчас хранится в поле класса. Таким образом наш класс Filter  меняет свое поведение в зависимости от значения поля (выбранной стратегии).

### Реализация
Пример про фильтры на swift
``` swift
import UIKit

final class Filter {
    var filterStrategy: FilterStrategy?
    
    func applyFilter(to image: UIImage) {
        filterStrategy?.process(image: image)
    }
}

protocol FilterStrategy {
    func process(image: UIImage) -> UIImage
}

final class SepiaFilter: FilterStrategy {
    func process(image: UIImage) -> UIImage {
        print("Apply SEPIA filter to image")
        return image
    }
}

final class BWFilter: FilterStrategy {
    func process(image: UIImage) -> UIImage {
        print("Apply B&W filter to image")
        return image
    }
}

final class DistortionFilter: FilterStrategy {
    func process(image: UIImage) -> UIImage {
        print("Apply DISTORTION filter to image")
        return image
    }
}

let filter = Filter()
let image = UIImage()

filter.filterStrategy = SepiaFilter()
filter.applyFilter(to: image)

filter.filterStrategy = BWFilter()
filter.applyFilter(to: image)

filter.filterStrategy = DistortionFilter()
filter.applyFilter(to: image)
```
Реализация на java
``` java
// Класс реализующий конкретную стратегию, должен реализовывать этот интерфейс
// Класс контекста использует этот интерфейс для вызова конкретной стратегии
interface Strategy {
    int execute(int a, int b); 
}

// Реализуем алгоритм с использованием интерфейса стратегии
class ConcreteStrategyAdd implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategyAdd's execute()");
        return a + b;  // Do an addition with a and b
    }
}
 
class ConcreteStrategySubtract implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategySubtract's execute()");
        return a - b;  // Do a subtraction with a and b
    }
}
 
class ConcreteStrategyMultiply implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategyMultiply's execute()");
        return a * b;   // Do a multiplication with a and b
    }    
}

// Класс контекста использующий интерфейс стратегии
class Context {
 
    private Strategy strategy;
 
    // Constructor
    public Context() {
    }

    // Set new strategy
    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public int executeStrategy(int a, int b) {
        return strategy.execute(a, b);
    }
}
 
// Тестовое приложение
class StrategyExample {
 
    public static void main(String[] args) {
 
        Context context = new Context();
 
        context.setStrategy(new ConcreteStrategyAdd());
        int resultA = context.executeStrategy(3,4);
 
        context.setStrategy(new ConcreteStrategySubtract());
        int resultB = context.executeStrategy(3,4);
 
        context.setStrategy(new ConcreteStrategyMultiply());
        int resultC = context.executeStrategy(3,4);

        System.out.println("Result A : " + resultA );
        System.out.println("Result B : " + resultB );
        System.out.println("Result C : " + resultC );
    }
}
```

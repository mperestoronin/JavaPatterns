### Прототип prototype
### Краткое описание
Задаёт виды создаваемых объектов с помощью экземпляра-прототипа и создаёт новые объекты путём копирования этого прототипа. Он позволяет уйти от реализации и позволяет следовать принципу «программирование через интерфейсы». В качестве возвращающего типа указывается интерфейс/абстрактный класс на вершине иерархии, а классы-наследники могут подставить туда наследника, реализующего этот тип.

Проще говоря, это паттерн создания объекта через клонирование другого объекта вместо создания через конструктор.

![](https://upload.wikimedia.org/wikipedia/ru/2/25/Prototype.gif)

![](https://habrastorage.org/r/w1560/getpro/habr/post_images/a9d/715/4a9/a9d7154a9b7e321a6330ab0c0337c061.jpg)

### Применение 
Данный паттерн позволяет:
- избежать дополнительных усилий по созданию объекта стандартным путём (имеется в виду использование конструктора, так как в этом случае также будут вызваны конструкторы всей иерархии предков объекта), когда это непозволительно дорого для приложения.
- избежать наследования создателя объекта (object creator) в клиентском приложении, как это делает паттерн abstract factory.

Используйте этот шаблон проектирования, когда системe безразлично как именно в ней создаются, компонуются и представляются продукты:

- инстанцируемые классы определяются во время выполнения, например с помощью динамической загрузки;
- избежать построения иерархий классов или фабрик, параллельных иерархии классов продуктов;
- экземпляры класса могут находиться в одном из нескольких различных состояний. Может оказаться удобнее установить соответствующее число прототипов и клонировать их, а не инстанцировать каждый раз класс вручную в подходящем состоянии.
### Реализация
``` java
/**
 * Prototype Class
 */
public class Cookie implements Cloneable {
    
    protected int weight;

    @Override
    public Cookie clone() throws CloneNotSupportedException {
        Cookie copy = (Cookie) super.clone();
       
        //In an actual implementation of this pattern you might now change references to
        //the expensive to produce parts from the copies that are held inside the prototype.

        return copy;
    }
}

/**
 * Concrete Prototypes to clone
 */
public class CoconutCookie extends Cookie { }
/**
 * Client Class
 */
public class CookieMachine {

    private Cookie cookie; // Could have been a private Cloneable cookie.

    public CookieMachine(Cookie cookie) {
        this.cookie = cookie;
    }

    public Cookie makeCookie() throws CloneNotSupportedException {
        return (Cookie) this.cookie.clone();
    }

    public static void main(String args[]) throws CloneNotSupportedException {
        Cookie tempCookie = null;
        Cookie prot = new CoconutCookie();
        CookieMachine cm = new CookieMachine(prot);
        for (int i = 0; i < 100; i++)
            tempCookie = cm.makeCookie();
    }
}
```

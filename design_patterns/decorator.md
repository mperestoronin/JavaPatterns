# Декоратор Decorator
### Краткое описание
Декоратор расширяет функциональность основного класса путем добавления дополнительного кода в начале, конце или вместо существующей реализации.
![](https://habrastorage.org/r/w1560/getpro/habr/post_images/15c/27c/26e/15c27c26e08f1936e3f73089ecac3d05.jpg)
![](https://media.geeksforgeeks.org/wp-content/uploads/uml.jpg)
### Классы и интерфейсы
- интерфейс component - содержит единственный метод operation. 
- ConcreteComponent - реализует component. 
- Decorator - также реализует Component. Дополняет функциональность, которая содержится в методе operation (в классе ConcreteComponent). Содержит ссылку на объект, реализующий интерфейс component (ромбовидная стрелка на диаграме). Т.е декоратор как бы внешнаяя оболочка над Concretecomponent. Это позволит в декоратору в своем методе operation вызывать метод operation конкректного компонента, а также добавлять туда что-то от себя.

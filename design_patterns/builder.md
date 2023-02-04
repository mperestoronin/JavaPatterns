# Строитель builder
### Краткое описание
Отделяет конструирование сложного объекта от его представления так, что в результате одного и того же процесса конструирования могут получаться разные представления.
![](https://habrastorage.org/r/w1560/getpro/habr/post_images/16b/2fe/a7f/16b2fea7f7f4dcd14fe2ad0b0bb9bf84.jpg)
![](https://upload.wikimedia.org/wikipedia/ru/2/28/Builder.gif)
### Классы и интерфейсы
- Director - класс, контролирующий строительство product ConcreteBuilder'ом.
- интерфейс Builder - Определяет все этапы, необходимые для постройки product.
- ConcreteBuilder - строитель product. Может быть несколько ConcreteBuilder'ов, если в программе есть нужда в создании объекта разнымим способами / создании объекта с разными характеристиками.
- product - объект, который мы пытаемся "построить".
### Детальный разбор
Итоговая цель шаблона - создать объект (будем называть его product). Этот product будет построен при поощи другого
объекта (ConcreteBuilder). Т.е. мы не будем напрямую вызывать конструкторы класса product.
Существует 2 вариации этого шаблона - простой и сложный. В простом будет всего два объекта ConcreteBuilder и product. Во втором более сложном варианте потребуются все вышеописанные классы и интерфейсы.
Этот шаблон  позволяет поэтапно создавать сложные объекты с помощью четко определенной последовательности действий. Строительство контролируется объектом-распорядителем (director), которому нужно знать только тип создаваемого объекта.

Данный паттерн применяется, когда в создание объекта вложена сложная логика.

### Реализация
Пример на C#
``` cs
class Client
{
    void Main()
    {
        Builder builder = new ConcreteBuilder();
        Director director = new Director(builder);
        director.Construct();
        Product product = builder.GetResult();
    }
}
class Director
{
    Builder builder;
    public Director(Builder builder)
    {
        this.builder = builder;
    }
    public void Construct()
    {
        builder.BuildPartA();
        builder.BuildPartB();
        builder.BuildPartC();
    }
}
 
abstract class Builder
{
    public abstract void BuildPartA();
    public abstract void BuildPartB();
    public abstract void BuildPartC();
    public abstract Product GetResult();
}
 
class Product
{
    List<object> parts = new List<object>();
    public void Add(string part)
    {
        parts.Add(part);
    }
}
 
class ConcreteBuilder : Builder
{
    Product product = new Product();
    public override void BuildPartA()
    {
        product.Add("Part A");
    }
    public override void BuildPartB()
    {
        product.Add("Part B");
    }
    public override void BuildPartC()
    {
        product.Add("Part C");
    }
    public override Product GetResult()
    {
        return product;
    }
}
```



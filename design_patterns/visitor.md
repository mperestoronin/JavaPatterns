# Посетитель visitor
### Краткое описание
Данный паттерн позволяет определить операцию для объектов других классов без изменения этих классов.

### Классы и интерфейсы
- Visitor: интерфейс посетителя, который определяет метод Visit() для каждого объекта Element
- ConcreteVisitor1 / ConcreteVisitor2: конкретные классы посетителей, реализуют интерфейс, определенный в Visitor.
- Element: определяет метод Accept(), в котором в качестве параметра принимается объект Visitor
- ElementA / ElementB: конкретные элементы, которые реализуют метод Accept()
- ObjectStructure: некоторая структура, которая хранит объекты Element и предоставляет к ним доступ. Это могут быть и простые списки, и сложные составные структуры в виде дер

### Более детальный разбор
Как уже писалось ранее, данный шаблон описывает операцию, которая производится над объектами других классов, не изменяя эти классы. Эта операция как правило имеет общий смысл или одну цель для всех объектов этих классов, но реализуется для кадого из них по разному. При этом, нет доступа или возможности ихменять эти классы или не хочется добавлять им дополнительную ответственность. Представим, что у нас есть некая структура объектов. Эти объекты могут быть разных типов и с разными интерфейсами. Требуется произвести над этими объектами определенные операции, не меняя код их классов. Данный шаблон предлагает ввести иерархию или интерфейс для объектов, описывающих эти классы. Т.е. создается интерфейс посетитель, где 

### Применение
Когда использовать данный паттерн?

- Когда имеется много объектов разнородных классов с разными интерфейсами, и требуется выполнить ряд операций над каждым из этих объектов

- Когда классам необходимо добавить одинаковый набор операций без изменения этих классов

- Когда часто добавляются новые операции к классам, при этом общая структура классов стабильна и практически не изменяется

### Реализация
Пример на C#
``` cs
class Client
{
    void Main()
    {
        var structure = new ObjectStructure();
        structure.Add(new ElementA());
        structure.Add(new ElementB());
        structure.Accept(new ConcreteVisitor1());
        structure.Accept(new ConcreteVisitor2());
    }
}
 
abstract class Visitor
{
    public abstract void VisitElementA(ElementA elemA);
    public abstract void VisitElementB(ElementB elemB);
}
 
class ConcreteVisitor1 : Visitor
{
    public override void VisitElementA(ElementA elementA)
    {
        elementA.OperationA();
    }
    public override void VisitElementB(ElementB elementB)
    {
            elementB.OperationB();
    }
}
class ConcreteVisitor2 : Visitor
{
    public override void VisitElementA(ElementA elementA)
    {
        elementA.OperationA();
    }
    public override void VisitElementB(ElementB elementB)
    {
        elementB.OperationB();
    }
}
 
class ObjectStructure
{
    List<Element> elements = new List<Element>();
    public void Add(Element element)
    {
        elements.Add(element);
    }
    public void Remove(Element element)
    {
        elements.Remove(element);
    }
    public void Accept(Visitor visitor)
    {
        foreach (Element element in elements)
            element.Accept(visitor);
    }
}
 
abstract class Element
{
    public abstract void Accept(Visitor visitor);
    public string SomeState { get; set; }
}
 
class ElementA : Element
{
    public override void Accept(Visitor visitor)
    {
        visitor.VisitElementA(this);
    }
    public void OperationA()
    { }
}
 
class ElementB : Element
{
    public override void Accept(Visitor visitor)
    {
        visitor.VisitElementB(this);
    }
    public void OperationB()
    { }
}
  ```

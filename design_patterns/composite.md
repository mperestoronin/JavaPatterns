# Компоновщик Composite
### Краткое описание
Данный паттерн  объединяет группы объектов в древовидную структуру по принципу "часть-целое и позволяет клиенту одинаково работать как с отдельными объектами, так и с группой объектов.
Образно реализацию паттерна можно представить в виде меню, которое имеет различные пункты. Эти пункты могут содержать подменю, в которых, в свою очередь, также имеются пункты. То есть пункт меню служит с одной стороны частью меню, а с другой стороны еще одним меню. В итоге мы однообразно можем работать как с пунктом меню, так и со всем меню в целом.

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/composite1.png)

![](https://habrastorage.org/r/w1560/getpro/habr/post_images/3c0/75e/525/3c075e525a7ed4aa2f13495a3289e348.jpg)

### Классы и интерфейсы
- интерфейс Component - определяет один метод - operation(). 
- Node - класс, реализующий интерфейс component. Представляет собой элементарный объект
- Composite - класс, реализующий интерфейс component. Представляет собой своего рода контейнер для объектов, реализующих интерфейс component (Node и Composite). Достигает этого за счет поля Component[] components. Т.е. в этом контейнере могут быть другие контейнеры. Также имеет методы addComponent(Component c) и removeComponent(Component c).


### Применение
Компоновщик следует применять:
- Когда объекты должны быть реализованы в виде иерархической древовидной структуры
- Когда клиенты единообразно должны управлять как целыми объектами, так и их составными частями. То есть целое и его части должны реализовать один и тот же интерфейс

### Детальный разбор
Смысл данного шаблона заключается в том, чтобы представить набор объектов как древовидную структуру в виде узлов и листьев. А затем работать с этими узлами и листьями единым образом, по одному интерфейсу. Иными словами, паттерн имеет смысл, когда все объекты можно представить в виде простых элементов и контейнеров. При этом контейнеры могут содержать как простые элементы, так и другие контейнеры. Каждый контейнер и простой элемент будут иметь один интерфейс, таким образом клиент сможет взаимодействовать с каждым элементом этой структуры и со всей структурой единым образом. Самый первый корневой элемент передапст вызовы остальным через рекурсию, таким образом данный паттерн позволяет работать с деревьями любой сложности, не привязываясь к конкретным классам из которых состоит система.

### Реализация 
Реализация на C#
``` cs
class Client
{
    public void Main()
    {
        Component root = new Composite("Root");
        Component leaf = new Leaf("Leaf");
        Composite subtree = new Composite("Subtree");
        root.Add(leaf);
        root.Add(subtree);
        root.Display();
    }
}
abstract class Component
{
    protected string name;
 
    public Component(string name)
    {
        this.name = name;
    }
 
    public abstract void Display();
    public abstract void Add(Component c); 
    public abstract void Remove(Component c);
}
class Composite : Component
{
    List<Component> children = new List<Component>();
 
    public Composite(string name)
        : base(name)
    {}
 
    public override void Add(Component component)
    {
        children.Add(component);
    }
 
    public override void Remove(Component component)
    {
        children.Remove(component);
    }
 
    public override void Display()
    {
        Console.WriteLine(name);
 
        foreach (Component component in children)
        {
            component.Display();
        }
    }
}
class Leaf : Component
{
    public Leaf(string name)
        : base(name)
    {}
 
    public override void Display()
    {
        Console.WriteLine(name);
    }
 
    public override void Add(Component component)
    {
        throw new NotImplementedException();
    }
 
    public override void Remove(Component component)
    {
        throw new NotImplementedException();
    }
}
  ```
Реализация на java
``` java
public interface SubExpression {
  
  public Number value();
  
  public void add(SubExpression expr);
  public void sub(SubExpression expr);
  public SubExpression getSubExpression(int index);
}

// Лист - целое число
public class IntegerValue implements SubExpression {
  
  private Integer value;
  
  public IntegerValue(Integer value) {
    this.value = value;
  }

  @Override
  public void add(SubExpression expr) {
    throw new UnsupportedOperationException();    
  }

  @Override
  public SubExpression getSubExpression(int index) {
    throw new UnsupportedOperationException();
  }

  @Override
  public void sub(SubExpression expr) {
    throw new UnsupportedOperationException();    
  }

  @Override
  public Number value() {
    return value;
  }
}

import java.util.ArrayList;
import java.util.List;

// Выражение - контейнер
public class Expression implements SubExpression {
  
  private List<SubExpression> exprs;
  
  public Expression(SubExpression ... exprs) {
    this.exprs = new ArrayList<SubExpression>();
    for (SubExpression expr: exprs) {
      this.exprs.add(expr);
    }
  }
  
  @Override
  public void add(SubExpression expr) {
    exprs.add(expr);    
  }
  
  @Override
  public void sub(SubExpression expr) {
    if (expr instanceof IntegerValue) {
      exprs.add(new IntegerValue(-1*expr.value().intValue()));
    } else {
      exprs.add(new FloatValue(-1*expr.value().floatValue()));
    }
    
  }

  @Override
  public SubExpression getSubExpression(int index) {
    return exprs.get(index);
  }

  @Override
  public Number value() {
    Number result = new Float(0);
    
    for (SubExpression expr: exprs) {
      result = result.floatValue() + expr.value().floatValue();
    }
    
    return result;
  }
}

// Использование
public class Main {

  public static void main(String[] args) {
    // Вычислим выражение - 20 - (5-2) - (11+6)
    // Приведем к следующему виду 20 - a - b
    SubExpression expr = new Expression();

    SubExpression a = new Expression(new IntegerValue(5), new IntegerValue(-2));
    SubExpression b = new Expression(new IntegerValue(11), new IntegerValue(6));
    
    expr.add(new IntegerValue(20));
    expr.sub(a);
    expr.sub(b);
    
    System.out.println(expr.value());
  }
}
    ```

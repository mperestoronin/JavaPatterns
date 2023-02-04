# Декоратор Decorator
### Краткое описание
Декоратор расширяет функциональность основного класса путем добавления дополнительного кода в начале, конце или вместо существующей реализации.
![](https://habrastorage.org/r/w1560/getpro/habr/post_images/15c/27c/26e/15c27c26e08f1936e3f73089ecac3d05.jpg)
![](https://media.geeksforgeeks.org/wp-content/uploads/uml.jpg)
### Классы и интерфейсы
- интерфейс component - содержит единственный метод operation. 
- ConcreteComponent - реализует component. 
- Decorator - также реализует Component. Дополняет функциональность, которая содержится в методе operation (в классе ConcreteComponent). Содержит ссылку на объект, реализующий интерфейс component (ромбовидная стрелка на диаграме). Т.е декоратор как бы внешнаяя оболочка над Concretecomponent. Это позволит в декоратору в своем методе operation вызывать метод operation конкректного компонента, а также добавлять туда что-то от себя.
### Реализация
Хороший пример на свифте:
``` swift
protocol Coffee {
    func cost() -> Double
    func ingredients() -> String
}

final class Espresso: Coffee {
    
    func ingredients() -> String {
        return "Espresso"
    }

    func cost() -> Double {
        return 100
    }
}

class CoffeDecorator: Coffee {
    
    private var сoffee: Coffee
    
    init(сoffee: Coffee) {
        self.сoffee = сoffee
    }
    
    func ingredients() -> String {
        return сoffee.ingredients()
    }
    
    func cost() -> Double {
        return сoffee.cost()
    }
}

final class Milk: CoffeDecorator {
    
    override func ingredients() -> String {
        return super.ingredients() + ", Milk"
    }
    
    override func cost() -> Double {
        return super.cost() + 20
    }
}

final class Whip: CoffeDecorator {
    
    override func ingredients() -> String {
        return super.ingredients() + ", Whip"
    }
    
    override func cost() -> Double {
        return super.cost() + 30
    }
}

final class Chocolate: CoffeDecorator {
    
    override func ingredients() -> String {
        return super.ingredients() + ", Chocolate"
    }
    
    override func cost() -> Double {
        return super.cost() + 50
    }
}

let espresso = Espresso()
let capuccino = Whip(сoffee: Milk(сoffee: espresso))
let capuccinoWithChocolate = Chocolate(сoffee: capuccino)

print(espresso.ingredients())
print(espresso.cost())

print(capuccino.ingredients())
print(capuccino.cost())

print(capuccinoWithChocolate.ingredients())
print(capuccinoWithChocolate.cost())
```

``` java
public interface InterfaceComponent {
	void doOperation();
}

class MainComponent implements InterfaceComponent {
	
	@Override
	public void doOperation() {
		System.out.print("World!");
	}	
}

abstract class Decorator implements InterfaceComponent {
	protected InterfaceComponent component;
	
	public Decorator (InterfaceComponent c) {
		component = c;
	}
	
	@Override
	public void doOperation() {
		component.doOperation();
	}

	public void newOperation() {
		System.out.println("Do Nothing");
	}
}

class DecoratorSpace extends Decorator {
	
	public DecoratorSpace(InterfaceComponent c) {
		super(c);
	}
	
	@Override
	public void doOperation() {
		System.out.print(" ");
		super.doOperation();
	}
	
	@Override
	public void newOperation() {
		System.out.println("New space operation");
	}
}

class DecoratorComma extends Decorator {

	public DecoratorComma(InterfaceComponent c) {
		super(c);
	}
	
	@Override
	public void doOperation() {
		System.out.print(",");
		super.doOperation();
	}	
        
	@Override
	public void newOperation() {
		System.out.println("New comma operation");
	}
}

class DecoratorHello extends Decorator {
	
	public DecoratorHello(InterfaceComponent c) {
		super(c);
	}
	
	@Override
	public void doOperation() {
		System.out.print("Hello");
		super.doOperation();
	}
	
	@Override
	public void newOperation() {
		System.out.println("New hello operation");
	}
}

class Main {
	
	public static void main (String... s) {
		Decorator c = new DecoratorHello(new DecoratorComma(new DecoratorSpace(new MainComponent())));
		c.doOperation(); // Результат выполнения программы "Hello, World!"
	    c.newOperation(); // New hello operation
    }
}
```

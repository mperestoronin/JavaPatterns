# Мост bridge
### Краткое описание
Данный паттерн позволяет отделить абстракцию от реализации так, чтобы и то и другое можно было изменять независимо.


![](https://habrastorage.org/r/w1560/getpro/habr/post_images/a37/91c/32c/a3791c32c219678bc6549b012747497d.jpg)

### Детальный разбор
Зачастую в крупных проектах, где есть достаточно сложная архитектура классов для удобства добавления новых классов разделяют эту архитектуру на 2 части. Одна часть отвечает за абстракцию, а другая за реализацию.
Предположим, мы создали абстрактный класс "автомобиль" с двумя наследниками - седан и хэтчбэк. Теперь мы захотели добаваить информацию о производителе. Тогда мы от класса седан наследуемся 2 раза и создаем 2 дополнительных класса "КияСедан" и "ШкодаСедан". Аналогично с хэтчбэком - "Кияхэтчбэк" и "ШкодаХэтчбэк". Теперь, если мы захотим добавить третьего производителя, то нужно еще для каждого вида автомобиля (хэтчбэк или седан) еще по одному классу писать. А если мы добавим еще один вид автомобиля (купе например). То нужно будет для него также писать столько же классов, сколько марок машин у нас есть. Уже сейчас чувствуется, что структура становится слишком сложной. А при добавлении дополнительных элементов она будет еще сложней.

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/bridge1.png)

Теперь попробуем применить "мост" для решения этой задачи:

Мы также создаем Абстрактный класс "Car", наследуем от него "Sedan", "Hatchback", "Coupe".
Но марки автомобилей вынесем уже в отедльную структуру. Создаем интерфейс "марка автомобиля", и реализуем его в двух классах: "кия" и "шкода".
Мы получили две несвязанные структуры:
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/bridge2.png)
Слева у нас полная абстракция, а справа уже конкретика. Чтобы их соеденить, мы внутри Car делаем поля с ссылкой на объект, реализующий интерфейс "марка машины". Таким образом, каждый седан, купэ и хэтчбэк имеет поле, где он хранит свою марку.
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/bridge3.png)

``` java
public interface Drawer {

	public void drawCircle(int x, int y, int radius);
}

public class SmallCircleDrawer implements Drawer{
	
	public static final double radiusMultiplier = 0.25;
	
	@Override
	public void drawCircle(int x, int y, int radius) {
		System.out.println("Small circle center = " + x + "," + y + " radius = " + radius*radiusMultiplier);
	}
}

public class LargeCircleDrawer implements Drawer{

	public static final int radiusMultiplier = 10;
	
	@Override
	public void drawCircle(int x, int y, int radius) {
		System.out.println("Large circle center = " + x + "," + y + " radius = " + radius*radiusMultiplier);
	}
}

public abstract class Shape {

	protected Drawer drawer;
	
	protected Shape(Drawer drawer){
		this.drawer = drawer;
	}

	public abstract void draw();

	public abstract void enlargeRadius(int multiplier);
}

public class Circle extends Shape{

	private int x;
	private int y;
	private int radius;
	
	public Circle(int x, int y, int radius, Drawer drawer) {
		super(drawer);
		setX(x);
		setY(y);
		setRadius(radius);
	}

	@Override public void draw() {  drawer.drawCircle(x, y, radius);  }

	@Override public void enlargeRadius(int multiplier) {  radius *= multiplier;  }

	public int getX() {  return x;  }
	public int getY() {  return y;  }

	public int getRadius()  {	return radius;	}

	public void setX(int x) {  this.x = x;  }
	public void setY(int y) {  this.y = y;  }

	public void setRadius(int radius) {  this.radius = radius;  }
}

// Класс, показывающий работу шаблона проектирования "Мост".

public class Application {

	public static void main (String [] args){
		Shape [] shapes = {
				new Circle(5,10,10, new LargeCircleDrawer()), 
				new Circle(20,30,100, new SmallCircleDrawer())};
		
		for (Shape next : shapes)
			next.draw();
	}
}
// Output
Large circle center = 5,10 radius = 100
Small circle center = 20,30 radius = 25.0
```

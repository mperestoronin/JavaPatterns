# Посредник mediator
### Краткое описание
Шаблон предназначен для упрощения взаимодействия объектов системы путем создания специального объекта,
который управляет распределением сообщений между остальными объектами.

### Классы и интерфейсы
- интерфейс Mediator
- ConcreteMediator своего рода диспечер. Описывает взаимодействие клиентов друг с другом.
- интерфейс client - 
- ConcreteClient/ ConcreteAggregate 

![](https://metanit.com/sharp/patterns/pics/mediator.png)

### Детальный разбор
Определяет объект, который инкапсулирует логику взаимодействия других объектов. Объекты не ссылаются друг на друга явно 
и можно изменять алгоритм их взаимодействия независимо. Инными словами, представим, что у нас есть куча объектов,
которые постоянно друг с другом взаимодействуют (реагируют друг на друга, вызывают друг у друга различные методы).
Мы же создаем отдельный объект, куда переносим всю логику взаимодействия вышеописанных объектов так, чтобы эти объекты
вместо того, чтобы напрямую вызывать друг друга, будут взаимодействовать через посредника. Т.е. каждый объект будет иметь
ссылку на посредника и уведомлять его при различных событиях, а посредник будет иметь ссылку на каждый объект и будет перенаправлять
поступающие ему запросы конкретным объектам. (Что-то типо диспечера).

Client обязывает ConcreteClient реализовать два метода:
- handleEvent - обрабатывает запрос, полученный от другого ConcreteClient через медиатор.
- broadCastEvent - служит для отправки запроса другому ConcreteClient через медиатор.

Mediator обязывает concreteMediator реализовывать один метод - broadCastEvent - он разбирается, какой клиент его вызвал и что этот клиент хочет сделать (какому другому клиенту нужно что-то передать).

Помимо этого concreteMediator хранит список всех ConcreteClient

При этом каждый ConcreteClient хранит ссылку на медиатор и может вызвать у него broadcastEvent (т.е. передать сообщение) mediator.breoadcastevent
### Применение
Представим чат. Пользователи хотят отправлять друг другу сообщения и делают это через посредника (мессенджер). Мессенджер же знает, какие клиенты у него есть и кому что надо отправить.

### Реализация 
Хороший пример на c#
``` c#
abstract class Mediator
{
    public abstract void Send(string msg, Colleague colleague);
}
 
abstract class Colleague
{
    protected Mediator mediator;
 
    public Colleague(Mediator mediator)
    {
        this.mediator = mediator;
    }
}
 
class ConcreteColleague1 : Colleague
{
    public ConcreteColleague1(Mediator mediator)
        : base(mediator)
    { }
  
    public void Send(string message)
    {
        mediator.Send(message, this);
    }
  
    public void Notify(string message)
    { }
}
 
class ConcreteColleague2 : Colleague
{
    public ConcreteColleague2(Mediator mediator)
        : base(mediator)
    { }
  
    public void Send(string message)
    {
        mediator.Send(message, this);
    }
  
    public void Notify(string message)
    { }
}
 
class ConcreteMediator : Mediator
{
    public ConcreteColleague1 Colleague1 { get; set; }
    public ConcreteColleague2 Colleague2 { get; set; }
    public override void Send(string msg, Colleague colleague)
    {
        if (Colleague1 == colleague)
            Colleague2.Notify(msg);
        else
            Colleague1.Notify(msg);
    }
}
```

Пример на java
``` java
public abstract class Colleague {

    protected Mediator mediator;

    public Colleague(Mediator mediator) {
        this.mediator = mediator;
    }

    public void send(String message) {
        mediator.send(message, this);
    }

    public abstract void notify(String message);
}

public abstract class Mediator {

    public abstract void send(String message, Colleague sender);
}

public class ConcreteColleague1 extends Colleague {

    public ConcreteColleague1(Mediator mediator) {
        super(mediator);
    }

    @Override
    public void notify(String message) {
        System.out.println("Colleague1 gets message: " + message);
    }
}

public class ConcreteColleague2 extends Colleague {

    public ConcreteColleague2(Mediator mediator) {
        super(mediator);
    }

    @Override
    public void notify(String message) {
        System.out.println("Colleague2 gets message: " + message);
    }
}

public class ConcreteMediator extends Mediator {

    private ConcreteColleague1 colleague1;
    private ConcreteColleague2 colleague2;

    public void setColleague1(ConcreteColleague1 colleague) {
        this.colleague1 = colleague;
    }

    public void setColleague2(ConcreteColleague2 colleague) {
        this.colleague2 = colleague;
    }

    @Override
    public void send(String message, Colleague sender) {
        if (sender.equals(colleague1)) {
            colleague2.notify(message);
        } else {
            colleague1.notify(message);
        }
    }
}

public class Main {

    public static void main(String[] args) {
        ConcreteMediator m = new ConcreteMediator();

        ConcreteColleague1 c1 = new ConcreteColleague1(m);
        ConcreteColleague2 c2 = new ConcreteColleague2(m);

        m.setColleague1(c1);
        m.setColleague2(c2);

        c1.send("How are you?");
        c2.send("Fine, thanks");
    }
}
```

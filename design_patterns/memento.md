# Хранитель memento
## Краткое описание
Позволяет легко хранить предидущие состояние объекта
## Классы паттерна
- **Memento** - класс обертка, хранящий состояние **originator** (под состоянием понимается значение полей)
- **Originator** - имеет состояние, которое необходимо сохранить. Может сохранить свое состояние создав новый  **memento** и добавив его в список, хранящийся в **caretaker**, или откатить свое состояние назад, запрасив его у **caretaker**.
- **Caretaker** - хранит список всех предидущих версий состояний **originator** (т.е. **memento**), может сохранять и доставать мементо.
## Классическое определение
Шаблон memento - Хранитель используется двумя объектами: «Создателем» (originator) и «Опекуном» (caretaker). originator — это объект, у которого есть внутреннее состояние. Объект caretaker может производить некоторые действия с originator, но при этом необходимо иметь возможность откатить изменения. Для этого caretaker запрашивает у originator объект memento. Затем выполняет запланированное действие (или последовательность действий). Для выполнения отката originator к состоянию, которое предшествовало изменениям, caretaker возвращает объект memento его originator. memento является непрозрачным (то есть таким, который не может или не должен изменяться caretaker).
## Более детальный разбор
<br> Рассмотрим картину ниже:
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/memento2.png)

Как уже писалось ранее, **memento** - это хранитель. Он хранит состояние объекта originator (значение его поля) в своем поле state.
<br>У **memento** есть:
- state - состояние originator на момент создания этого **memento**
- конструктор, заполняющий это поле.
- геттер, возвращающий нам значение этого поля

**caretaker** же обладает списком объектов типа **memento**, таким образом он сохраняет различные состояния originator. Он также может возвращать и добавлять новые состояния (**memento**) в этот список. **Caretaker** при этом ничего не знает о самом состоянии state, а лишь хранит ссылку на обертку этого состояния (**memento**). Доступ к содержимому **memento** имеет только создатель (**originator**).

**originator:**
- имеет какое - то состояние (значение поля)
-  createMemento создает экземпляр класса **memento**. Этот экземпляр будет хранить поле state нашего originator на момент создания.
-  restoreMemento(memento m) возвращает какое либо старое состояние объекта. Тем самым **originator** может "откатиться" на более старую версию себя.

## Применение
Хранение состояний доски для реализации отмены хода в ДЗ "реверси". В принципе применимо ко всем сохранениям в играх. 

## Реализация 
``` java
public class Memento {
    private final String state;

    public Memento(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }
}

public class Caretaker {
    private Memento memento;

    public Memento getMemento() {
        return memento;
    }

    public void setMemento(Memento memento) {
        this.memento = memento;
    }
}

public class Originator {
    private String state;

    public void setState(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }

    public Memento saveState() {
        return new Memento(state);
    }

    public void restoreState(Memento memento) {
        this.state = memento.getState();
    }
}

public class Application {
    public static void main(String[] args) {
        Originator originator = new Originator();
        Caretaker caretaker = new Caretaker();

        originator.setState("on");
        System.out.printf("State is %s\n", originator.getState());
        caretaker.setMemento(originator.saveState());

        originator.setState("off");
        System.out.printf("State is %s\n", originator.getState());

        originator.restoreState(caretaker.getMemento());
        System.out.printf("State is %s\n", originator.getState());
    }
}
```
## Источник
https://www.youtube.com/watch?v=epiQxPyOPBY&ab_channel=VladimirVysokomornyi
https://ru.wikipedia.org/wiki/%D0%A5%D1%80%D0%B0%D0%BD%D0%B8%D1%82%D0%B5%D0%BB%D1%8C_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)

### Хранитель memento
## Краткое описание
Позволяет легко хранить предидущие состояние объекта
## Классы паттерна
- **memento** - класс самого объекта, состояния которого необходимо хранить (под состоянием подразумевается значение полей этого объекта).
- **Originator** - получает и задает параметры выбранного memento. Создает новые мементо и присваивает им текущие значения
- **Caretaker** - хранит список всех предидущих версий мементо, может сохранять и доставать мементо.
## Классическое определение
Шаблон memento - Хранитель используется двумя объектами: «Создателем» (originator) и «Опекуном» (caretaker). originator — это объект, у которого есть внутреннее состояние. Объект caretaker может производить некоторые действия с originator, но при этом необходимо иметь возможность откатить изменения. Для этого caretaker запрашивает у originator объект memento. Затем выполняет запланированное действие (или последовательность действий). Для выполнения отката originator к состоянию, которое предшествовало изменениям, caretaker возвращает объект memento его originator. memento является непрозрачным (то есть таким, который не может или не должен изменяться caretaker).
## Более детальный разбор
<br> Рассмотрим картину ниже:
О **memento** проще думать, как о состоянии объекта в какой-то момент времени/ на каком то эатпе работы программы.
<br>У **memento** есть:
- article - параметр, который хранится в поле. В нашем случае это строка
- конструктор, заполняющий это поле (строку).
- геттер, возвращающий нам значение этого поля (строку)
<br>**caretaker** же обладает списком объектов типа **memento**, таким образом он сохраняет состояния объекта. Он также может возвращать и добавлять новые состояния (**memento**) в этот список
<br> **originator:**
- Хранит значение для article
-  set - меняет значение article
-  createMemento создает новый **memento** с заданным article, тем самым сохраняя текущее состояние объекта
-  restoreMemento возвращает какое либо старое состояние объекта

## Применение
Хранение состояний доски для реализации отмены хода в ДЗ "реверси"

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
 

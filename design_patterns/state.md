
### Краткое описание
Состояние позволяет объекту изменять свое поведение в зависимости от состояния в котором он находится.

Пример из жизни: Допустим, в графическом редакторе вы выбрали кисть. Она меняет своё поведение в зависимости от настройки цвета, т. е. рисует линию выбранного цвета.
### Классы и интерфейсы паттерна
- context - изначальный объект, меняющий свое поведение в зависимости от состояния. Имеет ссылку на объект состояния (по сути ссылка на свое текущее состояние). Переходы context из одного состояния в другое осуществляются либо в методе context'a, либо в объектах состояний. В первой реализации, context в курсе всех своих возможных состояниях и даже может хранить на них ссылки, во втором, объекты состояний будут хранить ссылку на изначальный объект (context) и знать про другие возможные состояния.
- интерфейс state - содержит все методы изначального объекта, реализация которых меняется в зависимости от состояния объекта.

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/state1.png)

### Более детальный разбор
Посмотрим на блок схему:
interface state обязывает concretestateA и concreteStateB реализовывать метод someMethod. Somemethod - это метод Context, реализация которого зависит от состояния этого context.

Context содержит поле currentState, содержащее объект, реализующий интерфейс state (т.е. ссылка на свое текущее состояние). А также метод setCurrentState(State s) меняющий текущее состояние (в случае диаграмы, либо на А, либо на В).

### Реализация
``` java
public class StateExample {

    public static void main(String[] args) {
        StateContext context = new StateContext();
        context.heat();
        context.heat();
        context.heat();
        context.freeze();
        context.freeze();
        context.freeze();
        // OUTPUT:
        // Heating solid substance...
        // Changing state to liquid...
        // Heating liquid substance...
        // Changing state to gaseous...
        // Heating gaseous substance...
        // Nothing happens.
        // Freezing gaseous substance...
        // Changing state to liquid...
        // Freezing liquid substance...
        // Changing state to solid...
        // Freezing solid substance...
        // Nothing happens.
    }

}

interface State {
    String getName();
    void freeze(StateContext context);
    void heat(StateContext context);
}

class SolidState implements State {

    private static final String NAME = "solid";

    public String getName() {
        return NAME;
    }

    public void freeze(StateContext context) {
        System.out.println("Nothing happens.");
    }

    public void heat(StateContext context) {
        context.setState(new LiquidState());
    }

}

class LiquidState implements State {

    private static final String NAME = "liquid";

    public String getName() {
        return NAME;
    }

    public void freeze(StateContext context) {
        context.setState(new SolidState());
    }

    public void heat(StateContext context) {
        context.setState(new GaseousState());
    }

}

class GaseousState implements State {

    private static final String NAME = "gaseous";

    public String getName() {
        return NAME;
    }

    public void freeze(StateContext context) {
        context.setState(new LiquidState());
    }

    public void heat(StateContext context) {
        System.out.println("Nothing happens.");
    }

}

class StateContext {

    private State state = new SolidState();

    public void freeze() {
        System.out.println("Freezing " + state.getName() + " substance...");
        state.freeze(this);
    }

    public void heat() {
        System.out.println("Heating " + state.getName() + " substance...");
        state.heat(this);
    }

    public void setState(State state) {
        System.out.println("Changing state to " + state.getName() + "...");
        this.state = state;
    }

    public State getState() {
        return state;
    }

}
```
### Источники
https://www.youtube.com/watch?v=emQW0UNjgF4&ab_channel=ArtKirillov
https://www.youtube.com/watch?v=cErmNab8LlA&ab_channel=VladimirVysokomornyi
https://ru.wikipedia.org/wiki/%D0%A1%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D0%B5_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)#:~:text=%D0%A1%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D0%B5%20(%D0%B0%D0%BD%D0%B3%D0%BB.,%D0%B2%20%D0%B7%D0%B0%D0%B2%D0%B8%D1%81%D0%B8%D0%BC%D0%BE%D1%81%D1%82%D0%B8%20%D0%BE%D1%82%20%D1%81%D0%B2%D0%BE%D0%B5%D0%B3%D0%BE%20%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F.

# Команда command
### Краткое описание
Команда - представляющий действие. Объект команды заключает в себе само действие и его параметры.
Позволяет создание структуры, в которой класс-отправитель и класс-получатель не зависят друг от друга напрямую. Организация обратного вызова к классу, который включает в себя класс-отправитель.

### Классическое определение
В данном паттерне объект используется для инкапсуляции всей информации, необходимой для выполнения действия или вызова события в более позднее время. Эта информация включает в себя имя метода, объект, который является владельцем метода и значения параметров метода.

Четыре термина всегда связаны с шаблоном Команда: команды (command), приёмник команд (receiver), вызывающий команды (invoker) и клиент (client). Объект Command знает о приёмнике и вызывает метод приемника. Значения параметров приёмника сохраняются в команде. Вызывающий объект (invoker) знает, как выполнить команду и, возможно, делает учёт и запись выполненных команд. Вызывающий объект (invoker) ничего не знает о конкретной команде, он знает только об интерфейсе. Оба объекта (вызывающий объект и несколько объектов команд) принадлежат объекту клиента (client). Клиент решает, какие команды выполнить и когда. Чтобы выполнить команду он передает объект команды вызывающему объекту (invoker).

Использование командных объектов упрощает построение общих компонентов, которые необходимо делегировать или выполнять вызовы методов в любое время без необходимости знать методы класса или параметров метода. Использование вызывающего объекта (invoker) позволяет вести учёт выполненных команд без необходимости знать клиенту об этой модели учёта (такой учёт может пригодиться, например, для реализации отмены и повтора команд).
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/command.png)
### Классы и интерфейсы паттерна
- Invoker - вызывающий класс. Хранит в себе ссылки на экземпляры ConcreteCommand. Под каждое действие в Receiver хранит отдельный ConcreteCommand
- Receiver - приемник, содержащий какое-то действие. Под это действие создается отдельный экземпляр ConcreteCommand. Если действий у одного receiver'a несколько, то под каждое из них создастся отдельный  ConcreteCommand.
- интерфейс Command - содержит один метод execute (т.е. выполнить команду)
- ConcreteCommand - класс, который реализует интерфейс Command. Содержит ссылку на конкретный receiver. Метод execute выполняет действие, описанное в receiver. Таким образом ConcreteCommand является оберткой над receiver, т.к. просто выполняет действие, описанное в нем.
### Детальный разбор
Предположим, что произошло какое-то событие (например поступило сообщение)
<br>Это событие будет обрабатывать цепочка обработчиков (самих обработчиков может быть сколько угодно)
<br>Пусть событие поступило к первому обработчику, если он успешно обработал это событие, то действие паттерна заканчивается. Если нет, то событие передается следующему обработчику по цепочке и так до тех пор, пока один из обработчиков его не обработает. Существуют ситуации, когда одно и то же событие следует обработать несколько раз разными обработчиками. Для такого случая есть реализация, когда паттерн не заканчивается на первом корректно обработавшем событие обработчике. В таком случае, обработчики, расположенные на каждом последующем уровне, добавляют свою новую функциональность к функциональности базового обработчика. 
Тогда событие так и будет передаваться от обработчика к обработчику до тех пор, пока оно не будет обработано нужное количество раз/ обработанно всеми необходимыми обработчиками.

У ConcreteHandler есть:
- ссылка на следующий обработчик в цепочке
- метод HandleMessage, обрабатывающий событие (каждый ConcreteHandler может обрабатывать его по своему)
- 

### Применение
Шаблон рекомендован для использования в условиях:
- в разрабатываемой системе имеется группа объектов, которые могут обрабатывать сообщения определенного типа;
- все сообщения должны быть обработаны хотя бы одним объектом системы;
- сообщения в системе обрабатываются по схеме «обработай сам либо перешли другому», то есть одни сообщения обрабатываются на том уровне, где они получены, а другие пересылаются объектам иного уровня.

### Реализация
``` java
import java.util.HashMap;

/** The Command interface */
interface Command {
    void execute();
}

/** The Invoker class */
class Switch {
    private final HashMap<String, Command> commandMap = new HashMap<>();
    
    public void register(String commandName, Command command) {
        commandMap.put(commandName, command);
    }
    
    public void execute(String commandName) {
        Command command = commandMap.get(commandName);
        if (command == null) {
            throw new IllegalStateException("no command registered for " + commandName);
        }
        command.execute();
    }
}

/** The Receiver class */
class Light {
    public void turnOn() {
        System.out.println("The light is on");
    }

    public void turnOff() {
        System.out.println("The light is off");
    }
}

/** The Command for turning on the light - ConcreteCommand #1 */
class SwitchOnCommand implements Command {
    private final Light light;

    public SwitchOnCommand(Light light) {
        this.light = light;
    }

    @Override // Command
    public void execute() {
        light.turnOn();
    }
}

/** The Command for turning off the light - ConcreteCommand #2 */
class SwitchOffCommand implements Command {
    private final Light light;

    public SwitchOffCommand(Light light) {
        this.light = light;
    }

    @Override // Command
    public void execute() {
        light.turnOff();
    }
}

public class CommandDemo {
    public static void main(final String[] arguments) {
        Light lamp = new Light();

        Command switchOn = new SwitchOnCommand(lamp);
        Command switchOff = new SwitchOffCommand(lamp);

        Switch mySwitch = new Switch();
        mySwitch.register("on", switchOn);
        mySwitch.register("off", switchOff);

        mySwitch.execute("on");
        mySwitch.execute("off");
    }
}
```

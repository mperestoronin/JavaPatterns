# Команда command
### Краткое описание
Команда - представляющий действие. Объект команды заключает в себе само действие и его параметры.
Позволяет создание структуры, в которой класс-отправитель и класс-получатель не зависят друг от друга напрямую. Организация обратного вызова к классу, который включает в себя класс-отправитель.

### Классическое определение
В данном паттерне объект используется для инкапсуляции всей информации, необходимой для выполнения действия или вызова события в более позднее время. Эта информация включает в себя имя метода, объект, который является владельцем метода и значения параметров метода.

Четыре термина всегда связаны с шаблоном Команда: команды (command), приёмник команд (receiver), вызывающий команды (invoker) и клиент (client). Объект Command знает о приёмнике и вызывает метод приемника. Значения параметров приёмника сохраняются в команде. Вызывающий объект (invoker) знает, как выполнить команду и, возможно, делает учёт и запись выполненных команд. Вызывающий объект (invoker) ничего не знает о конкретной команде, он знает только об интерфейсе. Оба объекта (вызывающий объект и несколько объектов команд) принадлежат объекту клиента (client). Клиент решает, какие команды выполнить и когда. Чтобы выполнить команду он передает объект команды вызывающему объекту (invoker).

Использование командных объектов упрощает построение общих компонентов, которые необходимо делегировать или выполнять вызовы методов в любое время без необходимости знать методы класса или параметров метода. Использование вызывающего объекта (invoker) позволяет вести учёт выполненных команд без необходимости знать клиенту об этой модели учёта (такой учёт может пригодиться, например, для реализации отмены и повтора команд).

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/command.jpg)

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/command2.gif)
### Классы и интерфейсы паттерна
- Client - тот, кто запрашивает выполнения какого - то действия
- Invoker - вызывающий класс. Хранит в себе ссылки на экземпляры ConcreteCommand. Под каждое действие в Receiver хранит отдельный ConcreteCommand
- Receiver - приемник, содержащий какое-то действие. Под это действие создается отдельный экземпляр ConcreteCommand. Если действий у одного receiver'a несколько, то под каждое из них создастся отдельный  ConcreteCommand.
- интерфейс Command - содержит один метод execute (т.е. выполнить команду)
- ConcreteCommand - класс, который реализует интерфейс Command. Содержит ссылку на конкретный receiver. Метод execute выполняет действие, описанное в receiver. Таким образом ConcreteCommand является оберткой над receiver, т.к. просто выполняет действие, описанное в нем.


### Применение
Данный шаблон применяется в следующих случаях:
- Кнопки пользовательского интерфейса и пункты меню
- Запись макросов
Если все действия пользователя представлены в виде объектов команды, программа может записать последовательность действий, просто сохраняя список командных объектов в том порядке, в котором они выполняются. Затем она может «воспроизвести» одни и те же действия, выполняя те же объекты команд в той же последовательности.
- Многоуровневая отмена операций (Undo)
Если все действия пользователя в программе реализованы в виде командных объектов, программа может сохранить стек последних выполненных команд. Когда пользователь хочет отменить команду, программа просто выталкивает последний объект команды и выполняет его метод undo().
- Сети
Можно отправить объекты команд по сети для выполнения на другой машине, например действие игрока в компьютерной игре.

- Индикаторы выполнения
Предположим, что программа имеет последовательность команд, которые она выполняет по порядку. Если каждый объект команды имеет метод getEstimatedDuration() (получить оценочную длительность), программа может легко оценить общую продолжительность процесса. Она может показать индикатор выполнения, который отражает, насколько близка программа к завершению всех задач.

- Пулы потоков
Типичный класс пула потоков общего назначения может иметь метод addTask(), который добавляет рабочий элемент к внутренней очереди заданий ожидающих своего выполнения. Он поддерживает пул потоков, которые выполняют команды из очереди. Элементы в очереди являются объектами команд. Как правило, эти объекты реализуют общий интерфейс, такой как java.lang.Runnable, что позволяет пулу потоков запустить команды на выполнение, даже если он сам был написан без каких-либо знаний о конкретных задачах, для которых он будет использоваться.

- Транзакции
Аналогично операции «отмена» система управления базами данных (СУБД) или установщик программного обеспечения может хранить список операций, которые были или будут выполнены. Если одна из них закончится неудачей, то все остальные могут быть отменены или быть отброшены (обычно называется откат). Например, если две связанные между собой таблицы базы данных должны быть обновлены, а второе обновление терпит неудачу, то система может откатить транзакцию, чтобы первая таблица не содержала недопустимую ссылку.

- Мастера
Часто мастер (мастер установки или любой другой) представляет несколько страниц конфигурации для одного действия, которое происходит только тогда, когда пользователь нажимает на кнопку «Готово» на последней странице. В этих случаях, естественный способ отделить код пользовательского интерфейса от кода приложения является реализация мастера с помощью объекта команд. Объект команда создается при первом отображении мастера. Каждая страница мастера сохраняет свои изменения в объекте команды, поэтому объект заполняется по мере перехода пользователя. Кнопка «Готово» просто запускает метод execute() на выполнение.


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
### Источник
https://www.youtube.com/watch?v=8gE-icd93WA&ab_channel=VladimirVysokomornyi
https://ru.wikipedia.org/wiki/%D0%9A%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%B0_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)
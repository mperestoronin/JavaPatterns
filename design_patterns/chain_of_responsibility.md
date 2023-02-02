# Цепочка обязанностей Chain of Responsibility
### Краткое описание
Данный шаблон предназначен для организации в системе уровней ответственности.
Избегает связывания отправителя запроса с его получателем, давая возможность обработать запрос более чем одному объекту. Связывает объекты- получатели и передает запрос по цепочке пока объект не обработает его.
### Классы и интерфейсы паттерна
- интерфейс Handler - интерфейс обработчика, содержит один метод - handleMessage
- ConcreteHandler - какой-то конкретный обработчик, реализует интерфейс Handler

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/chain1.png)

![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/chain2.png)

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
package chainofresp;

abstract class Logger {
    public static int ERR = 3;
    public static int NOTICE = 5;
    public static int DEBUG = 7;
    protected int mask;

    // The next element in the chain of responsibility
    protected Logger next;

    public Logger setNext(Logger log) {
        next = log;
        return log;
    }

    public void message(String msg, int priority) {
        if (priority <= mask) {
            writeMessage(msg);
        }
        if (next != null) {
            next.message(msg, priority);
        }
    }

    abstract protected void writeMessage(String msg);
}

class StdoutLogger extends Logger {
    public StdoutLogger(int mask) { 
        this.mask = mask;
    }

    protected void writeMessage(String msg) {
        System.out.println("Writing to stdout: " + msg);
    }
}

class EmailLogger extends Logger {
    public EmailLogger(int mask) {
        this.mask = mask;
    }

    protected void writeMessage(String msg) {
        System.out.println("Sending via email: " + msg);
    }
}

class StderrLogger extends Logger {
    public StderrLogger(int mask) {
        this.mask = mask;
    }

    protected void writeMessage(String msg) {
        System.out.println("Sending to stderr: " + msg);
    }
}

public class ChainOfResponsibilityExample {
    public static void main(String[] args) {
        // Build the chain of responsibility
        Logger logger, logger1,logger2;
        logger = new StdoutLogger(Logger.DEBUG);
        logger1 = logger.setNext(new EmailLogger(Logger.NOTICE));
        logger2 = logger1.setNext(new StderrLogger(Logger.ERR));

        // Handled by StdoutLogger
        logger.message("Entering function y.", Logger.DEBUG);

        // Handled by StdoutLogger and EmailLogger
        logger.message("Step1 completed.", Logger.NOTICE);

        // Handled by all three loggers
        logger.message("An error has occurred.", Logger.ERR);
    }
}
/*
The output is:
   Writing to stdout:   Entering function y.
   Writing to stdout:   Step1 completed.
   Sending via e-mail:  Step1 completed.
   Writing to stdout:   An error has occurred.
   Sending via e-mail:  An error has occurred.
   Sending to stderr:   An error has occurred.
*/
```
### Источник
https://www.youtube.com/watch?v=Xkw8Ir77Fak&ab_channel=VladimirVysokomornyi
https://ru.wikipedia.org/wiki/%D0%A6%D0%B5%D0%BF%D0%BE%D1%87%D0%BA%D0%B0_%D0%BE%D0%B1%D1%8F%D0%B7%D0%B0%D0%BD%D0%BD%D0%BE%D1%81%D1%82%D0%B5%D0%B9#%D0%9F%D1%80%D0%B8%D0%BC%D0%B5%D1%80_%D0%BD%D0%B0_Java

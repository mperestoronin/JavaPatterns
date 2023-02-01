# Наблюдатель Observer
### Краткое описание
Определяет зависимость типа один ко многим между объектами таким образом, что при изменении состояния одного объекта все зависящие от него оповещаются об этом событии.
![](https://github.com/mperestoronin/JavaPatterns/blob/main/photos/observer1.png)
### Классы и интерфейсы паттерна
- интерфейс Observable (наблюдаемый)
- интерфейс Observer (наблюдатель)
- класс ConcreteObservable - конкретный наблюдаемый объект, реаоизующий интерфейс Observable
- класс ConcreteObserver - конкретный наблюдатель, реализующий интерфейс observer, наблюдающий за конкретным объектом ConcreteObservable

### Детальный разбор
у интерфейса Observable есть 3 метода:
- addObserver(Observer o) 
- removeObserver(Observer o)
- notifyObserver(Observer o)

У ConcreteObservable есть:
- Observer[] observers - массив наблюдаетелей за этим объектом
- addObserver - добавляет наблюдателя за этим объектом
- removeObserver - убирает наблюдателя за этим объектом
- свое состояние (какие-то поля)
- notifyObserver - При каждой смене состояния (например поменялось какое-то поле) перебирает всех наблюдателей в массиве Observer[] И рассылает им свое новое состояние. По сути вызывает метод handleEvent каждого observer'a.

Интерфейс observer обладает лишь одним методом - handleEvent. 

ConcreteObserver - получает сообщение от ConcreteObservable, о смене состояния и как-то реагирует на эту информацуию при помощи метода handleEvent. Каждый наблюдатель может обрабатывать эту информацию по своему (иметь собственную реализацию handleEvent).

### Применение
Шаблон «наблюдатель» применяется в тех случаях, когда система обладает следующими свойствами:
- существует как минимум один объект, рассылающий сообщения;
- имеется не менее одного получателя сообщений, причём их количество и состав могут изменяться во время работы приложения;
- позволяет избежать сильного зацепления взаимодействующих классов.

Данный шаблон часто применяют в ситуациях, в которых отправителя сообщений не интересует, что делают получатели с предоставленной им информацией.

Пример:
Метерологическая станция, сообщает об изменении температуры, а данные автоматически поступают в новостную службу/мобильное приложение.

Торговля на фондовом рынке через мобильное приложение. Реакция на изменение стоимости активов (автопродажа (стоп лимит), отправка уведомления пользователю, обновление графика стоимости активов)

### Реализация
``` java
// В примере описывается получение данных от метеорологической станции (класс WeatherData, рассылатель событий) и 
//использование их для вывода на экран (класс CurrentConditionsDisplay, слушатель событий). 
//Слушатель регистрируется у наблюдателя с помощью метода registerObserver (при этом слушатель заносится в список observers).
//Регистрация происходит в момент создания объекта currentDisplay, т.к. метод registerObserver применяется в конструкторе.  
//При изменении погодных данных вызывается метод notifyObservers, который в свою очередь вызывает метод update 
//у всех слушателей, передавая им обновлённые данные.
import java.util.LinkedList;
import java.util.List;

public class WeatherStation {
    public static void main(String[] args) {
        WeatherData weatherData = new WeatherData();

        Observer currentDisplay = new CurrentConditionsDisplay ();

        weatherData.registerObserver(currentDisplay);

        weatherData.setMeasurements(29f, 65f, 745);
        weatherData.setMeasurements(39f, 70f, 760);
        weatherData.setMeasurements(42f, 72f, 763);
    }
}

interface Observer {
    void update (float temperature, float humidity, int pressure);
}

interface Observable {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

class WeatherData implements Observable {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private int pressure;
    
    public WeatherData() {
        observers = new LinkedList<>();
    }
    
    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers)
            observer.update(temperature, humidity, pressure);
    }
         
    public void setMeasurements(float temperature, float humidity, int pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        notifyObservers();
    }
}

class CurrentConditionsDisplay implements Observer {
    private float temperature;
    private float humidity;
    private int pressure;

    @Override
    public void update(float temperature, float humidity, int pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        display();
    }

    public void display() {
        System.out.printf("Сейчас значения:%.1f градусов цельсия и %.1f %% влажности. Давление %d мм рт. ст.\n", temperature, humidity, pressure);
    }
}
```
### Источник
https://www.youtube.com/watch?v=dRpRiJehdvc&ab_channel=VladimirVysokomornyi
https://ru.wikipedia.org/wiki/%D0%9D%D0%B0%D0%B1%D0%BB%D1%8E%D0%B4%D0%B0%D1%82%D0%B5%D0%BB%D1%8C_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)

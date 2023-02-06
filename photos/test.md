# Мементо

ReqSpecState.java
``` java
/**
 * это наш мементо/хранитель/снапшот/снимок и другие 100 имен, которыми он называется
 */
public class ReqSpecState {
    private String content; // содержимое нашей курсовой

    public ReqSpecState(String text) {
        content = text;
    }

    public String getContent() {
        return content;
    }

}
```
ReqSpec.java
``` java
/**
 * requirement specification - ТЗ для нашей курсовой
 */
public class ReqSpec {
    private StringBuilder content;

    public ReqSpec() {
        content = new StringBuilder();
    }

    public void addContent(String text) {
        content.append(text);
    }

    public String getContent() {
        return content.toString();
    }

    /**
     * создаем наш memento, куда сохраняем текующее состояние нашего ТЗ (поле content)
     * @return новый memento
     */
    public ReqSpecState save() {
        return new ReqSpecState(content.toString());
    }

    /**
     * Восстанавливает наше техническое задание к прежднему состоянию (если мы например сделали ошибку)
     * @param memento наш "снимок" т.е. прежднее состояние нашего ТЗ
     */
    public void restore(ReqSpecState memento) {
        content = new StringBuilder(memento.getContent());
    }
}
```
CourseWork.java
``` java
/**
 * наш CareTaker, будет сохранять состояние ReqSpec и восстанавливать его если нужно
 */
public class CourseWork {
    private ReqSpec requirements;

    private ReqSpecState lastSaveRS;

    public CourseWork(ReqSpec reqSpec) {
        requirements = reqSpec;
    }

    public void work(String content) {
        requirements.addContent(content);
    }

    public String print() {
        return requirements.getContent();
    }

    public void SaveRS() {
        lastSaveRS = requirements.save();
    }

    public void undo() {
        requirements.restore(lastSaveRS);
    }
}

```
Ссылки
https://github.com/eugenp/tutorials/blob/master/patterns-modules/design-patterns-behavioral-2/src/main/java/com/baeldung/memento/TextEditor.java
https://www.baeldung.com/java-memento-design-pattern

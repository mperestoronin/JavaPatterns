# Стратегия strategy
### Краткое описание
Этот шаблон предназначен для определения группы классов, которые представляют собой набор возможных вариантов поведения. Это дает возможность гибко подключать те или
иные наборы вариантов поведения во время работы приложения, меняя его функциональность "на ходу".

Простым языком - этот шаблон дает возможность в процессе выполнения выбрать стратегию (алгоритм, инструмент, подход) решения задачи. Он позволяет вынести семейство схожих алгоритмов, решающих конкректную задачу. Реализация алгоритмов выносится в отдельные классы и предоставляется возможность выбирать алгоритмы во время выполнения программы

### Применение 
Данный шаблон проектирования следует применять когда:
- У вас есть множество похожих реализаций отличающихся незначительным поведением. Можно вынести отличающее поведение в классы-стратегии, а повторяющий код свести к единому классу-контекста. 

- Ваш алгоритм реализован в супер-классе с множественными условными операторами. Выделите блоки условных операторов в отдельные классы-стратегии, а управление вызовов нужных доверьте классу-контекста. 

- Конкретные стратегии позволяют инкапсулировать алгоритмы в своих конкретных классах. Используйте этот подход для снижения зависимостей от других классов. 

- В зависимости от ситуации вы можете менять стратегию выполнения задачи в процессе выполнения программы. Например, в зависимости от скорости интернета использовать разные стратегии-поведения, возвращающие разный набор данных для отображения страницы.
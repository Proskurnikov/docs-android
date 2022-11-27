# Factory pattern

Не понятны для меня объеснения из вики и других источников. Для понимая очень помогла только книга head first про паттерны.

Поэтому здесь буду писать те моменты, которые помогают мне понимать паттерны.

Пример из книги head first:

```java
public class SimplePizzaFactory {
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        if (type.equals(“cheese”)) {
            pizza = new CheesePizza();
        } else if (type.equals(“pepperoni”)) {
            pizza = new PepperoniPizza();
        } else if (type.equals(“clam”)) {
            pizza = new ClamPizza();
        } else if (type.equals(“veggie”)) {
            pizza = new VeggiePizza();
        }
        return pizza;
    }
}
```
т.е. когда видим код, в которм на основании какого-то условия создаются разные объекты (а именно разные реализации какого-то родительского класса) то стоит задуматься о выносе его в фабрику. Также одно из условий выноса в фабрику создания объекта, когда такое создание может происходить в разных местах, т.е. в последующем, в случае изменений (например, добавления новых наследуемых классов) надо будет изменять код только в одном месте.

Также во многих примерах выносят в фабрику создание одного объекта, без выбора, исходя из каких-либо условий. Для меня было непонятно, для чего так делается, т.к. это не совпадает с условиями, что приведены выше.

Помог этот [ответ](https://stackoverflow.com/questions/69849/factory-pattern-when-to-use-factory-methods). Когда есть какие-то детали создания объекта (и они, напрмер, могут быть одинаковыми в разных местах) можно вынести эти детали в фабрику (т.е. опять, в последующем, нужно будет, при необходимости детали создания менять в одном месте). 

```C#
public interface IThingFactory
{
    Thing GetThing(string theString);
}

public class ThingFactory : IThingFactory
{
    public Thing GetThing(string theString)
    {
        return new Thing(theString, firstDependency, secondDependency);
    }
}
```
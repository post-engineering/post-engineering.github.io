---
layout: post
category : tech
tagline: "Java & Immutable Object pattern"
tags : [immutability, java]
excerpt: Стоит ли использовать Immutable Object в Java
---
[Immutable Object](https://en.wikipedia.org/wiki/Immutable_pattern) - шаблон проектирования, который подразумевает создание объекта, который не может быть изменен. Используется как для устранения дорогих операций копирования и сравнения, так и в многопоточной среде, для предотвращения Shared Mutable State между потоками.  

#Пример
В Java может быть реализован так:
```java
final class Unicorn {
	private final String name;
	private final String symbol;

	public Unicorn(final String name, final String symbol) {
		this.name = name;
		this.symbol = symbol;
	}

	public String getName() {
		return name;
	}

	public String getSymbol() {
		return symbol;
	}
}

Unicorn lady = new Unicorn("Lady Rainicorn", "rainbow");
```
Теперь можем передавать наш объект между потоками. Никто его не изменит.

# История из жизни
[Чекстайл](http://checkstyle.sourceforge.net) завалил билд. Причина - [превышено количество параметров, передаваемых в конструктор](http://checkstyle.sourceforge.net/config_sizes.html#ParameterNumber). Посмотрели - конструктор на 22 аргумента. Сразу возникли вопросы: *"WAT!?!?", "А зачем такой большой плоский объект?", "Как же его создавать?"*.  
Ответы, которые можно услышать:  
 - Плоская структура объекта взвана тем, что мапили JSON. 
    - **ОК, допустим**   
 - Большой конструктор вызван тем, что общий подход к построению системы предполагает, что все Data Objects будут Immutable. 
    - **Воу, Воу. Палехче!**  
 - Создавать руками планируется только в тестах. Можно билдер создать. Но все равно, все поля класса дожны быть инициализированы в конструкторе, ведь они `final` и .... *рассказ про то, как Java Memory Model может поступить в многопоточной среде при инициализации полей*. 
   - **Хитро, хитро.**

Проблему поправили. Теперь чекстайл позволяет передавать до 23 параметров в метод ;)

TODO: Java Memory model
Из конструктора передавать this другим объектам - зло. Вызывать методы в конструкторе - зло.

# Immutable Java
Несколько месяцев опросов коллег помогли создать список "За и Против". 
## Pros
### Работа в многопоточной среде. 
Здорово, когда объекты, которые разделяют несколько потоков сохраняют свое состояние неизменным. Это позволяет предотвратить множество сложно-обнаруживаемых ошибок.
### Partially immutable
Частично неизменяемый объект может быть не только полезен, но и логичен. Если Entity (объект, который мы достали из БД) позволяет изменить ID записи, то что-то не так. Каждый программист зайдет - посмотрит "что за ерунда" и "зачем это было сделано".
## Cons
### Java EE
Контейнеры приложений. В большинстве случаев они прячут от программиста многопоточную среду в которой приложение работает. Это снимает много вопросов, связанных с синхронизацией и shared mutable state. (прим. Добавляет много других. Специфических ;))
### JavaBeans
[JavaBeans — классы в языке Java, написанные по определённым правилам.](https://ru.wikipedia.org/wiki/JavaBeans)  
Классический подход к DataObject в Java. Используется в большинстве библиотек.
Многие фреймворки будут надеяться найти сеттеры и геттеры в вашем объекте. К примеру, Spring. Не огорчайте его.
### Java
Java, язык в возрасте. Да, он позволяет создавать Immutable объекты. Однако, работать с ними не просто. Особенно с большими. Не хватает синтаксического сахара.  
Вот пример паттерна на других ЯП  
**Groovy:**
```groovy
@Immutable
final class Pony {
	String name
	String color
	Integer age
}
new Pony(name = "Princess Celestia", color = "white", age = "70")
```
**Scala:**
```scala
case class Pony(name :String, color :String, age :Integer)
new Pony(name = "Princess Celestia", color = "white", age = "70")
```
```java
public class ImutableCatSpecificationDataObject {

    private String name;
    private String color;
    private String foo;
    private String bar;
    private String addYourFavoritePropertyHere;

    public ImutableCatSpecificationDataObject(String name, String color, String foo, String bar, String addYourFavoritePropertyHere) {
        this.name = name;
        this.color = color;
        this.foo = foo;
        this.bar = bar;
        this.addYourFavoritePropertyHere = addYourFavoritePropertyHere;
    }

    public String getName() {
        return name;
    }

    public String getColor() {
        return color;
    }

    public String getFoo() {
        return foo;
    }

    public String getBar() {
        return bar;
    }

    public String getAddYourFavoritePropertyHere() {
        return addYourFavoritePropertyHere;
    }
}
```

#Выводы
Использовать Immutable Object паттерн можно и нужно. Как часто? Из проекта в проект мнение различается кардинально.  
А что думаете вы?




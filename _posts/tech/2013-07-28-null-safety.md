---
layout: post
category : tech
tagline: "Null safety"
tags : [code smells]
excerpt: Несколько лет назад видел такой подход к написанию кода. Думал, что больше не увижу.
---
Несколько лет назад видел такой подход к написанию кода. Думал, что больше не увижу. Ошибался.

Есть в разработке такой термин, как null safety. Идеалогия борьбы такова: не допустить появление нулей (null).
Ведь нули ведут нас к `NPE`, а `NPE` - это АйАйАй. Мне очень нравится подход Kotlin в данном направлении: [Kotlin Null Safety](http://confluence.jetbrains.com/display/Kotlin/Null-safety)

```js
One of the most common pitfalls in Java programming is accessing a member of a null reference, that results in a NullPointerException,
```

Но разговор сегодня о другой крайности.
Итак, у нас есть метод:

```java
String doSmth(String str, Integer times) {
	String out = str;
	for i in range(times) {
		out = doSmthInternal(out);
	}
	return out;
}
```

Что-то тут не так. А, да. Конечно! Что же будет, если одним из параметров придет null? Все же упадет! Решение!

```java
String doSmth(String str, Integer times) {
	if(times == null) {
		return null;
	}
	String out = str;
	for i in range(times) {
		out = doSmthInternal(out);
	}
	return out;
}
```

Вот. Обезопасились. Теперь код будет работать.
#Стоп
Что мы только что сделали! Мы предусмотрели случай нарушения контракта метода!? Сигнатура метода говорит нам, что метод принимает `String` и `Integer` и возвращает `String`. Если клиент, вызывает метод, и неудовлетворяет требования контракта, то мы втихаря отвечаем гадостью на гадость. Типа: "Ты нам фигню на вход, а мы тебе в ответ - фигню на выход".
И ладно бы данное решение использовалось для методов, аля `String.isEmpty` или в языках с ограниченными механизмами обработки ошибок. Но при использовании данного подхода во ВСЕМ КОДЕ! Это слишком.

А, ладно с контрактом. Давайте посмотрим на проблему с другой стороны. Дебаг. Он великолепен.

```java
/**
 * Extracts digits from string
 * returns digits string or null if nothing found
 */
@Nullable String getDigits(String str) {
  return getDigitsInternal(str)  
}

/**
 * Multiplies string by 2
 */
String dobleString(String str) {
  if(str != null) {
    return str + str;
  }
  return null;
}

String toUpperCase(String str) {
  if(str != null) {
    return str.toUpper();
  }
  return null;
}

public static void main() {
  String str = "abc"; // <-- contains no digits
  String digits = getDigits(str);
  sendWithTcp(toUpperCase(doubleString(digits))); // <-- NPE in send() method
}
```

Стектрейс мы увидем на методе `sendWithTcp`. Ошибка была при вызове `doubleString`. Но `toUpperCase` проглатил ерунду на вход и выдал ерунду на выход.

Смысл таких выкрутасов? "Наш код не падает, но выдает ерунду?". Для меня это самая неприятная ситуация. Я хочу, чтобы программа показала, где ошибка. Я не хочу их скрывать. Откладывать ошибки до последнего? Не, не вариант.

Ребята, `NPE` это не плохо. `NPE` - это маркер ошибки. Он должен указывать максимально точно, где проблема.

Одна из моих любимых статей в блоге QT - [Apology of the NULL pointer](http://blog.qt.digia.com/blog/2010/08/11/apology-of-the-null-pointer/). Мнение NPE о людях ;)

Главное, чтобы котята не умирали.




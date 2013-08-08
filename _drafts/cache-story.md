---
layout: post
category : tech
tagline: "Cache"
tags : cache code-smells
excerpt: Как не надо делать кэш.
---

#Задача

В сказочной стране люди писали сервис. И код сервиса выглядел так

```java
public Data doTingz(param) {
  doSmthFast();
  doSmthFast();
  
  doSmthSlow(param); // !
  
  doSmthFast();
}
```

Медленно работал сервис. Но было решение. Кэширование. Самая сложная часть по всем критериям подходила под Кэш.
Но кэш ведь штука сложная. И с ним полно способов выстрелить себе в ногу. Один из них и рассмотрим.

#Решение
```java
public Data doTingz(param) {
  doSmthFast();
  doSmthFast();
  
  var data = cache.get();
  if(!data) {
    data = doSmthSlow(param);
    cache.put(data); // !
  }
  
  doSmthFast();
}
```
Сколько потенциальных ошибок вы видите в данном решении? Многопользовательская многопоточная среда приподнесет сюрпризы!
Но есть в данном подходе и архитектурная проблема. А именно то, что кэширование внедрено в код бизнес метода. Кэширование стало частью логики нашего сервиса. У нас появилась еще одна зависимость.

*Кешинование не должно быть внедрено в код. Сервис должен быть накрыт слоем кеширования.*

> Neither party is aware that caching occurs. It as well improves performance but does that by allowing the same data to be read multiple times in a fast fashion. [Spring Cache Abstraction](http://static.springsource.org/spring/docs/3.1.0.M1/spring-framework-reference/html/cache.html)

Выводы:
- Кеширование должно быть основано на архитектурных слоях приложения (Делегат, Декоратор и Прокси вам в руки)
- Код приложения не должен знать о наличии кеширования.

Кеширование и авторизация - две самые популярные вещи, которые растекаются по бизнес методам, нарушая логику и усложняя код. Не надо так.
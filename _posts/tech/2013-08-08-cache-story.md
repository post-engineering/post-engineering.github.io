---
layout: post
title: "Как не надо делать кэш"
categories: blog
author: wonderbeat
tags: [cache code-smells]
image:
  feature: so-simple-sample-image-2.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---

## Задача

В сказочной стране люди писали сервис. И код сервиса выглядел так:

~~~ java
public Data doTingz(param) {
  doSmthFast();
  doSmthFast();

  Data data = doSmthSlow(param); // !

  doSmthFast(data);
  return data;
}
~~~

Медленно работал сервис. Но было решение. Кэширование. Самая сложная (и медленная) часть по всем критериям подходила под Кэш.
Но кэш ведь штука сложная, с ним полно способов выстрелить себе в ногу.

## Плохое решение
~~~ java
public Data doTingz(param) {
  doSmthFast();
  doSmthFast();

  Data data = cache.get();
  if(data == null) {
    data = doSmthSlow(param);
    cache.put(data); // !
  }

  doSmthFast(data);
  return data;
}
~~~
Сколько ошибок вы видите в данном решении?
Здесь и "классические" многопоточные проблемы (что произойдет, если несколько потоков одновременно не обнаружат закэшировнных данных?), и неочевидные ограничения на хранение в кэше значения `null`.
Но главная проблема в этом коде — архитектурная. А именно в том, что кэширование внедрено в код бизнес метода. Кэширование стало частью логики нашего сервиса. У нас появилась еще одна зависимость, нам стало ещё сложнее тестировать этот класс.

*Кешинование не должно быть внедрено в код. Сервис должен быть "накрыт" слоем кеширования.*

> Neither party is aware that caching occurs. It as well improves performance but does that by allowing the same data to be read multiple times in a fast fashion.
[Spring Cache Abstraction](http://static.springsource.org/spring/docs/3.1.0.M1/spring-framework-reference/html/cache.html)

### Выводы
- Кеширование должно быть основано на архитектурных слоях приложения (Делегат, Декоратор и Прокси вам в помощь).
- Код приложения не должен знать о наличии кеширования.
- Если кэширование отказало, ваше приложение должно продолжить работать, пусть и медленнее.

Кеширование и авторизация - две самые популярные вещи, которые растекаются по бизнес методам, нарушая логику и усложняя код. Не надо так.

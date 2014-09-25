---
layout: post
title: "Задачка по Cyclomatic complexity"
categories: blog
excerpt: "В репозитории [JSHint](https://github.com/jshint/jshint) наткнулся на интересную задачку."
author: wonderbeat
tags: [cyclomatic complexity, java, javascript]
image:
  feature: so-simple-sample-image-4.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---


В репозитории [JSHint](https://github.com/jshint/jshint) наткнулся на интересную задачку.

Даны две фукции:


~~~ js
function(someVal) {
    switch (someVal) {
        case 1:
        case 2:
        case 3:
            doSomething();
            break;
        default:
            doSomethingElse();
            break;
    }
}
~~~

~~~ js
function(someVal) {
    if (someVal === 1 || someVal === 2 || someVal === 3) {
        doSomething();
    } else {
        doSomethingElse();
    }
}
~~~

JSHint говорит, что СС для данных функций равны 4 и 2 соответственно.
Задача - выяснить истину и поправить библиотеку.

С первого взгляда, все казалось довольно просто. СС для обеих функций должно равняться 2.
Но парень из [Германии](https://github.com/WolfgangKluge) усомнился в решении.
В пример привел [статью](http://gmetrics.sourceforge.net/gmetrics-CyclomaticComplexityMetric.html) и [PDF с примерами на фортране](http://www.literateprogramming.com/mccabe.pdf).
И говорит парень, что все должно быть по 4 и рассчет данной циферки *not always human intuitive*.

Хорошо. Тут либо читать статью, либо приводить другие аргументы. Статью читать не хотелось. Решил Я прогнать данные функции через чекстайл. Он то для Java, а она то Enterprise.

Интересные результаты:
{% gist 6078981 %}

Чекстайл полностью согласен с Германией. Придется читать статью.

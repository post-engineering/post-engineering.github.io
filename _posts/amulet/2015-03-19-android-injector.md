---
layout: post
title: "Android Injector"
categories: blog
excerpt: "Возможно потому, что под Андроид нужно писать на джавке, так хочется притащить в андроидное приложение некоторые ставшие привычными вещи."
author: ratsam
tags: [androgame]
image:
  feature: amulet/bg-injector.png
comments: true
share: true
---

Возможно потому, что под Андроид нужно писать на джавке, так хочется притащить в андроидное приложение некоторые ставшие привычными вещи. Например, хочется иметь возможность попросить заинжектить все зависимости компонента. Нет, конечно, хочется, чтобы этого даже просить не нужно было, но в андроидной инфраструктуре это будто бы невозможно.  

Единственный [полиморфный] контекст до которого в Андроиде могут дотянуться все компоненты, участвующие в обычном жизненном цикле, — это `Application`. С другой стороны, завязывать все-все-все компоненты на какой-то конкретный апп (и пораждать циклические зависимости) нет никакого желания, и на свет просится интерфейс `Injector` с единственным методом `void inject(Object o)`. Более того, этот тощий интерфейс даже заселяется в отдельный, специально для него созданный, модуль, за что на работе меня некоторые мои коллеги бы прокляли.  
Сам же Application использует Dagger от Square, подробнее о котором можно [тут](http://square.github.io/dagger/) почитать.  

Даггер непривычен тем, что в нём при описании модулей нужно перечислять все классы, которым будут предоставляться зависимости. Каждый раз, создав новый фрагмент и попробовав вызвать `.inject(this)` из метода `onCreate`, прийдётся встречаться с сообщением об ошибке (времени исполнения, конечно же), добавлять фрагмент в список `injects` одного из модулей даггера и пересобирать приложение.  
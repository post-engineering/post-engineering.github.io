---
layout: post
title: "Google-аутентификация в андроид-приложении"
categories: blog
excerpt: "Как проще всего сделать аутентификацию в приложении под Андроид? Использовать Google аккаунт! На самом деле нет."
author: ratsam
tags: [androgame]
image:
  feature: so-simple-sample-image-3.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---

Как проще всего сделать аутентификацию в приложении под Андроид? Использовать Google аккаунт!  
На самом деле нет.  
Если вы знаете что делать, то простая форма с логином и паролем обойдется меньшей кровью.  
В Android Studio есть опция создания activity для аутентификации через Google+, но генерируется настолько устаревшая форма для логина, что результат даже отказывается компилироваться.  

В первую очередь, ещё до того как приступить к написанию кодулек, нам понадобится зарегистрировать наше приложение в [Google APIs Console](https://code.google.com/apis/console/).  
Для debug keystore можно выполнить команду
`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore -list -v` (пароль android).  
Также **обязательно** нужно во вкладке Consent screen выбрать Email address (на обнаружение этой ошибки ушло 20 минут моих нервов).  
Если приложение не зарегистрировать, `GoogleAuthUtil.getToken` будет возвращать `null`.  

Код получения токена я практически полностью скопировал из примера, однако оставлять его в таком состоянии посчитал невозможным. Statefull логику хорошо бы сразу перенести во фрагмент, переживающий пересоздание активити (например, при повороте телефона).  
Сигнатура `AsyncTask<Void, Void, Void>` явно намекает на то, что `AsyncTask` здесь использован не по назначению, но это повод для будущих изменений.
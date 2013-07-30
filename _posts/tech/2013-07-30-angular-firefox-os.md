---
layout: post
category : tech
tagline: "AngularJS for Firefox OS"
tags : [AngularJs, Firefox OS]
excerpt: Вот и приняли мое приложение в Файрфокс маркет. Теперь расскажу о подводных камнях.
---
Сначала о приложении. Решил Я портировать Google Authenticator под Firefox OS.
Вот ссылка: . [Скачиваем, тестируем.](https://marketplace.firefox.com/app/authenticator-1/)
![Application in Firefox OS Market](/images/authenticator/market.png)

С технической точки зрения - это HTML5 приложение на AngularJS. Самая мудреная вещь в нем - это TOTP алгоритм.
Да и алгоритм то не сложный, если RFC почитать. Сложным он становится, когда в для реализации берешь JavaScript.

Разработка приложения заняла около недели. Путь в магазин - месяц. 

#Разработка
Ну тут все просто. TDD и Angular. Немного повозился с TOTP и примеры из RFC стали проходить. Решил зайти с помошью приложения в Google - хрен. Гугл совершают доп операцию с ключем (оказалось так делают все). Ну ничего, допилили - заработало.
![Application homescreen](/images/authenticator/application.png)

С UI чуть поинтереснее. Firefox OS платформа новая. Стандартные компоненты под UI для нее еще в разработке. В результате, пришлось стянуть их с ГитХаба. Сыровато, но работает.

#Маркет

Отправил приложение в маркет и обалдел. В очереди на ревью находится более 300 человек и меня поставили в конец.
За неделю очередь продвинулась на 30 человек. Грусть, тоска. Очередь удалось пройти за 3 недели.
Приложение завернули. 

> Application opens with a blank screen

А телефона на руках с Firofox OS нету. Только эмулятор в браузере. И в эмуляторе работает.
Уж не помню как, но на Firefox OS форумах узнал Я о [CSP](https://developer.mozilla.org/en-US/docs/Security/CSP) (Content Security Policy) от Firefox. Набор правил и ограничений к веб приложению. Почти не используется в вебе, зато включен для всех приложений на Firefox OS.

>Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks, including Cross Site Scripting (XSS) and data injection attacks. These attacks are used for everything from data theft to site defacement or distribution of malware.

Идея про то, что Angular не проходит CSP оказалась правильной. Но без реального телефона, баги в приложении пришлось искать методом "РевьюДебага". С четвертого раза приложение прошло ревью.
Вот несколько советов по применению Angular для приложения:

* *Добавляем ng-csp директиву*. Без нее приложение не поднимется точно.
* *Добавляем sanitization для URL*. Такие директивы, как `ng-href` геренинуют абсолютный урл, который начинается с `app://` для приложений. Данный урл не является безопасным, по мнению Angular, и к урлу добавляется префикс `unsafe:app://`. Телефон такие ссылки не открывает.

```js
angular.module('authenticatorApp')
    .config( [
        '$compileProvider',
        function( $compileProvider )
        {
            /**
             * An interesting thing about Firefox OS and Angular.
             * ng-href produces canonical url: http:/yoursite.com/href
             * In Firefox OS it looks like app://yourappId/href
             * But Angular prfixes this URL with 'unsafe', because 'app' protocol is unknown.
             */
            $compileProvider.urlSanitizationWhitelist(/^\s*(app|https?|ftp|mailto|chrome-extension):/);
        }
    ]);
```

Технически, все. 
Была еще проблема с security policy и описанием приложения. Но эта беда быстро решается. Все написано в гайдах для девелоперов.

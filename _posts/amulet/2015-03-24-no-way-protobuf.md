---
layout: post
title: "Protobuf не взлетел"
categories: blog
excerpt: "В качестве средства описания схемы моделей для клиент-серверного взаимодействия прийдётся пробовать что-то другое"
author: ratsam
tags: [androgame]
image:
  feature: amulet/bg-proto.png
comments: true
share: true
---

Protobuf не взлетел.  
Я воображал, как здорово было бы завести отдельный репозиторий, в котором хранились бы только схемы моделей, собирать их в отдельный артефакт и подключать как зависимость во все взаимодействующие приложения. Серверная часть генерировала бы настоящие — гугловые — протобафные классы, андроидный клиент — свои, на Wire.  
Начав с конца и успев наесться других проблем, я всё-таки дошёл до того момента, когда наивный воображатель обнаруживает отсутствие мавеновских плагинов для протобафа, просто работающих из коробки. И это стало последным фактором против использования protobuf.  
Дальше — JAXB.  

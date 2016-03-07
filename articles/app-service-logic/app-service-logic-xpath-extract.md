<properties
   pageTitle="Использование средства извлечения XPath BizTalk в приложениях логики в службе приложений Azure | Microsoft Azure"
   description="Средство извлечения XPath BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Средство извлечения XPath BizTalk

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2014-12-01-preview.

Соединитель средства извлечения XPath BizTalk помогает вашему приложению находить и извлекать данные из содержимого XML по заданному XPath.

## Использование средства извлечения XPath BizTalk
1. Чтобы использовать средство извлечения XPath BizTalk, необходимо сначала создать экземпляр приложения API средства извлечения XPath BizTalk. Это можно сделать либо в процессе, при создании приложения логики, либо выбрав приложение API средства извлечения XPath BizTalk на сайте Azure Marketplace.

	>[AZURE.NOTE] Для средства извлечения Xpath BizTalk не определены параметры конфигурации.
2. [Создайте новое приложение логики]. Откройте раздел "Триггеры и действия" в приложении логики, чтобы открыть конструктор приложений логики для настройки потока.
3. В конструкторе в правой части перечисляются приложения API, доступные для построения потока. Найдите приложение BizTalk XPath Extractor. После его выбора в поток добавится приложение Xpath Extractor, и будет подготовлен его экземпляр.
4. После подготовки конструктор отобразит действие, связанное с приложением API BizTalk XPath Extractor: ![Выбор действия средства извлечения XPath BizTalk][1].

5. Выберите "Извлечь с помощью XPath". "Извлечь с помощью XPath" вычисляет входное выражение xpath для заданных входных данных XML: ![Ввод средства извлечения XPath BizTalk][2].

	Параметр|Тип|Описание параметра
---|---|---
XPath|string|Путь запроса внутри XML.
Входные данные XML|string|Входное содержимое XML.

Действие возвращает выходные данные в виде строки Result. Result содержит значение пути запроса внутри XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Создайте новое приложение логики]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->
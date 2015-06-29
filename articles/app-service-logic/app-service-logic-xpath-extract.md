<properties
   pageTitle="Средство извлечения XPath BizTalk"
   description="Средство извлечения XPath BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="prkumar"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/20/2015"
   ms.author="rajram"/>

#Средство извлечения XPath BizTalk

Соединитель средства извлечения XPath BizTalk помогает вашему приложению находить и извлекать данные из содержимого XML по заданному XPath.

##Использование средства извлечения XPath BizTalk
1. Чтобы использовать средство извлечения XPath BizTalk, необходимо сначала создать экземпляр приложения API средства извлечения XPath BizTalk. Это можно сделать либо в процессе, при создании приложения логики, либо выбрав приложение API средства извлечения XPath BizTalk на сайте Azure Marketplace.

	>[AZURE.NOTE]
2. [Создайте новое приложение логики]. Откройте раздел «Триггеры и действия» в созданном приложении логики, чтобы открыть конструктор приложений логики для настройки потока.
3. В конструкторе в правой части перечисляются приложения API, доступные для построения потока. Найдите приложение BizTalk XPath Extractor. С его выбором в поток добавится приложение Xpath Extractor и будет подготовлен его экземпляр.
2. После подготовки конструктор отобразит действие, связанное с приложением API BizTalk XPath Extractor.

![Выбор действия средства извлечения XPath BizTalk][1]

3. Выберите «Извлечь с помощью XPath».

«Извлечь с помощью XPath» вычисляет входное выражение xpath для заданных входных данных XML.

![Ввод средства извлечения XPath BizTalk][2]

<table>
	<tr>
		<th>Параметр</th>
		<th>Тип</th>
		<th>Описание параметра</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>string</td>
		<td>Путь запроса внутри XML.</td>
	</tr>
	<tr>
		<td>Входные данные XML</td>
		<td>string</td>
		<td>Входное содержимое XML.</td>
	</tr>
</table>

Действие возвращает выходные данные в виде строки Result. Result содержит значение пути запроса внутри XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Создайте новое приложение логики]: app-service-logic-create-a-logic-app.md

<!---HONumber=58_postMigration-->
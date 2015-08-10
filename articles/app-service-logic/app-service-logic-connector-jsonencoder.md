<properties
   pageTitle="Кодировщик JSON BizTalk"
   description="Кодировщик JSON BizTalk"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="06/14/2015"
   ms.author="rajram"/>

#Кодировщик JSON BizTalk
Соединитель для кодирования и декодирования позволяет вашему приложению взаимодействовать с данными JSON и XML. Он может преобразовать экземпляр JSON в XML, и наоборот.

##Использование кодировщика JSON BizTalk
Чтобы использовать кодировщик JSON BizTalk, необходимо сначала создать экземпляр приложения API для кодировщика JSON BizTalk. Это можно сделать либо в процессе, при создании приложения логики, либо выбрав приложение API для кодировщика JSON BizTalk на сайте Azure Marketplace.

##Использование кодировщика JSON BizTalk в области конструктора приложений логики
Выполните шаги из раздела [Создание приложения логики]. Кодировщик JSON BizTalk можно использовать как действие. У него нет никаких триггеров.

###Действие
- На правой панели щелкните кодировщик JSON BizTalk.

	![Параметры действия][3]
- Щелкните ->

	![Список действий][4]
- Кодировщик JSON BizTalk поддерживает два действия. Выберите *Xml в JSON*

	![Входные данные для преобразования XML в JSON][5]
- Введите входные данные для действия и настройте их

	![Кодирование и отправка настроены][6]

<table>
	<tr>
		<th>Параметр</th>
		<th>Тип</th>
		<th>Описание параметра</th>
	</tr>
	<tr>
		<td>Входной XML-файл</td>
		<td>object</td>
		<td>Содержимое входного XML-файла</td>
	</tr>
	<tr>
		<td>Удаление внешнего конверта</td>
		<td>string</td>
		<td>Флаг для удаления корневого узла из XML-содержимого</td>
	</tr>
</table>


Действие возвращает представление JSON входного содержимого.

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. раздел [Управление приложениями API и соединителем, а также их отслеживание](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Создание приложения логики]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO5-->
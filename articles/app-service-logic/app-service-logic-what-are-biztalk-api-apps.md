<properties 
	pageTitle="Что такое соединители и приложения API BizTalk" 
	description="Сведения о приложениях API, соединителях и приложениях API BizTalk" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/20/2016" 
	ms.author="mandia"/>

# Что такое соединители и приложения API BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Службы приложений Azure строятся на принципе расширяемости и общего подключения через приложения API. *Соединитель* — это тип приложения API, предназначенного для подключения. Соединители, как и любые другие приложения API, используются из веб-приложений, мобильных приложений и приложений логики. Соединители облегчают подключение к существующим службам и помогают управлять проверкой подлинности, предоставляя мониторинг, аналитику и многое другое.

Любой разработчик может создавать свои собственные приложения API и разворачивать их в частном порядке. В будущем разработчики могут совместно использовать свои приложения API и получать от них прибыль в Marketplace.

![Приложения API в Marketplace](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

Чтобы ускорить построение разработчиками решений с использованием службы приложений Azure, группа Azure добавила в Marketplace ряд соединителей для реализации многих распространенных сценариев. Кроме того, для охвата службой приложений сложных и расширенных сценариев также доступен ряд возможностей уровня Premium и BizTalk.

В службе приложений Azure доступны различные «уровни» службы. Все уровни содержат все соединители и приложения API, включая их полную функциональность.

В разделе [цен на службу приложений](https://azure.microsoft.com/pricing/details/app-service/) описываются эти уровни служб, а также перечисляется, что в них включено. В следующих разделах рассматриваются различные категории приложений API BizTalk и соединителей.


## Гибридные соединители 
Гибридные соединители расширяют сферу применения служб приложений до корпоративного уровня с возможностью подключения к системам [SAP](app-service-logic-connector-sap.md), [Oracle](app-service-logic-connector-oracle.md), [DB2](app-service-logic-connector-db2.md), [Informix](app-service-logic-connector-informix.md) и WebSphere MQ.

## Службы EAI и EDI
Для создания важных коммерческих приложений требуется не только подключение. Основываясь на фундаменте ведущей отраслевой платформы интеграции от корпорации Майкрософт — BizTalk Server — приложения API BizTalk предоставляют расширенные возможности интеграции, которые могут с легкостью использоваться в ваших веб-приложениях, мобильных приложениях и приложениях логики. Вот некоторые из этих возможностей интеграции: [проверка](app-service-logic-xml-validator.md), [извлечение](app-service-logic-xpath-extract.md), [преобразование](app-service-logic-transform-xml-documents.md), [кодировщики](app-service-logic-connector-jsonencoder.md), [управление торговыми партнерами](app-service-logic-connector-tpm.md) и поддержка форматов EDI, например [X12](app-service-logic-connector-x12.md), [EDIFACT](app-service-logic-connector-edifact.md) и [AS2](app-service-logic-connector-as2.md).

Дополнительные ресурсы: [Соединители "бизнес–бизнес" и приложения API в службе приложений Microsoft Azure](app-service-logic-b2b-connectors.md); [Создание процесса B2B](app-service-logic-create-a-b2b-process.md); [Создание соглашения между торговыми партнерами](app-service-logic-create-a-trading-partner-agreement.md); [Отслеживание сообщений B2B](app-service-logic-track-b2b-messages.md).


## Правила
Бизнес-правила инкапсулируют политики и решения, которые управляют бизнес-процессами. Как правило, правила являются динамическими и изменяются со временем по разным причинам, например в результате изменения бизнес-планов, нормативов, и по многим другим причинам. [Правила BizTalk в службах приложений](app-service-logic-use-biztalk-rules.md) позволяют выделить эти политики из кода приложения и сделать процесс изменения проще и быстрее.

## Список соединителей и приложений API
В разделе [Список соединителей и приложений API](app-service-logic-connectors-list.md) можно найти полный список соединителей и приложений API, содержащихся в каждой категории, включая стандартные соединители, BizTalk EAI, соединители Premium и так далее.
 

<!---HONumber=AcomDC_0420_2016-->
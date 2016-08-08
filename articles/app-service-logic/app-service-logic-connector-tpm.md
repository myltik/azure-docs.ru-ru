<properties 
   pageTitle="Использование соединителя BizTalk Trading Partner Management в приложениях логики | Служба приложений Microsoft Azure" 
   description="Как создать и настроить соединитель BizTalk Trading Partner Management или приложение API и использовать его в приложении логики в службе приложений Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Приступая к работе с BizTalk Trading Partner Management: добавление в приложение логики

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Служба TPM позволяет определять и сохранять отношения между компаниями, такие как партнеры и соглашения, вместе с различными объектами, такими как схемы и сертификаты. Эти отношения затем можно использовать с помощью связанных служб API, таких как AS2, Edifact и X12.

Это приложение API TPM является базовым требованием соединителя AS2, приложений API X12 и EDIFACT. BizTalk Trading Partner Management можно добавить в рабочий процесс компании и обрабатывать данные в рамках рабочего процесса «бизнес-бизнес» в приложении логики.

## Предварительные требования
- Пустая база данных SQL Azure. Необходимо сначала создать пустую базу данных SQL Azure, прежде чем создавать новое приложение API TPM.

## Основные сведения о партнерах, соглашениях и профилях
Узнайте больше о [создании соглашения с торговыми партнерами][1].

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в рабочий бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Если вы хотите начать работу с приложениями логики Azure до создания учетной записи Azure, перейдите на веб-сайт [пробного использования приложений логики](https://tryappservice.azure.com/?appservice=logic). На этом сайте вы сможете быстро создать кратковременное приложение логики начального уровня в службе приложений. Никаких кредитных карт и обязательств.

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. статью [Управление встроенными приложениями API и соединителями, а также их мониторинг](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_0727_2016-->
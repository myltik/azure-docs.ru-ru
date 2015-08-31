<properties 
   pageTitle="Управление торговыми партнерами BizTalk" 
   description="Управление торговыми партнерами BizTalk" 
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
   ms.date="08/19/2015"
   ms.author="rajram"/>

#Управление торговыми партнерами BizTalk
Служба TPM от Microsoft Azure позволяет определять и сохранять отношения между компаниями, такие как партнеры и соглашения, вместе с различными объектами, такими как схемы и сертификаты. Эти отношения затем можно использовать с помощью связанных служб API, таких как AS2, Edifact и X12.

Это приложение API TPM является базовым требованием соединителя AS2, а также приложений API X12 и EDIFACT.

##Предварительные требования
- Пустая база данных SQL Azure. Необходимо сначала создать пустую базу данных SQL Azure, прежде чем создавать новое приложение API TPM.

##Основные сведения о партнерах, соглашениях и профилях
Чтобы получить дополнительные сведения о соглашении торговых партнеров, щелкните [здесь][1].

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в рабочий бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. статью [Управление встроенными приложениями API и соединителями, а также их мониторинг](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO8-->
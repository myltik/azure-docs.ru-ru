<properties
   pageTitle="Создание пространства имен служебной шины с помощью портала Azure | Microsoft Azure"
   description="Чтобы начать работу со служебной шиной, необходимо пространство имен. В этой статье показано, как создать его с помощью портала Azure."
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Создание пространства имен служебной шины с помощью портала Azure
Пространство имен — это общий контейнер для всех компонентов обмена сообщениями. В одном пространстве имен могут содержаться несколько очередей и разделов. Часто пространства имен выполняют роль контейнеров приложений. В настоящее время существует два различных способа создания пространства имен служебной шины.

1.	Портал Azure (в этой статье)

2.	[Шаблоны ARM][create-namespace-using-arm]

##Создание пространства имен на портале Azure
[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен служебной шины.

##Дальнейшие действия

В нашем репозитории GitHub доступны образцы, демонстрирующие расширенные возможности обмена сообщениями служебной шины Azure: [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples].

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->
<properties
    pageTitle="Создание пространства имен служебной шины с помощью портала Azure | Microsoft Azure"
    description="Чтобы начать работу со служебной шиной, необходимо пространство имен. В этой статье показано, как создать его с помощью портала Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# Создание пространства имен служебной шины с помощью портала Azure

Пространство имен — это общий контейнер для всех компонентов обмена сообщениями. В одном пространстве имен могут содержаться несколько очередей и разделов. Часто пространства имен выполняют роль контейнеров приложений. В настоящее время создать пространство имен служебной шины можно двумя способами.

1.	Портал Azure (в этой статье)

2.	[Шаблоны диспетчера ресурсов][create-namespace-using-arm]

## Создание пространства имен на портале Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен служебной шины, предназначенное для обмена сообщениями.

## Дальнейшие действия

Ознакомьтесь с примерами [в нашем репозитории GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->
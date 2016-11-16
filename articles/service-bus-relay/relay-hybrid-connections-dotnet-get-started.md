---
title: "Приступая к работе с гибридными подключениями к ретранслятору | Документация Майкрософт"
description: "Как написать консольное приложение C# для гибридных подключений"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>Приступая к работе с гибридными подключениями к ретранслятору
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Что будет выполнено
Так как для гибридных подключений требуется компонент клиента и сервера, в этом руководстве мы создадим два консольных приложения. Для этого выполните следующие действия:

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение с помощью портала Azure.
3. Создайте серверное консольное приложение для получения сообщений.
4. Создайте клиентское консольное приложение для отправки сообщений.

## <a name="prerequisites"></a>Предварительные требования
1. [Visual Studio 2013 или Visual Studio 2015](http://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2015.
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure
Если пространство имен ретранслятора уже создано, перейдите к разделу [Создание гибридного подключения с помощью портала Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2) Создание гибридного подключения с помощью портала Azure
Если гибридное подключение уже создано, перейдите к разделу [Создание серверного приложения](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Создание серверного приложения (прослушивателя)
Для прослушивания и получения сообщений, отправленных ретранслятором, мы создадим консольное приложение C# с помощью Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Создание клиентского приложения (отправителя)
Для отправки сообщений в ретранслятор мы создадим консольное приложение C# с помощью Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Запуск приложений
1. Запустите серверное приложение.
2. Запустите клиентское приложение и введите любой текст.
3. Убедитесь, что серверное консольное приложение выводит текст, введенный в клиентском приложении.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Поздравляем, вы создали приложение для гибридных подключений.

## <a name="next-steps"></a>Дальнейшие действия:
* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Создание пространства имен](relay-create-namespace-portal.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->



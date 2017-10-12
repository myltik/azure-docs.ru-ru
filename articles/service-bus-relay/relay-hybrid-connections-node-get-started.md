---
title: "Приступая к работе с гибридными подключениями к ретранслятору Azure в Node | Документация Майкрософт"
description: "Написание консольного приложения Node.js для гибридных подключений ретранслятора Azure."
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Приступая к работе с гибридными подключениями к ретранслятору

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

В этом руководстве содержатся обзорные сведения о [гибридных подключениях ретранслятора Azure](relay-what-is-it.md#hybrid-connections) и показано, как с помощью Node.js создать клиентское приложение, которое отправляет сообщения соответствующему приложению прослушивателя. 

## <a name="what-will-be-accomplished"></a>Что будет выполнено

Так как для гибридных подключений требуется компонент клиента и сервера, в этом руководстве мы создадим два консольных приложения. Для этого выполните следующие действия:

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение с помощью портала Azure.
3. Создайте серверное консольное приложение для получения сообщений.
4. Создайте клиентское консольное приложение для отправки сообщений.

## <a name="prerequisites"></a>Предварительные требования

1. [Node.js](https://nodejs.org/en/).
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure

Если пространство имен ретранслятора уже создано, перейдите к разделу [Создание гибридного подключения с помощью портала Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Создание гибридного подключения с помощью портала Azure

Если гибридное подключение уже создано, перейдите к разделу [Создание серверного приложения](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Создание серверного приложения (прослушивателя)

Для прослушивания и получения сообщений, отправленных ретранслятором, мы создадим консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Создание клиентского приложения (отправителя)

Для отправки сообщений в ретранслятор мы создадим консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Запуск приложений

1. Запустите серверное приложение. Для этого в командной строке Node.js введите `node listener.js`.
2. Запустите клиентское приложение. Для этого в командной строке Node.js введите `node sender.js`, а затем любой текст.
3. Убедитесь, что серверное консольное приложение выводит текст, введенный в клиентском приложении.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Поздравляем, вы создали приложение для гибридных подключений с помощью Node.js!

## <a name="next-steps"></a>Дальнейшие действия:

* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Создание пространства имен](relay-create-namespace-portal.md)
* [Приступая к работе с .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)


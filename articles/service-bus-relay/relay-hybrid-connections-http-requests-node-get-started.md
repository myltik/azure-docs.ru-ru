---
title: Начало работы с HTTP-запросами гибридных подключений Azure Relay в Node | Документация Майкрософт
description: Создание консольного приложения Node.js для HTTP-запросов гибридных подключений Azure Relay в Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 2bc923650425c76562161dd6f44f3a5722b5cefe
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896631"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Начало работы с HTTP-запросами гибридных подключений Azure Relay в Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

В этом руководстве содержатся обзорные сведения об HTTP-запросах [гибридных подключениях Azure Relay](relay-what-is-it.md#hybrid-connections) и показано, как с помощью Node.js создать клиентское приложение, которое отправляет сообщения соответствующему приложению прослушивателя.

## <a name="what-will-be-accomplished"></a>Что будет выполнено

Так как для гибридных подключений требуется компонент клиента и сервера, в этом руководстве нужно создать два консольных приложения. Для этого выполните следующие действия:

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение с помощью портала Azure.
3. Создайте серверное консольное приложение для получения сообщений.
4. Создайте клиентское консольное приложение для отправки сообщений.

## <a name="prerequisites"></a>предварительным требованиям

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

Для ожидания передачи данных и получения сообщений, отправленных ретранслятором, создайте консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Создание клиентского приложения (отправителя)

Для отправки сообщений в ретранслятор используйте любой клиент HTTP или создайте консольное приложение Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Запуск приложений

1. Запустите серверное приложение. Для этого в командной строке Node.js введите `node listener.js`.
2. Запустите клиентское приложение. Для этого в командной строке Node.js введите `node sender.js`, а затем любой текст.
3. Убедитесь, что серверное консольное приложение выводит текст, введенный в клиентском приложении.

Поздравляем, вы создали приложение для гибридных подключений с помощью Node.js!

## <a name="next-steps"></a>Дополнительная информация

* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Создание пространства имен](relay-create-namespace-portal.md)
* [Приступая к работе с .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)


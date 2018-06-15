---
title: Как использовать соединитель SFTP в приложениях логики | Документация Майкрософт
description: Создание приложений логики с помощью службы приложений Azure. Подключитесь к API SFTP, чтобы отправлять и получать файлы. Вы можете выполнять различные операции, например создавать, обновлять, получать или удалять файлы.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 28ea02082903f71f619a52672ba41ce65557b0c7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296008"
---
# <a name="get-started-with-the-sftp-connector"></a>Начало работы с соединителем SFTP
Используйте соединитель SFTP, чтобы получить доступ к учетной записи SFTP для отправки и получения файлов. Вы можете выполнять различные операции, например создавать, обновлять, получать или удалять файлы.  

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-sftp"></a>Подключение к SFTP
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

### <a name="create-a-connection-to-sftp"></a>Создание подключения к SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Использование триггера SFTP
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

В этом примере триггер **SFTP - When a file is added or modified** (SFTP — при добавлении или изменении файла) используется для запуска рабочего процесса приложения логики при добавлении или изменении файла на SFTP-сервере. Также вы добавите условие, которое проверяет содержимое нового или измененного файла, и принимает решение извлечь файл, если содержимое файла указывает, что он должен быть извлечен перед использованием содержимого. Наконец, вы добавите действие, которое извлекает содержимое файла и помещает извлеченное содержимое в папку на SFTP-сервере. 

В контексте предприятия можно использовать этот триггер, чтобы отслеживать в папке SFTP новые файлы, представляющие заказы клиентов.  Также вы можете использовать действие соединителя SFTP **Получить содержимое файла**, чтобы получить содержимое заказа для дальнейшей обработки и хранения в базе данных заказов.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Добавить условие
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Использование действия SFTP
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
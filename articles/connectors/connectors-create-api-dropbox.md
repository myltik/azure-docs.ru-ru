---
title: "Соединитель Dropbox в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к Dropbox, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов в Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-dropbox-connector"></a>Начало работы с соединителем Dropbox
Подключитесь к Dropbox, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов в Dropbox.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Подключение к Dropbox
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите соединение между приложением логики и другой службой. Например, чтобы подключиться к Dropbox, сначала необходимо создать *подключение* к Dropbox. Чтобы создать подключение, необходимо ввести учетные данные, которые обычно используются для доступа к определенной службе. То есть для создания подключения к Dropbox вам понадобятся учетные данные учетной записи Dropbox. Дополнительные сведения о подключениях см. [здесь]().

### <a name="create-a-connection-to-dropbox"></a>Создание подключения к Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Использование триггера Dropbox
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

В этом примере мы будем использовать триггер **When a file is created** (При создании файла). Когда этот триггер активируется, мы вызовем действие Dropbox **Get file content using path** (Получение содержимого файла с помощью пути). 

1. Введите запрос *dropbox* в поле поиска в конструкторе Logic Apps, а затем выберите триггер **Dropbox - When a file is created** (Dropbox — при создании файла).      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Выберите папку для отслеживания создания файла. Щелкните многоточие "…" (на снимке экрана выделено красной рамкой) и перейдите к папке, которую вы хотите выбрать в качестве входных данных триггера.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Использование действия Dropbox
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Теперь, когда триггер добавлен, выполните следующие действия, чтобы добавить действие, которое получит новое содержимое файла.

1. Нажмите кнопку **+ Новый шаг**, чтобы добавить действие, которое будет выполняться после создания нового файла.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Выберите **Добавить действие**. Откроется поле поиска. В этом поле вы можете выполнить поиск действия, которое нужно применить.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Введите запрос *dropbox*, чтобы найти действия, связанные с Dropbox.  
4. Выберите **Dropbox - Get file content using path** (Dropbox — получение содержимого файла с помощью пути) в качестве действия, которое будет выполняться после создания файла в выбранной папке Dropbox. Откроется блок управления действием. Вам будет предложено авторизовать приложение логики для доступа к учетной записи Dropbox, если вы еще не сделали это.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Щелкните многоточие "…" (находится в правой части элемента управления **Путь к файлу**) и перейдите к файлу, который вы хотите использовать. Или используйте маркер **file path**, чтобы ускорить создание приложения логики.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Сохраните изменения и создайте новый файл в Dropbox, чтобы активировать рабочий процесс.  

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/dropbox/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
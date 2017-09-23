---
title: "Соединитель SMTP в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключение к SMTP для отправки электронной почты."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-smtp-connector"></a>Начало работы с соединителем SMTP
Подключение к SMTP для отправки электронной почты.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Подключение к SMTP
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой. Например, чтобы подключиться к SMTP, сначала необходимо создать *подключение* по протоколу SMTP. Чтобы создать подключение, введите учетные данные, которые используются для доступа к определенной службе. Таким образом, чтобы создать подключение по протоколу SMTP, следует ввести имя подключения, адрес SMTP-сервера и учетные данные для входа.  

### <a name="create-a-connection-to-smtp"></a>Создание подключения к SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Использование триггера SMTP
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Так как SMTP не предоставляет триггеры, мы используем триггер **Salesforce - When an object is created** (Salesforce — при создании объекта). Этот триггер активируется при создании объекта в Salesforce. В нашем примере мы настроим триггер таким образом, чтобы при каждом создании интереса в Salesforce выполнялось действие *Send Email* через соединитель SMTP. При этом будет отправляться уведомление об этом интересе.

1. В конструкторе приложений логики в поле поиска введите запрос *salesforce* , а затем выберите триггер **Salesforce — при создании объекта** .  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Отобразится элемент управления **При создании объекта** .
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Укажите **тип объекта** , а затем выберите *Интерес* из списка объектов. На этом шаге вы указываете, что создаете триггер, который будет уведомлять приложение логики о создании интереса в Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Ваш триггер создан.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Использование действия SMTP
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Теперь, когда триггер добавлен, выполните следующие действия, чтобы добавить действие SMTP, которое будет выполняться при создании интереса в Salesforce.

1. Нажмите кнопку **+ Новый шаг**, чтобы добавить действие, которое будет выполняться после создания интереса.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Выберите **Добавить действие**. Откроется поле поиска. В этом поле вы можете выполнить поиск действия, которое нужно применить.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Введите *smtp* в поле поиска, чтобы найти действия, связанные с SMTP.  
4. Выберите **SMTP - Send Email** (SMTP — отправка электронного сообщения) в качестве действия, которое следует выполнять при создании интереса. Откроется блок управления действием. Установите подключение SMTP в блоке конструктора, если вы еще этого не сделали.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. В блоке **SMTP - Send Email** (SMTP — отправка электронного сообщения) введите необходимые сведения об электронной почте.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Сохраните данные, чтобы активировать рабочий процесс.  

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).

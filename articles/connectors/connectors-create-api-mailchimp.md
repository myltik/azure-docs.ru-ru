---
title: "Соединитель MailChimp в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 50919fd903c24e4748a6efe29af2c188b1c3c195
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-mailchimp-connector"></a>Начало работы с соединителем MailChimp
MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-mailchimp"></a>Создание подключения к MailChimp
Для создания приложений логики с помощью MailChimp необходимо создать **подключение**, а затем указать данные для следующих свойств.

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Укажите учетные данные MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/mailchimp/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
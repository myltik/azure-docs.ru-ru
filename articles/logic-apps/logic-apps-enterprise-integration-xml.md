---
title: Работа с сообщениями XML в рабочих процессах с помощью Azure Logic Apps | Документация Майкрософт
description: Обработка, проверка, преобразование и обогащение сообщений XML в приложениях логики и сценариях B2B с помощью Пакета интеграции Enterprise.
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 85bdaff5cb88bbadaed778458b66c0cce7bce9de
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298980"
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Проверка и преобразование XML, кодирование и декодирование неструктурированных файлов и обогащение сообщений в приложениях логики

С помощью приложений логики можно обрабатывать отправляемые и получаемые сообщения XML. Эта функция в состав Пакета интеграции Enterprise. Для пользователей, работавших с BizTalk Server, Пакет интеграции Enterprise обеспечит аналогичные возможности по преобразованию и проверке сообщений, работе с неструктурированными файлами и даже использованию XPath для обогащения сообщений или извлечения из них свойств. 

Пользователи, не знакомые с этой областью, с помощью данных функций смогут расширить возможности обработки сообщений в рабочем процессе. Например, если в сценарии B2B вы работаете с конкретными схемами XML, то можете использовать Пакет интеграции Enterprise для улучшения обработки этих сообщений в организации. 

Пакет интеграции Enterprise включает в себя следующее: 

* [Проверка XML](logic-apps-enterprise-integration-xml-validation.md "Узнайте о проверке сообщений XML") позволяет проверить входящее или исходящее сообщение XML на соответствие указанной схеме.
* [Преобразование XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Узнайте о преобразованиях сообщений XML и сопоставлениях") позволяет преобразовать или настроить сообщение XML в соответствии с вашими требованиями или требованиями партнера.
* [Кодирование и декодирование неструктурированных файлов](logic-apps-enterprise-integration-flatfile.md "См. дополнительные сведения о кодировании и декодировании неструктурированных файлов") позволяет кодировать и декодировать неструктурированный файл. Например, SAP принимает и отправляет IDOC-файлы в виде неструктурированных файлов. Многие платформы интеграции создают сообщения XML, включая Logic Apps. Поэтому можно создать приложение логики, которое использует кодировщик неструктурированных файлов для преобразования XML-файлов в неструктурированные файлы. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) позволяет дополнить сообщение и извлечь из него определенные свойства. Эти извлеченные свойства можно затем использовать для перенаправления сообщения в целевую или промежуточную конечную точку.

## <a name="try-it-out"></a>Попробуйте сейчас
[Разверните полностью работоспособное приложение логики](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (пример с GitHub) с помощью функций XML в Azure Logic Apps.

## <a name="learn-more"></a>Подробнее
[Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")

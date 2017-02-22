---
title: "Обработка XML в Azure Logic Apps | Документация Майкрософт"
description: "Обработка и проверка XML в Azure Logic Apps в рамках сценариев B2B с помощью пакета интеграции Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 683fe55c16862c3f7df8741d491edd9ba238351d
ms.openlocfilehash: 015e387c1b252165017c4a68b26d07ef89d54ef6


---
# <a name="xml-processing"></a>Обработка XML
XML-документы, используемые для обмена с партнерами в сценарии B2B можно легко проверять и обрабатывать с помощью пакета интеграции Enterprise и Azure Logic Apps. Ниже описано, как можно обрабатывать эти сообщения.

* [Проверка XML](logic-apps-enterprise-integration-xml-validation.md "См. дополнительные сведения о проверке сообщений XML") позволяет проверять сообщения, поступающие из исходной конечной точки, на соответствие указанной схеме.
* [Преобразование XML](../logic-apps/logic-apps-enterprise-integration-transform.md "См. дополнительные сведения о преобразованиях сообщений XML и картах") позволяет преобразовывать сообщения XML в соответствии с требованиями целевой конечной точки.
* [Кодирование и декодирование неструктурированных файлов](logic-apps-enterprise-integration-flatfile.md "См. дополнительные сведения о кодировании и декодировании неструктурированных файлов") позволяет кодировать и декодировать неструктурированный файл. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) позволяет дополнить сообщение и извлечь из него определенные свойства. Эти извлеченные свойства можно затем использовать для перенаправления сообщения в целевую или промежуточную конечную точку.

## <a name="try-out-xml-processing"></a>Знакомство с обработкой XML
[Разверните полностью работоспособное приложение логики](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) с помощью функций XML в Azure Logic Apps.

## <a name="learn-more"></a>Подробнее
[Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")



<!--HONumber=Feb17_HO1-->



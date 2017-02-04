---
title: "Обзор интеграции Enterprise | Документация Майкрософт"
description: "Используйте функции интеграции Enterprise, чтобы реализовать сценарии бизнес-процессов и интеграции с помощью приложений логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: d0508a40cd4ccea1d7476b366b8e1d17a0b78847


---
# <a name="overview-of-the-enterprise-integration-pack"></a>Обзор пакета интеграции Enterprise
## <a name="what-is-the-enterprise-integration-pack"></a>Что такое пакет интеграции Enterprise?
Пакет интеграции Enterprise — это облачное решение корпорации Майкрософт, с помощью которого можно легко обеспечить обмен данными "бизнес-бизнес". Пакет использует стандартные протоколы, в том числе [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) и [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), для обмена сообщениями между деловыми партнерами. Сообщения можно дополнительно защитить с помощью шифрования и цифровых подписей. 

Данный пакет позволяет организациям, которые используют разные протоколы и форматы, обмениваться электронными сообщениями, преобразовывая разные форматы в формат, который системы обеих организаций могут интерпретировать, чтобы дать возможность предпринять соответствующие действия. 

Если вы уже работали с BizTalk Server или службами BizTalk Microsoft Azure, то вам будет легко использовать функции интеграции Enterprise, так как большая часть используемых понятий аналогична. Одно из основных отличий — в интеграции Enterprise используются учетные записи интеграции, которые упрощают хранение артефактов, используемых при обмене данными "бизнес-бизнес", и управление ими. 

С точки зрения архитектуры в основе пакета интеграции Enterprise лежат **учетные записи интеграции** , в которых хранятся все артефакты, доступные для разработки, развертывания и обслуживания ваших приложений "бизнес-бизнес". По сути, учетная запись интеграции — это облачный контейнер для хранения таких артефактов, как схемы, партнеры, сертификаты, карты и соглашения. Эти артефакты можно использовать в приложениях логики для создания рабочих процессов "бизнес-бизнес". Прежде чем использовать артефакты в приложении логики, необходимо связать с ними свою учетную запись интеграции. После связывания приложение логики будет иметь доступ к артефактам в учетной записи интеграции.  

## <a name="why-should-you-use-enterprise-integration"></a>Для чего следует использовать интеграцию Enterprise?
* Благодаря интеграции Enterprise можно хранить все артефакты в одном месте — в своей учетной записи интеграции. 
* Вы можете использовать ядро приложений логики и все его соединители, чтобы создать рабочие процессы "бизнес-бизнес" и реализовать интеграцию с приложениями SaaS сторонних поставщиков, локальными приложениями или пользовательскими приложениями.
* Можно также использовать функции Azure.

## <a name="how-to-get-started-with-enterprise-integration"></a>Как приступить к работе с интеграцией Enterprise?
Создавать приложения "бизнес-бизнес" и управлять ими с помощью пакета интеграции Enterprise можно в конструкторе приложений логики на **портале Azure**.  

Для управления приложениями логики можно также использовать [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Разделы о PowerShell для приложений логики") . 

Ниже описаны действия, которые необходимо выполнить перед созданием приложений на портале Azure. ![Графическое представление действий](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Каковы наиболее распространенные сценарии применения?
Интеграция Enterprise поддерживает следующие отраслевые стандарты:   

* EDI — электронный обмен данными;  
* EAI — интеграция приложений.  

## <a name="heres-what-you-need-to-get-started"></a>Вот что необходимо для немедленного начала работы.
* Подписка Azure с учетной записью интеграции.
* Visual Studio 2015 для создания карт и схем.
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0.](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Попробовать
[Попробуйте](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) развернуть полностью работоспособный пример приложения логики для обмена данными AS2 с помощью функций "бизнес — бизнес" приложений логики.

## <a name="learn-more-about"></a>См. также:
* [Соглашения](../logic-apps/logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях интеграции Enterprise")
* [Сценарии "бизнес — бизнес"](../logic-apps/logic-apps-enterprise-integration-b2b.md "Узнайте, как создавать приложения логики с возможностями "бизнес — бизнес"")  
* [Сертификаты](logic-apps-enterprise-integration-certificates.md "Узнайте о сертификатах интеграции Enterprise")
* [Кодирование и декодирование неструктурированных файлов](logic-apps-enterprise-integration-flatfile.md "Узнайте, как кодировать и декодировать содержимое неструктурированных файлов")  
* [Учетные записи интеграции](../logic-apps/logic-apps-enterprise-integration-accounts.md "Узнайте больше об учетных записях интеграции")
* [Карты](../logic-apps/logic-apps-enterprise-integration-maps.md "Узнайте о картах интеграции Enterprise")
* [Партнеры](logic-apps-enterprise-integration-partners.md "Узнайте о партнерах интеграции Enterprise")
* [Схемы](logic-apps-enterprise-integration-schemas.md "Узнайте о схемах интеграции Enterprise")
* [Проверка сообщений XML](logic-apps-enterprise-integration-xml.md "Узнайте, как проверять сообщения XML с помощью приложений логики")
* [Преобразование XML-файла](logic-apps-enterprise-integration-transform.md "Узнайте о картах интеграции Enterprise")
* [Корпоративные соединители интеграции](../connectors/apis-list.md "Узнайте о соединителях пакета интеграции Enterprise.")
* [Метаданные учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-metadata.md "Узнайте о метаданных учетной записи интеграции")
* [Мониторинг сообщений "бизнес — бизнес"](logic-apps-monitor-b2b-message.md "Узнайте больше о мониторинге сообщений "бизнес — бизнес"")
* [Отслеживание сообщений "бизнес — бизнес" на портале OMS](logic-apps-track-b2b-messages-omsportal.md "Узнайте больше об отслеживании сообщений "бизнес — бизнес" на портале OMS")




<!--HONumber=Jan17_HO3-->



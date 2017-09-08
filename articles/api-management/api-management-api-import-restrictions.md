---
title: "Ограничения и известные проблемы при импорте API в службу управления API Azure | Документация Майкрософт"
description: "Сведения об известных проблемах и ограничениях при импорте в службу управления API Azure с помощью форматов Open API, WSDL и WADL."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: ac799d66b5038c207413086b0fa71239ff2a332f
ms.contentlocale: ru-ru
ms.lasthandoff: 03/15/2017

---
# <a name="api-import-restrictions-and-known-issues"></a>Ограничения и известные проблемы при импорте API
## <a name="about-this-list"></a>Об этом списке
Мы делаем все возможное, чтобы процесс импорта API в службу управления API Azure стал максимально эффективным и проходил без каких-либо проблем. Но на данный момент существуют некоторые ограничения и могут возникать ошибки, которые необходимо исправить, чтобы импорт прошел успешно. В этой статье собраны необходимые сведения. Они сгруппированы по форматам импорта API.

## <a name="open-api"> </a>Open API/Swagger
Как правило, если возникают ошибки при импорте документа Open API, то необходимо проверить, утвержден ли этот процесс. Это можно сделать с помощью конструктора на новом портале Azure ("Конструктор" > "Интерфейс" > "Open API Specification Editor") или инструмента стороннего разработчика, такого как <a href="http://www.swagger.io">редактор Swagger</a>.

* **Host Name** (Имя узла): необходимо указать атрибут имени узла.
* **Base Path** (Базовый путь): необходимо указать атрибут базового пути.
* **Schemes** (Схемы): необходимо указать массив схем. 

## <a name="wsdl"> </a>WSDL
WSDL-файлы используются для создания сквозных интерфейсов API SOAP, а также служат в качестве серверной части интерфейса API между SOAP и REST.

* **WSDL:import**: в настоящее время интерфейсы API, использующие этот атрибут, не поддерживаются. Клиентам следует объединить импортированные элементы в один документ.
* **Сообщения из нескольких частей**: в настоящее время не поддерживаются.
* **WCF wsHttpBinding**: в службах SOAP, созданных с помощью технологии Windows Communication Foundation, следует использовать значение basicHttpBinding. Значение wsHttpBinding не поддерживается.
* **MTOM**: службы, использующие MTOM, <em>могут</em> работать. Но официальная поддержка в данный момент не предоставляется.
* **Рекурсия** типов, которые определены рекурсивно (например, ссылаются на включающий их массив), не поддерживается.

## <a name="wadl"> </a>WADL
У нас нет сведений о проблемах импорта с помощью формата WADL.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md


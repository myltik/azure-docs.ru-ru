---
title: Развертывание служб управления API Azure в нескольких регионах Azure | Документация Майкрософт
description: Дополнительные сведения о развертывании экземпляра службы управления Azure API в различных регионах Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ff0101bde54f99f99461d0f042af520b1642d0df
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31586812"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Развертывание экземпляра службы управления Azure API в различных регионах Azure
Служба управления API поддерживает развертывание в нескольких регионах, что позволяет издателям API распространять единую службу управления API в любых требуемых регионах Azure. Это сокращает задержки, связанные с географической удаленностью потребителей API, а также повышает доступность службы, когда какой-либо из регионов переходит в автономный режим. 

При создании службы управления API она содержит только одну [единицу][unit] и располагается в одном регионе Azure, который считается основным. Однако через портал Azure можно легко добавить дополнительные регионы. В каждом регионе развертывается сервер шлюза управления API, и весь трафик вызовов направляется на ближайший из таких шлюзов. Если регион переходит в автономный режим, трафик автоматически перенаправляется к другому ближайшему шлюзу. 

> [!IMPORTANT]
> Развертывание в нескольких регионах доступно только для уровня **[Премиум][Premium]**.
> 
> 

## <a name="add-region"> </a>Создание экземпляра службы управления API в новом регионе
> [!NOTE]
> Если экземпляр службы управления API еще не создан, выполните инструкции в статье [Создание экземпляра службы управления API Azure][Create an API Management service instance].
> 
> 

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API. 

![Вкладка "Масштаб"][api-management-scale-service]

Чтобы развернуть службу в новом регионе, щелкните **+ Add region** (+ Добавить регион) на панели инструментов.

![Добавление региона][api-management-add-region]

Выберите расположение из раскрывающегося списка и задайте число единиц с помощью ползунка.

![Указание единиц][api-management-select-location-units]

Щелкните **Добавить**, чтобы разместить выбранные ресурсы в таблице "Расположения". 

Повторите этот процесс, пока не будут настроены все расположения, затем щелкните **Сохранить** на панели инструментов, чтобы начать процесс развертывания.

## <a name="remove-region"> </a>Удаление экземпляра службы управления API из расположения

На портале Azure перейдите на страницу **Scale and pricing** (Масштаб и цены) для своего экземпляра службы управления API. 

![Вкладка "Масштаб"][api-management-scale-service]

Для расположения, которое нужно удалить, откройте контекстное меню с помощью кнопки **…** в правой части таблицы. Щелкните **Удалить**.

Подтвердите удаление и нажмите кнопку **Сохранить**, чтобы применить изменения.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/


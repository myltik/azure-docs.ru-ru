---
title: "Развертывание экземпляра службы управления Azure API в различных регионах Azure"
description: "Дополнительные сведения о развертывании экземпляра службы управления Azure API в различных регионах Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: d99e2d885d56e3752a3b1caf51e52c801acaab52
ms.openlocfilehash: 98201867fd8b1b5f074aa6135e04b04faf384224


---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Развертывание экземпляра службы управления Azure API в различных регионах Azure
Служба управления API поддерживает развертывание в нескольких регионах, что позволяет издателям API распространять единую службу управления API в любых требуемых регионах Azure. Это сокращает задержки, связанные с географической удаленностью потребителей API, а также повышает доступность службы, когда какой-либо из регионов переходит в автономный режим. 

При создании службы управления API она содержит только одну [единицу][единицу] и располагается в одном регионе Azure, который считается основным. Однако через портал Azure можно легко добавить дополнительные регионы. В каждом регионе развертывается сервер шлюза управления API, и весь трафик вызовов направляется на ближайший из таких шлюзов. Если регион переходит в автономный режим, трафик автоматически перенаправляется к другому ближайшему шлюзу. 

> [!IMPORTANT]
> Развертывание в нескольких регионах доступно только для уровня **[Премиальный][Премиальный]**.
> 
> 

## <a name="add-region"> </a>Создание экземпляра службы управления API в новом регионе
> [!NOTE]
> Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Создание экземпляра управления API] в статье [Начало работы со службой управления Azure API][Приступая к работе со службой управления API].
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

![Удаление региона][api-management-remove-region]

Подтвердите удаление и нажмите кнопку **Сохранить**, чтобы применить изменения.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Создание экземпляра управления API]: api-management-get-started.md#create-service-instance
[Приступая к работе со службой управления API]: api-management-get-started.md

[Создание экземпляра службы управления API в новом регионе]: #add-region
[Удаление экземпляра службы управления API из региона]: #remove-region

[единицу]: http://azure.microsoft.com/pricing/details/api-management/
[Премиальный]: http://azure.microsoft.com/pricing/details/api-management/




<!--HONumber=Nov16_HO3-->



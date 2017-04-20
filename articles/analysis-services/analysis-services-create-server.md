---
title: "Создание сервера Analysis Services в Azure | Документация Майкрософт"
description: "Узнайте, как создать экземпляр сервера служб Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/17/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 466595773663d43ad8e25fa9ec0ec0163a3f5962
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-analysis-services-server"></a>Создание сервера Analysis Services
В этой статье приведено пошаговое руководство по созданию ресурса сервера служб Analysis Services в подписке Azure.

## <a name="before-you-begin"></a>Перед началом работы
Для начала работы необходимы перечисленные ниже компоненты и данные.

* **Подписка Azure**: откройте ссылку на [бесплатную пробную версию Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), чтобы создать учетную запись.
* **Azure Active Directory**: ваша подписка должна быть связана с клиентом Azure Active Directory. И необходимо войти в Azure с учетной записью в этом Azure Active Directory. Учетные записи Майкрософт не поддерживаются. Дополнительные сведения см. в разделе [Аутентификация пользователей](analysis-services-overview.md#secure).
* **Группа ресурсов**: используйте уже имеющуюся группу ресурсов или [создайте новую](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> При создании сервера Analysis Services вам может быть выставлен счет за новую службу. Дополнительные сведения см. в разделе [цен на службы Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="create-an-analysis-services-server"></a>Создание сервера Analysis Services
1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **+ Создать** > **Аналитика** > **Службы Analysis Services**.
3. В колонке **Службы Analysis Services** заполните обязательные поля и нажмите кнопку **Создать**.
   
    ![Создание сервера](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Имя сервера**: введите уникальное имя, используемое для обращения к серверу.
   * **Подписка**: выберите подписку, в рамках которой выставляются счета за сервер.
   * **Группа ресурсов**: это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительные сведения см. в описании [групп ресурсов](../azure-resource-manager/resource-group-overview.md).
   * **Расположение**: в этом расположении центра обработки данных Azure размещается сервер. Выберите расположение, ближайшее к крупнейшей имеющейся базе пользователей.
   * **Ценовая категория**: выберите ценовую категорию. Поддерживаются табличные модели размером до 100 ГБ. Дополнительные сведения см. в разделе [цен на службы Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Щелкните **Создать**.

Создание обычно занимает меньше минуты, чаще всего несколько секунд. Если был выбран параметр **Add to Portal** (Добавить на портал), перейдите на портал, чтобы просмотреть новый сервер. Или перейдите по адресу **Больше служб** > **Службы Analysis Services**, чтобы проверить, готов ли ваш сервер.

 ![Панель мониторинга](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>Автоматизация создания сервера
Можно автоматизировать подготовку серверов "на лету" с помощью файлов шаблонов Azure Resource Manager. Чтобы узнать больше, просмотрите это полезное видео.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>Дальнейшие действия
После создания сервера вы можете [развернуть на нем модель](analysis-services-deploy.md) с помощью SSDT или SSMS.

Если модель, развертываемая на сервере, подключается к локальным источникам данных, необходимо будет установить [локальный шлюз данных](analysis-services-gateway.md) на одном из компьютеров в вашей сети.



---
title: Создание сервера Analysis Services в Azure | Документация Майкрософт
description: Узнайте, как создать экземпляр сервера служб Analysis Services в Azure.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 9a80d7e6e9f24ccc730fe747fa0a71b91f22b7e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Создание сервера Azure Analysis Services на портале Azure
В этой статье приведено пошаговое руководство по созданию ресурса сервера служб Analysis Services в подписке Azure.

## <a name="before-you-begin"></a>Перед началом работы
Для работы с этим кратким руководством вам понадобится:

* **Подписка Azure**: откройте ссылку на [бесплатную пробную версию Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), чтобы создать учетную запись.
* **Azure Active Directory**: ваша подписка должна быть связана с клиентом Azure Active Directory. И необходимо войти в Azure с учетной записью в этом Azure Active Directory. Учетные записи Майкрософт не поддерживаются. Дополнительные сведения см. в руководстве по [аутентификации и настройке пользовательских разрешений](analysis-services-manage-users.md).
* **Группа ресурсов**: используйте уже имеющуюся группу ресурсов или [создайте новую](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> При создании сервера вам могут выставляться счета за новую службу. Дополнительные сведения см. в разделе [цен на службы Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>Создание сервера на портале Azure
1. Войдите на [портале Azure](https://portal.azure.com).  
2. Щелкните **+ Создать** > **Данные и аналитика** > **Службы Analysis Services**.
3. В колонке **Службы Analysis Services** заполните обязательные поля и нажмите кнопку **Создать**.
   
    ![Создание сервера](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Имя сервера**: введите уникальное имя, используемое для обращения к серверу.
   * **Подписка**: выберите подписку, в рамках которой выставляются счета за сервер.
   * **Группа ресурсов**: это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительные сведения см. в описании [групп ресурсов](../azure-resource-manager/resource-group-overview.md).
   * **Расположение**: в этом расположении центра обработки данных Azure размещается сервер. Выберите расположение, ближайшее к крупнейшей имеющейся базе пользователей.
   * **Ценовая категория**: выберите ценовую категорию. Поддерживаются табличные модели размером до 400 ГБ. Дополнительные сведения см. в разделе [цен на службы Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Нажмите кнопку **Создать**.

Создание обычно занимает меньше минуты, чаще всего несколько секунд. Если был выбран параметр **Add to Portal** (Добавить на портал), перейдите на портал, чтобы просмотреть новый сервер. Или перейдите по меню **Все службы** > **Analysis Services**, чтобы проверить, готов ли ваш сервер.

 ![панель мониторинга](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Дополнительная информация
После создания сервера вы можете [развернуть на нем модель](analysis-services-deploy.md) с помощью SSDT или SSMS.

Если модель, развертываемая на сервере, подключается к локальным источникам данных, необходимо будет установить [локальный шлюз данных](analysis-services-gateway.md) на одном из компьютеров в вашей сети.


---
title: Создание сервера Analysis Services в Azure | Документация Майкрософт
description: Узнайте, как создать экземпляр сервера служб Analysis Services в Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
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


---
title: "Как создавать и использовать группы для управления учетными записями разработчика в службе управления Azure API"
description: "Сведения об управлении учетными записями разработчика с помощью групп в службе управления API Azure"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 33660b45-442f-44be-9a4a-1899d7f699b0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 2187c94f5deab28159594216545d7b57ef02cb70


---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Как создавать и использовать группы для управления учетными записями разработчика в службе управления Azure API
В службе управления API группы используются для управления видимостью продуктов для разработчиков. Продукты сначала делаются видимыми для групп, а затем разработчики в таких группах могут просматривать и подписываться на продукты, которые связаны с группами. 

Служба управления API имеет несколько неизменяемых системных групп.

* **Администраторы** — члены этой группы являются администраторами подписок Azure. Администраторы управляют экземплярами службы управления API, созданием API, операциями и продуктами, которые используются разработчиками.
* **Разработчики** — в эту группу входят пользователи портала разработчиков, прошедшие проверку подлинности. Разработчики — это клиенты, которые создают приложения с использованием API. Разработчикам предоставляется доступ к порталу разработчика, и они могут создавать приложения, вызывающие операции этого API.
* **Гости** — пользователи портала разработчиков, не прошедшие проверку подлинности; например, в эту группу попадают возможные клиенты, посетившие портал разработчиков экземпляра управления API. Им может быть предоставлен доступ только для чтения, позволяющий просматривать API, но не вызывать их.

Помимо системных групп администраторы могут создавать пользовательские группы или [использовать внешние группы в связанных с ними клиентах Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Чтобы обеспечить разработчикам видимость и предоставить доступ к продуктам API, пользовательские и внешние группы можно использовать вместе с системными группами. Например, можно создать одну пользовательскую группу для разработчиков, связанных с конкретной партнерской организацией, и предоставить им доступ к интерфейсам API в продукте, содержащем только соответствующие интерфейсы API. Пользователь может входить в несколько групп.

В этом руководстве показано, как администраторы экземпляра службы управления API могут добавлять новые группы и связывать их с продуктами и разработчиками.

> [!NOTE]
> Помимо создания групп и управления ими на портале издателя, это можно делать с помощью сущности [Group](https://msdn.microsoft.com/library/azure/dn776329.aspx) REST API интерфейса API управления.
> 
> 

## <a name="create-group"> </a>Создание группы
Чтобы создать группу, щелкните на портале Azure **Publisher portal** (Портал издателя) для службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

> Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Get started with Azure API Management].
> 
> 

Щелкните **Группы** в расположенном слева меню **Управление API**, а затем — **Добавить группу**.

![Добавление новой группы][api-management-add-group]

Введите уникальное имя для группы и необязательное описание, а затем щелкните **Сохранить**.

![Добавление новой группы][api-management-add-group-window]

Новая группа отображается на вкладке групп. Чтобы изменить **имя** или **описание** группы, щелкните имя группы в списке. Чтобы удалить группу, щелкните **Удалить**.

![Группа добавлена][api-management-new-group]

Теперь, когда группа создана, ее можно связать с продуктами и разработчиками.

## <a name="associate-group-product"> </a>Связывание группы с продуктом
Чтобы связать группу с продуктом, щелкните **Продукты** в расположенном слева меню **Управление API**, а затем щелкните имя нужного продукта.

![Настройка видимости][api-management-add-group-to-product]

Для добавления и удаления групп, а также просмотра текущих групп для продукта перейдите на вкладку **Видимость** . Для добавления или удаления групп установите или снимите флажки для требуемых групп и щелкните **Сохранить**.

![Настройка видимости][api-management-add-group-to-product-visibility]

> [!NOTE]
> Дополнительную информацию о добавлении групп Azure Active Directory см. в статье [Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure](api-management-howto-aad.md).
> 
> Для настройки групп на вкладке **Видимость** для продукта щелкните **Управление группами**.
> 
> 

После установления связи между продуктом и группой разработчики в данной группе могут просматривать продукт и подписаться на него.

## <a name="associate-group-developer"> </a>Связывание групп с разработчиками
Чтобы связать группы с разработчиками, щелкните **Пользователи** в расположенном слева меню **Управление API**, а затем установите флажок рядом с разработчиками, которых необходимо связать с группой.

![Добавление разработчика в группу][api-management-add-group-to-developer]

После выбора требуемых разработчиков щелкните требуемую группу в раскрывающемся списке **Добавить в группу** . Для удаления разработчиков из групп используйте раскрывающийся список **Удалить из группы** . 

![Разработчики][api-management-add-group-to-developer-saved]

После добавления связи между разработчиком и группой ее можно просмотреть на вкладке **Пользователи** .

## <a name="next-steps"> </a>Дальнейшие действия
* После добавления  в группу разработчик может просматривать связанные с данной группой продукты и подписываться на них. Дополнительные сведения см. в статье [Создание и публикация продукта в службе управления API Azure][How create and publish a product in Azure API Management].
* Помимо создания групп и управления ими на портале издателя, это можно делать с помощью сущности [Group](https://msdn.microsoft.com/library/azure/dn776329.aspx) REST API интерфейса API управления.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group



<!--HONumber=Dec16_HO3-->



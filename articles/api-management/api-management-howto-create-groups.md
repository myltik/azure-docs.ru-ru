<properties 
	pageTitle="Как создавать и использовать группы для управления учетными записями разработчика в Azure API Management" 
	description="Сведения об управлении учетными записями разработчика с помощью групп в службе управления API Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Как создавать и использовать группы для управления учетными записями разработчика в Azure API Management

В API Management группы используются для управления видимостью продуктов для разработчиков. Продукты сначала делаются видимыми для групп, а затем разработчики в таких группах могут просматривать и подписываться на продукты, которые связаны с группами.

Управление API имеет несколько неизменяемых системных групп.

-	**Администраторы** — члены этой группы являются администраторами подписок Azure. Администраторы управляют экземплярами службы управления API, созданием API, операциями и продуктами, которые используются разработчиками.
-	**Разработчики** — в эту группу входят пользователи портала разработчиков, прошедшие проверку подлинности. Разработчики — это клиенты, которые создают приложения с использованием API. Разработчикам предоставляется доступ к порталу разработчика, и они могут создавать приложения, вызывающие операции этого API.
-	**Гости** — пользователи портала разработчиков, не прошедшие проверку подлинности; например, в эту группу попадают возможные клиенты, посетившие портал разработчиков экземпляра управления API. Им может быть предоставлен доступ только на чтение, такой как способность просматривать API, но не вызывать их.

Помимо этих системных групп администраторы могут создавать пользовательские группы или [использовать внешние группы в связанных с ними клиентах Azure Active Directory](api-management-howto-aad.md/#how-to-add-an-external-azure-active-directory-group). Чтобы обеспечить разработчикам видимость и предоставить доступ к продуктам API, пользовательские и внешние группы можно использовать вместе с системными группами. Например, можно создать одну пользовательскую группу для разработчиков, связанных с конкретной партнерской организацией, и предоставить им доступ к интерфейсам API в продукте, содержащем только соответствующие интерфейсы API. Пользователь может входить в несколько групп.

В этом руководстве показано, как администраторы экземпляра API Management могут добавлять новые группы и связывать их с продуктами и разработчиками.

## <a name="create-group"></a>Создание группы

Чтобы создать новую группу, щелкните **Управление** на портале Azure службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][] в руководстве [Начинаем работу с API Management][].

Щелкните **Группы** в расположенном слева меню **API Management**, а затем **Добавить группу**.

![Добавление новой группы][api-management-add-group]

Введите уникальное имя для группы и необязательное описание, а затем щелкните **Сохранить**.

![Добавление новой группы][api-management-add-group-window]

Новая группа отображается на вкладке групп. Для изменения **имени** или **описания** группы щелкните имя группы в списке. Чтобы удалить группу, щелкните **Удалить**.

![Группа добавлена][api-management-new-group]

Теперь, когда группа создана, ее можно связать с продуктами и разработчиками.

## <a name="associate-group-product"></a>Связывание группы с продуктом

Для связи группы с продуктом щелкните **Продукты** в расположенном слева меню **API Management**, а затем щелкните имя требуемого продукта.

![Настройка видимости][api-management-add-group-to-product]

Для добавления и удаления групп, а также просмотра текущих групп для продукта перейдите на вкладку **Видимость**. Для добавления или удаления групп установите или снимите флажки для требуемых групп и щелкните **Сохранить**.

![Настройка видимости][api-management-add-group-to-product-visibility]

>[AZURE.NOTE]Дополнительную информацию о добавлении групп Azure Active Directory см. в статье [Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure](api-management-howto-aad.md).
>
>Для настройки групп на вкладке **Видимость** для продукта щелкните **Управление группами**.

После установления связи между продуктом и группой разработчики в данной группе могут просматривать продукт и подписаться на него.

## <a name="associate-group-developer"></a>Связывание групп с разработчиками

Чтобы связать группы с разработчиками, щелкните **Пользователи** в расположенном слева меню **Управление API**, а затем установите флажок рядом с разработчиками, которых необходимо связать с группой.

![Добавление разработчика в группу][api-management-add-group-to-developer]

После выбора требуемых разработчиков щелкните требуемую группу в раскрывающемся списке **Добавить в группу**. Для удаления разработчиков из групп используйте раскрывающийся список **Удалить из группы**.

![Разработчики][api-management-add-group-to-developer-saved]

После добавления связи между разработчиком и группой ее можно просмотреть на вкладке **Пользователи**.

## <a name="next-steps"> </a>Дальнейшие действия

После добавления разработчика в группу их можно просматривать и подписать на продукты, связанные с данной группой. Дополнительные сведения см. в разделе [Как создавать и публиковать продукт в Azure API Management][],


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

[Как создавать и публиковать продукт в Azure API Management]: api-management-howto-add-products.md

[Начинаем работу с API Management]: api-management-get-started.md
[Создание экземпляра службы API Management]: api-management-get-started.md#create-service-instance

<!---HONumber=62-->
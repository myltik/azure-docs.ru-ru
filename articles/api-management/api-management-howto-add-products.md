<properties 
	pageTitle="Создание и публикация продукта в службе управления API Azure" 
	description="Сведения о создании и публикации продуктов в службе управления API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Создание и публикация продукта в службе управления API Azure

В службе управления API Azure продукт содержит один или несколько API вместе с квотой и условиями использования. Как только продукт будет опубликован, разработчики могут подписаться на него и начать использовать его интерфейсы API. В данном разделе рассматривается процесс создания продукта, добавления интерфейса API и публикации продукта для разработчиков.

## <a name="create-product"></a>Создание продукта

Операции добавляются и настраиваются для API на портале издателя. Чтобы перейти на портал издателя, на классическом портале Azure для службы управления API щелкните **Управление**.

![Издательский портал][api-management-management-console]

>Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра службы управления API][] в руководстве [Начинаем работу со службой управления API][].

Щелкните **Продукты** в меню слева для отображения страницы **Продукты**, а затем щелкните **Добавить продукт**.

![Продукты][api-management-products]

![Новый продукт][api-management-add-new-product]

Введите описательное имя для продукта в поле **Имя** и описание продукта в поле **Описание**.

Продукты в службе управления API могут быть **открытыми** или **защищенными**. Прежде чем можно будет использовать защищенные продукты, на них необходимо подписаться, а открытые продукты могут использоваться без подписки. Установите флажок **Требуется подписка**, чтобы создать защищенный продукт, требующий подписки. Это значение по умолчанию.

Установите флажок **Требуется утверждение администратором**, если вы хотите, чтобы администратор рассматривал и принимал или отклонял попытки подписки на этот продукт. Если флажок не установлен, попытки подписки получат автоматическое утверждение. Дополнительные сведения о подписках см. в разделе [Просмотр подписчиков продукта][].

Чтобы разрешить для учетных записей разработчика многократную подписку на продукт, установите флажок **Разрешить несколько подписок**. Если этот флажок не установлен, каждую учетную запись разработчика можно подписать на продукт только один раз.

![Неограниченное количество подписок][api-management-unlimited-multiple-subscriptions]

Чтобы ограничить количество одновременных подписок, установите флажок **Ограничить количество одновременных подписок до** и введите ограничение количества подписок. В следующем примере количество одновременных подписок ограничено до четырех на каждую учетную запись разработчика.

![Четыре подписки][api-management-four-multiple-subscriptions]

После настройки всех параметров нового продукта щелкните **Сохранить**, чтобы создать новый продукт.

![Продукты][api-management-products-page]

>По умолчанию новые продукты не опубликованы и видны только группе **Администраторы**.

Для настройки продукта щелкните имя продукта на вкладке **Продукты**.

## <a name="add-apis"></a>Добавление интерфейсов API в продукт

Страница **Продукты** содержит четыре ссылки для настройки: **Сводка**, **Параметры**, **Видимость** и **Подписчики**. На вкладке **Сводка** можно добавить интерфейсы API, а также опубликовать продукт или отменить его публикацию.

![Сводка][api-management-new-product-summary]

Прежде чем опубликовать продукт, необходимо добавить один или несколько интерфейсов API. Для этого щелкните **Добавить API к продукту**.

![Добавление интерфейсов API][api-management-add-apis-to-product]

Выберите требуемые интерфейсы API и щелкните **Сохранить**.

## <a name="add-description"></a>Добавление описательной информации в продукт

На вкладке **Параметры** можно вводить подробные сведения о продукте, например его назначение, интерфейсы API, к которым он предоставляет доступ, и другую полезную информацию. Данные сведения предназначены для разработчиков, которые будут вызывать API, и могут быть внесены в виде обычного текста или в формате HTML.

![Параметры продукта][api-management-product-settings]

Установите флажок **Требуется подписка**, чтобы создать защищенный продукт, который требует подписку для использования, или снимите флажок, чтобы создать открытый продукт, который можно вызвать без подписки.

Установите флажок **Требуется утверждение администратором**, если вы хотите вручную утверждать все запросы подписки на продукт. По умолчанию все подписки на продукт предоставляются автоматически.

Чтобы разрешить для учетных записей разработчика многократную подписку на продукт, установите флажок **Разрешить несколько подписок** и по желанию укажите ограничение. Если этот флажок не установлен, каждую учетную запись разработчика можно подписать на продукт только один раз.

Дополнительно в поле **Условия использования** можно указать условия применения продукта, который должны принимать подписчики, если они хотят использовать продукт.

## <a name="publish-product"> </a>Публикация продукта

Прежде чем можно будет вызывать интерфейсы API в продукте, сам продукт должен быть опубликован. На вкладке **Сводка** для продукта щелкните **Опубликовать**, а затем **Да, опубликовать** для подтверждения. Для отмены публикации уже опубликованного продукта щелкните **Отменить публикацию**.

![Публикация продукта][api-management-publish-product]

## <a name="make-visible"> </a>Обеспечение видимости продукта для разработчиков

На вкладке **Видимость** можно указать, какие роли смогут видеть продукт на портале разработчика и подписываться на продукт.

![Видимость продукта][api-management-product-visiblity]

Чтобы включить или отключить видимость продукта для разработчиков в группе, установите или снимите флажок для группы, а затем щелкните **Сохранить**.

>Дополнительные сведения см. в разделе [Как создать и использовать группы для управления учетными записями разработчиков в службе управления Azure API][].

## <a name="view-subscribers"> </a>Просмотр подписчиков продукта

На вкладке **Подписчики** содержится перечень разработчиков, которые подписались на продукт. Для просмотра сведений и параметров для каждого разработчика щелкните по имени разработчика. В этом примере никакие разработчики еще не подписались на продукт.

![Разработчики][api-management-developer-list]

## <a name="next-steps"> </a>Дальнейшие действия

Как только требуемые интерфейсы API добавлены, а продукт опубликован, разработчики могут подписаться на продукт и начать вызывать интерфейсы API. Учебник, в котором рассматриваются эти операции, а также подробно описывается конфигурация продукта, см. в разделе [Как создать и настраивать расширенные параметры продукта в службе управления Azure API][].

Дополнительную информацию о работе с продуктами см. в следующем видео.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Просмотр подписчиков продукта]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Начинаем работу со службой управления API]: api-management-get-started.md
[Создание экземпляра службы управления API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Как создать и использовать группы для управления учетными записями разработчиков в службе управления Azure API]: api-management-howto-create-groups.md
[Как создать и настраивать расширенные параметры продукта в службе управления Azure API]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0309_2016-->
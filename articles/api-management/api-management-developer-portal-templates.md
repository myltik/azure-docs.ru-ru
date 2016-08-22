<properties 
	pageTitle="Настройка портала разработчика в службе управления API Azure с помощью шаблонов | Microsoft Azure" 
	description="Получите дополнительные сведения о настройке портала разработчика в службе управления API Azure с помощью шаблонов" 
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
	ms.date="08/09/2016" 
	ms.author="sdanie"/>


# Настройка портала разработчика в службе управления API Azure с помощью шаблонов

Служба управления API Azure предоставляет несколько функций настройки, которые позволяют администраторам [настраивать внешний вид портала разработчика](api-management-customize-portal.md), а также содержимое страниц портала разработчика с помощью набора шаблонов, которые регулируют содержимое самих страниц. С помощью синтаксиса [DotLiquid](http://dotliquidmarkup.org/) и указанного набора локализованных строковых ресурсов, значков и элементов управления на странице можно гибко настраивать содержимое страниц по своему усмотрению с использованием этих шаблонов.

## Обзор шаблонов портала разработчика

Управление шаблонами портала разработчика осуществляют администраторы службы управления API на портале разработчика. Для управления шаблонами разработчика перейдите к экземпляру службы управления API на классическом портале Azure и нажмите кнопку **Обзор**.

![Портал разработчика][api-management-browse]

Если вы уже находитесь на портале публикации, можно открыть портал разработчика, щелкнув **Портал разработчика**.

![Меню портала разработчика][api-management-developer-portal-menu]

Чтобы открыть шаблоны портала разработчиков, щелкните значок настройки слева, чтобы открылось меню настройки, а затем **Шаблоны**.

![Шаблоны портала разработчика][api-management-customize-menu]

В списке шаблонов отображается несколько категорий шаблонов, охватывающих различные страницы портала разработчика. Все шаблоны отличаются друг от друга, но действия, которые нужно выполнить для их изменения и публикации, одинаковы. Чтобы изменить шаблон, щелкните имя шаблона.

![Шаблоны портала разработчика][api-management-templates-menu]

Щелкнув шаблон, можно перейти на страницу портала разработчика, которая настраивается с помощью этого шаблона. В этом примере отображается шаблон **Список продуктов**. Шаблон **Список продуктов** управляет областью экрана, обозначенной красным прямоугольником.

![Шаблон "Список продуктов"][api-management-developer-portal-templates-overview]

Некоторые шаблоны, например шаблоны **Профиля пользователя**, настраивают другие области на той же странице.

![Шаблоны "Профиль пользователя"][api-management-user-profile-templates]

Редактор для каждого шаблона портала разработчика состоит из двух разделов, отображаемых в нижней части страницы. Слева отображается область редактирования шаблона, а справа — модель данных для шаблона.

Область редактирования шаблона содержит разметку, которая определяет внешний вид и поведение соответствующей страницы на портале разработчика. В разметке шаблона используется синтаксис [DotLiquid](http://dotliquidmarkup.org/). Один из популярных редакторов для DotLiquid — [DotLiquid для конструкторов](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Любые изменения, внесенные в шаблон во время редактирования, отображаются в браузере в режиме реального времени, но не видны клиентам, пока вы не [сохраните](#to-save-a-template) и не [опубликуете](#to-publish-a-template) шаблон.

![Разметка шаблона][api-management-template]

В области **Данные шаблона** содержится руководство по модели данных для сущностей, которые доступны для использования в данном шаблоне. Это руководство предоставляется путем вывода реальных данных, отображаемых в данный момент на портале разработчика. Панели шаблонов можно развернуть, щелкнув прямоугольник в правом верхнем углу области **Данные шаблона**.

![Модель данных шаблона][api-management-template-data]

В предыдущем примере на портале разработчика отображаются два продукта, которые получены из данных, отображаемых в области **Данные шаблона**, как показано в следующем примере.

	{
		"Paging": {
			"Page": 1,
			"PageSize": 10,
			"TotalItemCount": 2,
			"ShowAll": false,
			"PageCount": 1
		},
		"Filtering": {
			"Pattern": null,
			"Placeholder": "Search products"
		},
		"Products": [
			{
				"Id": "56ec64c380ed850042060001",
				"Title": "Starter",
				"Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
				"Terms": "",
				"ProductState": 1,
				"AllowMultipleSubscriptions": false,
				"MultipleSubscriptionsCount": 1
			},
			{
				"Id": "56ec64c380ed850042060002",
				"Title": "Unlimited",
				"Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
				"Terms": null,
				"ProductState": 1,
				"AllowMultipleSubscriptions": false,
				"MultipleSubscriptionsCount": 1
			}
		]
	}

Для получения необходимых выходных данных разметка в шаблоне **Список продуктов** обрабатывает данные путем прохода по коллекции продуктов для отображения сведений о каждом отдельном продукте и ссылки на этот продукт. Обратите внимание на элементы `<search-control>` и `<page-control>` в разметке. Они управляют отображением элементов поиска и разбиения по страницам. `ProductsStrings|PageTitleProducts` представляет собой ссылку на локализованную строку, которая содержит текст заголовка `h2` для страницы. Список строковых ресурсов, элементов управления страницы и значков, которые доступны для использования в шаблонах портала разработчиков, см. в разделе [Справочник по шаблонам портала разработчика в службе управления API](https://msdn.microsoft.com/library/azure/mt697540.aspx).

	<search-control></search-control>
	<div class="row">
	    <div class="col-md-9">
	        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
	    </div>
	</div>
	<div class="row">
	    <div class="col-md-12">
		{% if products.size > 0 %}
		<ul class="list-unstyled">
		{% for product in products %}
			<li>
				<h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
				{{product.description}}
			</li>	
		{% endfor %}
		</ul>
		<paging-control></paging-control>
		{% else %}
		{% localized "CommonResources|NoItemsToDisplay" %}
		{% endif %}
		</div>
	</div>

## Сохранение шаблона

Чтобы сохранить шаблон, нажмите кнопку "Сохранить" в редакторе шаблона.

![Сохранение шаблона][api-management-save-template]

Сохраненные изменения не отобразятся на портале разработчика, пока шаблон не будет опубликован.

## Публикация шаблона

Сохраненные шаблоны могут быть опубликованы по отдельности или все вместе. Чтобы опубликовать отдельный шаблон, нажмите кнопку "Опубликовать" в редакторе шаблона.

![Публикация шаблона][api-management-publish-template]

Щелкните **Да** для подтверждения публикации шаблона, после которой он станет доступным на портале разработчика.

![Подтверждение публикации][api-management-publish-template-confirm]

Чтобы опубликовать все неопубликованные версии шаблонов, нажмите **Опубликовать** в списке шаблонов. Неопубликованные шаблоны обозначены звездочкой рядом с именем шаблона. В этом примере публикуются шаблоны **Список продуктов** и **Продукт**.

![Публикация шаблонов][api-management-publish-templates]

Щелкните **Опубликовать настройки** для подтверждения.

![Подтверждение публикации][api-management-publish-customizations]

Недавно опубликованные шаблоны немедленно вступают в силу на портале разработчика.

## Возврат к предыдущей версии шаблона

Чтобы вернуться к предыдущей опубликованной версии шаблона, нажмите кнопку "Отменить изменения" в редакторе шаблонов.

![Отмена изменений шаблона][api-management-revert-template]

Нажмите кнопку **Да** для подтверждения.

![Подтверждение][api-management-revert-template-confirm]

Ранее опубликованная версия шаблона станет активной на портале разработчика после завершения операции восстановления.

## Восстановление шаблона до версии по умолчанию

Восстановление шаблонов до версии по умолчанию выполняется в два этапа. Сначала необходимо восстановить шаблоны, а затем опубликовать восстановленные версии.

Чтобы восстановить одиночный шаблон до версии по умолчанию, нажмите кнопку "Восстановить" в редакторе шаблона.

![Отмена изменений шаблона][api-management-reset-template]

Нажмите кнопку **Да** для подтверждения.

![Подтверждение][api-management-reset-template-confirm]

Чтобы восстановить все шаблоны до версии по умолчанию, нажмите кнопку **Восстановить шаблоны по умолчанию** в списке шаблонов.

![Восстановление шаблонов][api-management-restore-templates]

Затем необходимо опубликовать восстановленные шаблоны вместе или по отдельности, выполнив действия, описанные в разделе [Публикация шаблона](#to-publish-a-template).

## Справочник по шаблонам портала разработчика

Справочную информацию о шаблонах портала разработчика, строковых ресурсах, значках и элементах управления страницы см. в разделе [Справочник по шаблонам портала разработчика в службе управления API](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## Просмотр видеообзора

Посмотрите следующий видеоролик, чтобы узнать, как добавить доски обсуждений и оценки на страницы API и страницы операций на портале разработчика с помощью шаблонов.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png

<!---HONumber=AcomDC_0810_2016-->
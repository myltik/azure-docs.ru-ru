---
title: "Настройка стилей на портале разработчика в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как изменить стили, используемые для любой страницы портала разработчика в службе управления API Azure."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 336d4f80f0357796fb29eb9314c11edfce831a69
ms.openlocfilehash: bd08eb476a4bd7298c5650977b88ba0b24deddec
ms.lasthandoff: 02/23/2017


---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Настройка стилей портала разработчика в службе управления API Azure
Существуют три основных способа настройки портала разработчика в службе управления Azure API.

* [Изменение содержимого статических страниц и элементов макета страницы.][modify-content-layout]
* [Обновление стилей, которые используются для элементов страницы на портале разработчика][customize-styles] (как описано в этом руководстве).
* [Изменение шаблонов, используемых для страниц, созданных порталом][portal-templates] (например, документы API, продукты, аутентификация пользователей и т. д.).

## <a name="change-headers-styling"> </a>Изменение стиля элементов страницы

Цвета, шрифты, размеры, отступы и другие элементы стиля страниц портала задаются правилами стиля. 

Чтобы изменить правила стилей, войдите на **портал разработчика** в качестве администратора. Чтобы открыть его, войдите на портал Azure и щелкните **Портал издателя** на панели инструментов для вашего экземпляра службы управления API.

![Портал издателя][api-management-management-console]

Затем щелкните **Портал разработчика** в правом верхнем углу. 

![Ссылка на портал разработчика на портале издателя][api-management-pp-dp-link]

Чтобы открыть панель инструментов настройки, наведите указатель мыши на значок настройки (или выберите его) и щелкните "Стили" на панели инструментов.

![Кнопка на панели инструментов настройки][api-management-customization-toolbar-button]

Существует два основных способа редактирования правил стилей: просмотрите список всех используемых правил стиля, который отображается по умолчанию, и при необходимости измените стиль или щелкните **Select an element on the page** (Выбрать элемент на странице), а затем щелкните в любом месте страницы, чтобы просмотреть стили только для этого элемента.

![Панель инструментов настройки][api-management-customization-toolbar]

Для данного примера щелкните параметр **Select an element on the page** (Выбрать элемент на странице).  Теперь элементы будут выделяться при наведении на них указателя мыши. Это позволяет понять, стиль какого элемента вы будете редактировать, если щелкнете мышью. Наведите указатель мыши на текст в заголовке (обычно это название компании) и щелкните его. В редакторе стилей появится именованный и упорядоченный по категориям набор правил стилей. Каждое правило представляет свойство стиля выбранного элемента. Например, для текста выбранного выше заголовка размер текста содержится в @font-size-h1, а имя шрифта с вариантами содержится в @headings-font-family.

> Если вы знакомы [с Bootstrap Framework][bootstrap], эти правила на самом деле подобны [переменным LESS][LESS variables] в теме начальной загрузки, используемой порталом разработчика.
> 
> 

Попробуем изменить цвет текста заголовка. Выберите запись в поле **@headings-color** и введите **#000000**. Это шестнадцатеричный код черного цвета. После этого в конце текстового блока появится квадратный цветовой индикатор. Щелкнув его, вы сможете выбрать цвет.

![Выбор цвета][api-management-customization-toolbar-color-picker]

Вносимые изменения можно видеть в режиме реального времени, но они будут видны только администраторам. Чтобы сделать изменения видимыми для всех, нажмите кнопку **Опубликовать** в редакторе стилей и подтвердите внесение изменений.

![Меню публикации][api-management-customization-toolbar-publish-form]

> Чтобы изменить правила стиля, которые применяются к любому другому элементу на странице, выполните ту же процедуру, что и для заголовка. Щелкните **Select an element on the page** (Выбрать элемент на странице) в редакторе стилей, выберите интересующий вас элемент и измените значения для правил стилей, отображаемых на экране.
> 
> 


## <a name="next-steps"> </a>Дальнейшие действия
* Узнайте, как настроить содержимое страницы портала разработчика с помощью [шаблонов портала разработчиков](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/


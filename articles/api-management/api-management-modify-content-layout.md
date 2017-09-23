---
title: "Изменение содержимого страниц на портале разработчика в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как изменить содержимое страниц на портале разработчика в службе управления API Azure."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: ecae1da20551d8372331124b07c4aca2e15f55bb
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.contentlocale: ru-ru
ms.lasthandoff: 02/23/2017

---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Изменение содержимого и макета страниц на портале разработчика в службе управления API Azure
Существуют три основных способа настройки портала разработчика в службе управления Azure API.

* [Изменение содержимого статических страниц и элементов макета страницы][modify-content-layout] (описывается в этом руководстве).
* [Обновление стилей, которые используются для элементов страницы на портале разработчика][customize-styles].
* [Изменение шаблонов, используемых для страниц, созданных порталом][portal-templates] (например, документы API, продукты, аутентификация пользователей и т. д.).

## <a name="page-structure"> </a>Структура страниц портала разработчика

Портал разработчика основан на системе управления контентом. Макет каждой страницы состоит из набора небольших элементов страницы, называемых мини-приложениями.

![Структура страницы портала разработчика][api-management-customization-widget-structure]

Все мини-приложения можно изменять. 
* Основное содержимое для каждой отдельной страницы находится в мини-приложении "Содержимое". Изменение страницы означает изменение содержимого этого мини-приложения.
* Все элементы макета страницы содержатся в остальных мини-приложениях. Изменения, внесенные в эти мини-приложения, применяются ко всем страницам. Они будут называться "мини-приложениями макета".

В повседневном редактировании страниц обычно изменяется только содержимое мини-приложения "Содержимое", содержимое которого будет отличаться для каждой отдельной страницы.

## <a name="modify-layout-widget"> </a>Изменение содержимого мини-приложения макета

Контент портала разработчика можно изменять с портала издателя, который доступен на портале Azure. Чтобы открыть его, щелкните **Publisher portal** (Портал издателя) на панели инструментов для вашего экземпляра службы управления API.

![Портал издателя][api-management-management-console]

Для изменения содержимого этого мини-приложения щелкните **Мини-приложения** в меню **Портал разработчика** слева. Давайте в рамках нашего примера изменим содержимое мини-приложения "Заголовок". Выберите мини-приложение **Заголовок** из списка.

![Заголовок мини-приложения][api-management-widgets-header]

Содержимое заголовка можно редактировать в поле **Текст** . Измените текст на свое усмотрение и в нижней части страницы нажмите кнопку **Сохранить**.

Теперь новый заголовок будет отображаться на каждой странице портала разработчика.

> Чтобы открыть портал разработчика на портале издателя, выберите **Портал разработчика** на верхней панели.
> 
> 

## <a name="edit-page-contents"> </a>Редактирование содержимого страницы

Чтобы увидеть список всех страниц с контентом, выберите пункт **Контент** в меню **Портал разработчика** на портале издателя.

![Управление содержимым][api-management-customization-manage-content]

Щелкните **страницу приветствия** , чтобы отредактировать контент на домашней странице портала разработчика. Внесите изменения, при необходимости просмотрите их, а затем щелкните **Опубликовать сейчас** , чтобы сделать их видимыми для всех.

> На домашней странице используется особый макет, который позволяет выводить вверху баннер. Этот баннер нельзя редактировать в разделе **Контент** . Чтобы отредактировать его, щелкните **Мини-приложения** в меню **Портал разработчика**, затем выберите **Домашняя страница** из раскрывающегося меню **Текущий слой** и откройте пункт **Баннер** в разделе **Особые свойства**. Содержимое в этом мини-приложении редактируется так же, как для других страниц.
> 
> 

## <a name="next-steps"> </a>Дальнейшие действия
* [Обновление стилей, которые используются для элементов страницы на портале разработчика][customize-styles].
* [Изменение шаблонов, используемых для страниц, созданных порталом][portal-templates] (например, документы API, продукты, аутентификация пользователей и т. д.).

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png


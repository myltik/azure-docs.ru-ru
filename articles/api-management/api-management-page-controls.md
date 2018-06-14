---
title: Элементы управления страницей в службе управления API Azure | Документация Майкрософт
description: Сведения об элементах управления страницей, доступных для использования в шаблонах портала разработчика в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: da68c9b7ebbb1880e35bd60b12db9f920f51e13c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936167"
---
# <a name="azure-api-management-page-controls"></a>Элементы управления страницей в службе управления API Azure
Служба управления API Azure предоставляет следующие элементы управления страницей для использования в шаблонах портала разработчика.  
  
Чтобы применить элемент управления, поместите его в нужное место шаблона портала разработчика. Некоторые элементы управления имеют параметры, как например [app-actions](#app-actions), представленный в следующем примере.  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Значения для этих параметров передаются в составе модели данных для шаблона. В большинстве случаев достаточно просто скопировать представленный пример для элемента управления, и он сразу будет правильно работать. Дополнительные сведения о значениях параметров вы найдете в разделе модели данных для каждого шаблона, в котором можно использовать соответствующий элемент управления.  
  
 Дополнительные сведения о работе с шаблонами см. в статье [Настройка портала разработчика в службе управления API Azure с помощью шаблонов](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Элементы управления страницей в шаблонах портала разработчика  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 Элемент управления `app-actions` предоставляет пользовательский интерфейс для взаимодействия с приложениями на странице профиля пользователя на портале разработчика.  
  
 ![элемент управления действиями](./media/api-management-page-controls/APIM-app-actions-control.png "элемент управления действиями для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Параметры  
  
|Параметр|ОПИСАНИЕ|  
|---------------|-----------------|  
|appId|Идентификатор приложения.|  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `app-actions` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Приложения](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 Элемент управления `basic-signin` предоставляет интерфейс для сбора учетных данных пользователей на странице входа на портал разработчика.  
  
 ![базовый элемент управления входом](./media/api-management-page-controls/APIM-basic-signin-control.png "базовый элемент управления входом для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `basic-signin` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Вход](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 Элемент `paging-control` предоставляет функцию разбиения по страницам, которую можно использовать на страницах со списками на портале разработчика.  
  
 ![элемент управления разбиением по страницам ](./media/api-management-page-controls/APIM-paging-control.png "элемент управления разбиением по страницам для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `paging-control` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Список API](api-management-api-templates.md#APIList)  
  
-   [Список проблем](api-management-issue-templates.md#IssueList)  
  
-   [Список продуктов](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> providers  
 Элемент управления `providers` предоставляет интерфейс для выбора поставщика проверки подлинности на странице входа на портал разработчика.  
  
 ![Элемент управления поставщиками](./media/api-management-page-controls/APIM-providers-control.png "Элемент управления поставщиками для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `providers` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Вход](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 Элемент `search-control` предоставляет функцию поиска, которую можно использовать на страницах со списками на портале разработчика.  
  
 ![Элемент управления поиском](./media/api-management-page-controls/APIM-search-control.png "Элемент управления поиском для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `search-control` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Список API](api-management-api-templates.md#APIList)  
  
-   [Список продуктов](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 Элемент управления `sign-up` предоставляет интерфейс для сбора информации о профиле пользователя на странице регистрации на портале разработчика.  
  
 ![Элемент управления регистрацией](./media/api-management-page-controls/APIM-sign-up-control.png "Элемент управления регистрацией для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `sign-up` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Регистрация](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 Элемент `subscribe-button` предоставляет функцию оформления подписки пользователя на продукт.  
  
 ![Элемент управления подпиской](./media/api-management-page-controls/APIM-subscribe-button-control.png "Элемент управления подпиской для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Параметры  
 Отсутствует.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `subscribe-button` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Продукт](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 Элемент управления `subscription-cancel` предоставляет интерфейс для отмены подписки на продукт на странице профиля пользователя на портале разработчика.  
  
 ![Элемент управления отменой подписки](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Элемент управления отменой подписки для APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Параметры  
  
|Параметр|ОПИСАНИЕ|  
|---------------|-----------------|  
|subscriptionId|Идентификатор подписки, которую следует отменить.|  
|cancelUrl|URL-адрес для отмены подписки.|  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `subscription-cancel` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Продукт](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о работе с шаблонами см. в статье [Настройка портала разработчика в службе управления API Azure с помощью шаблонов](api-management-developer-portal-templates.md).
---
title: "Настройка пользовательской домашней страницы для опубликованного приложения с помощью прокси приложения Azure AD | Документация Майкрософт"
description: "Основные сведения о соединителях прокси приложения Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Настройка пользовательской домашней страницы для опубликованного приложения с помощью прокси приложения Azure AD

Эта статья содержит сведения о том, как настроить в приложении пользовательскую домашнюю страницу, на которую будут перенаправляться пользователи, переходящие к приложению из панели доступа Azure AD или из средства запуска приложений Office 365.

>[!NOTE]
>Прокси приложения — это функция, которая доступна только после обновления до выпуска Premium или Basic службы Azure Active Directory. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).
> 
 
С помощью модуля Azure AD PowerShell можно задать URL-адреса пользовательской домашней страницы для тех случаев, когда пользователи должны попадать на определенную страницу в приложении, например https://expenseApp-contoso.msappproxy.net/login/login.aspx.

>[!NOTE]
>Когда вы предоставляете пользователям доступ к опубликованному приложению, это приложение отображается на [панели доступа Azure AD](active-directory-saas-access-panel-introduction.md) и в [средстве запуска приложений Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher). 
>

Пользователь, открывающий это приложение, по умолчанию направляется на корневой URL-адрес домена, указанного для опубликованного приложения. Обычно целевой страницей считается URL-адрес домашней страницы приложения. Например, для серверного приложения http://ExpenseApp публикуется адрес https://expenseApp-contoso.msappproxy.net. По умолчанию для домашней страницы задан URL-адрес https://expenseApp-contoso.msappproxy.net.

## <a name="determine-the-home-page-url"></a>Определение URL-адреса домашней страницы

Прежде чем настраивать URL-адрес домашней страницы, следует учесть несколько важных аспектов.

* Указанный путь должен содержать URL-адрес поддомена корневого домена.

 Например, если домашняя страница опубликованного приложения имеет URL-адрес https://intranet-contoso.msappproxy.net/, то URL-адрес настраиваемой домашней страницы должен начинаться с https://intranet-contoso.msappproxy.net/. 
 
* Если URL-адрес домашней страницы имеет значение https://apps.contoso.com/app1/, URL-адрес домашней страницы должен начинаться с https://apps.contoso.com/app1/.

* Когда вы вносите изменения в опубликованное приложение, URL-адрес домашней страницы может вернуться к значению по умолчанию. Поэтому при любых обновлениях приложения следует проверить URL-адрес домашней страницы и скорректировать его при необходимости.


В следующем разделе мы рассмотрим процесс изменения URL-адреса пользовательской домашней страницы для опубликованных приложений. 

## <a name="install-the-azure-ad-powershell-module"></a>Установка модуля Azure AD PowerShell

Прежде чем настраивать URL-адрес пользовательской домашней страницы с помощью PowerShell, необходимо установить нестандартный пакет модуля Azure AD PowerShell.  Этот пакет можно скачать из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), в которой используется конечная точка API GRAPH. 

**Установка пакета с помощью PowerShell**

1. Откройте стандартный интерфейс PowerShell.
2. Выполните следующую команду:

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 Если вы запускаете команду не от имени администратора, используйте параметр _-scope currentuser_.
3. Во время установки выберите ответ "Y" на предложение установить два пакета с сайта Nuget.org.  Оба они необходимы для использования этого пакета. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Настройка URL-адреса пользовательской домашней страницы с помощью модуля Azure AD PowerShell

Теперь нужный модуль Azure AD PowerShell установлен, и вы можете настроить URL-адрес домашней страницы, выполнив два простых шага.

1. Найдите приложение, которое нужно обновить.
2. Измените URL-адрес домашней страницы для этого приложения.

###<a name="step-1--find-the-objectid-of-the-application"></a>Шаг 1. Поиск идентификатора ObjectID для приложения

Прежде всего вам нужно получить ObjectID для приложения, а затем найти приложение по его домашней странице.

1. Откройте PowerShell.
2. Импортируйте модуль Azure AD.
  
 ```
 Import-Module AzureAD
 ```
3. Войдите в модуль Azure AD.  Выполните приведенный ниже командлет и следуйте инструкциям на экране. Для входа используйте учетные данные администратора клиента.
 
 ```
 Connect-AzureAD
 ```
4. Этот командлет находит приложение, в адресе домашней страницы которого есть элемент _sharepoint-iddemo_. Это приложение, которое требуется изменить. Замените это значение тем, которое подходит для вашего приложения.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. Вы получите примерно такой результат, как показано ниже. Из него нужно скопировать идентификатор GUID (значение ObjectID в приведенном ниже примере).
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. Скопируйте значение идентификатора GUID (ObjectID). Он потребуется для выполнения следующего шага.


###<a name="step-2--update-the-homepage-url"></a>Шаг 2. Изменение URL-адреса домашней страницы

Для изменения URL-адреса домашней страницы используйте тот же модуль PowerShell, с помощью которого вы ранее нашли идентификатор приложения. После входа в PowerShell выполните следующие действия.

1. Убедитесь, что найдено правильное приложение, затем замените значение _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ значением ObjectID, которое вы нашли для своего приложения и скопировали на шаге 1. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 Убедившись, что это нужное приложение, вы можете изменить для него адрес домашней страницы, как описано далее.
 
2. Создайте пустой объект приложения, в котором будут храниться вносимые изменения. Это только переменная, в которой содержатся значения для обновления, и она не создает никаких реальных объектов.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. Задайте нужное значение для домашней страницы. Не забывайте, что это должен быть путь к поддомену для опубликованного приложения. Например, если вместо домашней страницы _https://sharepoint-iddemo.msappproxy.net/_ вы укажете значение _https://sharepoint-iddemo.msappproxy.net/hybrid/_, пользователи будут перенаправляться на новую пользовательскую домашнюю страницу.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. И наконец, для завершения процесса выполните обновление. Обязательно используйте идентификатор GUID, который вы скопировали на шаге 1 выше.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. Теперь снова запустите приложение, чтобы убедиться, что изменения внесены успешно и открывается пользовательская домашняя страница.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>Имейте в виду, что любые изменения, внесенные в приложение, могут привести к сбросу URL-адреса домашней страницы. В таком случае необходимо повторить этот процесс.

##<a name="next-steps"></a>Дальнейшие действия

[Enable remote access to SharePoint with Azure AD App Proxy](application-proxy-enable-remote-access-sharepoint.md) (Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD)<br>
[Enable Application Proxy in the Azure portal](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md) (Включение прокси приложения на портале Azure)



<!--HONumber=Feb17_HO1-->



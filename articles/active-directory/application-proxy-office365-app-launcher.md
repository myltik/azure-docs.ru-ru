---
title: "Настройка пользовательской домашней страницы для опубликованных приложений с помощью прокси приложения Azure AD | Документация Майкрософт"
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
ms.sourcegitcommit: 5818efb315a0452beea03cde1adc657a9520dcac
ms.openlocfilehash: 1fe3f3a697618bec5d314c6ebf161da37efc1346
ms.lasthandoff: 03/01/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Настройка пользовательской домашней страницы для опубликованных приложений с помощью прокси приложения Azure AD

В этой статье объясняется, как настроить приложения для перенаправления пользователей на пользовательскую домашнюю страницу, когда они открывают приложения из панели доступа Azure Active Directory (Azure AD) или из средства запуска приложений Office 365.

>[!NOTE]
>Функция прокси приложения доступна только после обновления службы Azure Active Directory до выпуска Premium или Basic. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).
>

С помощью модуля Azure AD PowerShell можно определить URL-адреса пользовательской домашней страницы для тех случаев, когда пользователи должны переходить на определенную страницу в приложении, например *https://expenseApp-contoso.msappproxy.net/login/login.aspx*.

>[!NOTE]
>Когда вы предоставляете пользователям доступ к опубликованным приложениям, эти приложения отображаются на [панели доступа Azure AD](active-directory-saas-access-panel-introduction.md) и в [средстве запуска приложений Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).
>

Пользователь, открывающий это приложение, по умолчанию перенаправляется на корневой URL-адрес домена опубликованного приложения. Обычно целевой страницей считается URL-адрес домашней страницы приложения. Например, для серверного приложения http://ExpenseApp публикуется URL-адрес *https://expenseApp-contoso.msappproxy.net*. По умолчанию для домашней страницы указан URL-адрес *https://expenseApp-contoso.msappproxy.net*.

## <a name="determine-the-home-page-url"></a>Определение URL-адреса домашней страницы

Прежде чем указывать URL-адрес домашней страницы, учтите следующие факторы.

* Указанный путь должен содержать URL-адрес поддомена корневого домена.

 Например, если корневой домен опубликованного приложения находится по адресу https://intranet-contoso.msappproxy.net/, URL-адрес настраиваемой домашней страницы должен начинаться с https://intranet-contoso.msappproxy.net/.

* Если URL-адрес корневого домена — https://apps.contoso.com/app1/, URL-адрес настраиваемой домашней страницы должен начинаться с https://apps.contoso.com/app1/.

* Когда вы вносите изменения в опубликованное приложение, URL-адрес домашней страницы может вернуться к значению по умолчанию. Поэтому при любых обновлениях приложения следует повторно проверить URL-адрес домашней страницы и изменить его при необходимости.

В следующем разделе будет описана настройка URL-адреса пользовательской домашней страницы для опубликованного приложения. 

## <a name="install-the-azure-ad-powershell-module"></a>Установка модуля Azure AD PowerShell

Прежде чем настраивать URL-адрес пользовательской домашней страницы с помощью PowerShell, установите нестандартный пакет модуля Azure AD PowerShell. Этот пакет можно скачать из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), в которой используется конечная точка API Graph. 

Чтобы установить пакет с помощью PowerShell, выполните следующие действия.

1. Откройте стандартное окно PowerShell и выполните следующую команду.

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Если вы запускаете команду не от имени администратора, используйте параметр **-scope currentuser**.
2. Во время установки выберите ответ **Y** (Да) на предложение установить два пакета с сайта Nuget.org. Требуются оба пакета. 

## <a name="set-a-custom-home-page-url-by-using-the-azure-ad-powershell-module"></a>Настройка URL-адреса пользовательской домашней страницы с помощью модуля Azure AD PowerShell

Теперь модуль Azure AD PowerShell установлен, и вы можете настроить URL-адрес домашней страницы. Для этого следуйте инструкциям в следующих разделах.

### <a name="step-1-find-the-objectid-of-the-app"></a>Шаг 1. Поиск идентификатора ObjectID для приложения

Получите ObjectID приложения и затем найдите приложение по его домашней странице.

1. Откройте PowerShell и импортируйте модуль Azure AD с помощью следующей команды:

    ```
    Import-Module AzureAD
    ```

2. Войдите в модуль Azure AD с помощью следующего командлета и следуйте инструкциям на экране. Для входа обязательно используйте учетные данные администратора клиента.

    ```
    Connect-AzureAD
    ```
3. С помощью этого командлета найдите приложение на домашней странице, которая содержит *sharepoint-iddemo*. Чтобы изменить приложение, замените следующее значение фактическим значением приложения.

    ```
    Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
    ```
4. Вы должны получить результат, похожий на приведенный ниже. Скопируйте идентификатор GUID (ObjectID), чтобы использовать его на шаге 2 "Изменение URL-адреса домашней страницы".

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="step-2-update-the-home-page-url"></a>Шаг 2. Изменение URL-адреса домашней страницы

В том же модуле PowerShell, который использовался на шаге 1, выполните следующие действия.

1. Убедитесь, что вы нашли правильное приложение, затем замените значение *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* значением GUID (ObjectID), которое вы скопировали на предыдущем шаге.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Убедившись, что это нужное приложение, вы можете изменить для него адрес домашней страницы, как описано далее.

2. Создайте пустой объект приложения, в котором будут храниться ваши изменения.  

 >[!NOTE]
 >Это только переменная, в которой содержатся значения для обновления, и она не создает никаких реальных объектов.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Задайте нужное значение для URL-адреса домашней страницы. Это значение должно быть путем к поддомену опубликованного приложения. Например, если вместо домашней страницы *https://sharepoint-iddemo.msappproxy.net/* вы укажете значение *https://sharepoint-iddemo.msappproxy.net/hybrid/*, пользователи приложения будут сразу перенаправляться на пользовательскую домашнюю страницу.

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Внесите изменения с помощью GUID (ObjectID), который был скопирован на шаге 1 "Поиск идентификатора ObjectID для приложения".

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. Чтобы проверить, внесены ли изменения, перезапустите приложение.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Все изменения, которые вы внесли в приложение, могут сбросить URL-адрес домашней страницы. В этом случае повторите процедуру из раздела "Шаг 2. Изменение URL-адреса домашней страницы".

## <a name="next-steps"></a>Дальнейшие действия

[Настройка удаленного доступа к SharePoint с помощью прокси приложения Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Enable Application Proxy in the Azure portal](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md) (Включение прокси приложения на портале Azure)


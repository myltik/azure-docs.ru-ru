---
title: "Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: bd2c5364-4164-407d-ac25-34088c175c4a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ed32c23b8c69664dd75eae9c831341c93e57ebb3


---
# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory (Azure AD) B2B
Предварительная версия службы совместной работы Azure AD B2B требует CSV-файл, указывающий, какая информация о пользователе партнера должна быть загружена через портал Azure AD. CSV-файл должен содержать обязательные метки (см. ниже) и — при необходимости — необязательные поля. Измените пример CSV-файла (см. ниже), не меняя написание меток в первом ряду.

> [!NOTE]
> Первая строка меток (Email, DisplayName и т. д.) необходима для успешного анализа CSV-файла. Написание должно соответствовать указанным ниже полям. Эти метки определяют содержимое под ними. Метки необязательных полей, которые не нужны, можно удалить из CSV-файла. Весь столбец может быть пустым.
> 
> 

## <a name="required-fields-br"></a>Обязательные поля:  <br/>
**Email** — адреса электронной почты приглашенных пользователей. <br/>
**DisplayName** — отображаемое имя приглашенного пользователя (как правило, имя и фамилия).<br/>

## <a name="optional-fields-br"></a>Дополнительные поля:  <br/>
**InvitationText** — настройка текста приглашения по электронной почте, отображаемого между фирменной символикой приложения и ссылкой для его активации.<br/>
**InvitedToApplications** — идентификаторы AppID корпоративных приложений, назначаемые пользователям. Идентификаторы AppID можно получить в PowerShell путем вызова метода `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups** — идентификаторы ObjectID для групп, в которые необходимо добавить пользователя. Идентификаторы ObjectID можно получить в PowerShell путем вызова метода `Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL** — URL-адрес, на который будет направлен приглашенный пользователь после принятия приглашения. Необходимо указать URL-адрес компании (например, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Если значение в этом необязательном поле не задано, приглашенный пользователь направляется на панель доступа к приложениям, откуда можно перейти в выбранные корпоративные приложения вашей организации. URL-адрес панели доступа к приложениям имеет такой вид: `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress** — электронный адрес пересылки копии приглашения, отправленного по электронной почте. Если используется поле CcEmailAddress, это приглашение не может использоваться для создания пользователя или клиента с подтверждением по электронной почте.<br/>
**Language** — язык электронного приглашения и интерфейса активации. Если язык не указан, по умолчанию используется английский (код "en"). Коды еще 10 поддерживаемых языков:<br/>

1. de: немецкий;<br/>
2. es: испанский;<br/>
3. fr: французский;<br/>
4. it: итальянский;<br/>
5. ja: японский;<br/>
6. ko: корейский;<br/>
7. pt-BR: португальский (Бразилия);<br/>
8. ru: русский;<br/>
9. zh-HANS: китайский, упрощенное письмо;<br/>
10. zh-HANT: китайский, традиционное письмо.<br/>

## <a name="sample-csv-file"></a>Образец CSV-файла
Ниже приведен пример CSV-файла, который можно изменить.

> [!NOTE]
> Копируйте его и вставьте в Блокнот, а затем сохраните с расширением файла .csv. Затем отредактируйте файл в Excel. Данные будут структурированы в таблицу с метками в первой строке.
> 
> Добавьте еще несколько необязательных полей в Excel, указав метку и заполнив столбец под ней.
> 
> 

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

* [Обзор службы Azure AD B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Принцип работы](active-directory-b2b-how-it-works.md)
* [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
* [Формат токена внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
* [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->



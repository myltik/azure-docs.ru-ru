<properties
   pageTitle="Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory B2B | Microsoft Azure"
   description="Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory (Azure AD) B2B

Предварительная версия службы совместной работы Azure AD B2B требует CSV-файл, указывающий, какая информация о пользователе партнера должна быть загружена через портал Azure AD. CSV-файл должен содержать обязательные метки (см. ниже) и — при необходимости — необязательные поля. Измените пример CSV-файла (см. ниже), не меняя написание меток в первом ряду.

>[AZURE.NOTE] Первая строка меток (Email, DisplayName и т. д.) необходима для успешного анализа CSV-файла. Написание должно соответствовать указанным ниже полям. Эти метки определяют содержимое под ними. Метки необязательных полей, которые не нужны, можно удалить из CSV-файла. Весь столбец может быть пустым.

## Обязательные поля: <br/>
**Email** — адреса электронной почты приглашенных пользователей. <br/> **DisplayName** — отображаемое имя приглашенного пользователя (как правило, имя и фамилия).<br/>


## Дополнительные поля: <br/>

**InvitationText:** настройте текст приглашения по электронной почте, отображаемый между фирменной символикой приложения и ссылкой для его активации.<br/> **InvitedToApplications** — идентификаторы AppID корпоративных приложений, назначаемые пользователям. Идентификаторы приложений извлекаются в PowerShell вызовом метода `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>. **InvitedToGroups:** идентификаторы объектов для групп, в которые нужно добавить пользователя. Идентификаторы ObjectID извлекаются в PowerShell вызовом метода `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL:** URL-адрес для направления приглашенного пользователя после принятия приглашения. Необходимо указать URL-адрес компании (например, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Если значение в этом необязательном поле не задано, приглашенный пользователь направляется на панель доступа к приложениям, откуда можно перейти в выбранные корпоративные приложения вашей организации. URL-адрес панели доступа к приложениям имеет форму `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **CcEmailAddress**: электронный адрес пересылки копии приглашения, отправленного по электронной почте. Если используется поле CcEmailAddress, это приглашение не может использоваться для создания пользователя или клиента с подтверждением по электронной почте.<br/> **Language** — язык электронного приглашения и интерфейса активации. По умолчанию, если язык не указан, используется английский (код "en"). Коды еще 10 поддерживаемых языков:<br/>
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

## Образец CSV-файла
Ниже приведен пример CSV-файла, который можно изменить.

>[AZURE.NOTE] Копируйте его и вставьте в Блокнот, а затем сохраните с расширением файла .csv. Затем отредактируйте файл в Excel. Данные будут структурированы в таблицу с метками в первой строке.

> Добавьте еще несколько необязательных полей в Excel, указав метку и заполнив столбец под ней.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications, InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

- [Обзор службы Azure AD B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Принцип работы](active-directory-b2b-how-it-works.md)
- [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
- [Формат токена внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
- [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)
- [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->
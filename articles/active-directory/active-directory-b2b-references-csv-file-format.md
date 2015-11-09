<properties
   pageTitle="Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory B2B | Microsoft Azure"
   description="Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
   services="active-directory"
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
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Формат файлов CSV для предварительной версии службы совместной работы Azure Active Directory (Azure AD) B2B

Предварительная версия службы совместной работы Azure AD B2B требует CSV-файл, указывающий, какая информация о пользователе партнера должна быть загружена через портал Azure AD. CSV-файл должен содержать обязательные метки (см. ниже) и — при необходимости — необязательные поля. Измените образец CSV-файла (см. ниже), не меняя написание меток в первом ряду и не изменяя порядок столбцов.

>[AZURE.NOTE]Первая строка меток (Email, DisplayName...) необходима для успешного синтаксического анализа CSV-файла. Написание должно соответствовать указанным ниже полям. Эти метки определяют содержимое под ними. Метки необязательных полей, которые не нужны, можно удалить из CSV-файла. Весь столбец может быть пустым.

## Обязательные поля: <br/>
**Email** — адреса электронной почты приглашенных пользователей. <br/> **DisplayName** — отображаемое имя приглашенного пользователя (как правило, имя и фамилия).<br/> **InviteContactUsUrl** — URL-адрес для указания в электронных приглашениях на случай, если приглашенный пользователь захочет связаться с вашей организацией.<br/>

## Дополнительные поля: <br/>
**InviteAppID** — идентификатор приложения, который нужно использовать для брендового оформления электронного приглашения и страниц принятия приглашения.<br/> **InviteAppResources** — идентификаторы корпоративных приложений для назначения пользователей. Идентификаторы приложений извлекаются в PowerShell путем вызова метода `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** идентификаторы объектов для групп, в которые нужно добавить пользователя. Идентификаторы объектов извлекаются в PowerShell вызовом метода `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL:** URL-адрес для направления приглашенного пользователя после принятия приглашения. Необходимо указать URL-адрес компании (например, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Если значение в этом необязательном поле не задано, приглашенный пользователь направляется на панель доступа к приложениям, откуда можно перейти в выбранные корпоративные приложения вашей организации. URL-адрес панели доступа к приложениям имеет форму `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **Язык** — язык электронного приглашения и предоставляемых ресурсов. По умолчанию, если не указан другой язык, используется английский. Десять других поддерживаемых языковых кодов: <br/> 1. de: немецкий<br/> 2. es: испанский<br/> 3. fr: французский<br/> 4. it: итальянский<br/> 5. ja: японский<br/> 6. ko: корейский<br/> 7. pt-BR: португальский (Бразилия)<br/> 8. ru: русский<br/> 9. zh-HANS: упрощенный китайский<br/> 10. zh-HANT: традиционный китайский<br/>.

## Образец CSV-файла
Ниже приведен пример CSV-файла, который можно изменить.

>[AZURE.NOTE]Копируйте его и вставьте в Блокнот, а затем сохраните с расширением файла .csv. Затем отредактируйте файл в Excel. Данные будут структурированы в таблицу с метками в первой строке.

>[AZURE.NOTE]Добавьте еще несколько необязательных полей в Excel, указав метку и заполнив столбец под ней.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Связанные статьи
Ознакомьтесь с другими статьями о службе совместной работы Azure B2B.

- [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Принцип работы](active-directory-b2b-how-it-works.md)
- [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
- [Формат токена внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
- [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->
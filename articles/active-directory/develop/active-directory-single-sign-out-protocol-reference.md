---
title: Протокол единого выхода SAML в Azure | Документация Майкрософт
description: В этой статье описывается протокол единого выхода SAML в Azure Active Directory
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 9ec99ffc64138cf1cd94e0f11077cdc5d86dbc57
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155503"
---
# <a name="single-sign-out-saml-protocol"></a>Протокол единого выхода SAML
Azure Active Directory (Azure AD) поддерживает профиль SAML 2.0 для единого выхода с использованием веб-браузера. Чтобы единый выход работал правильно, во время регистрации приложения необходимо явно зарегистрировать в Azure AD его **LogoutURL** (URL-адрес выхода). Azure AD использует этот URL-адрес для перенаправления пользователей после их выхода.

Эта схема демонстрирует рабочий процесс единого выхода Azure AD.

![Рабочий процесс единого выхода](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Облачная служба отправляет сообщение `LogoutRequest` в Azure AD, чтобы сообщить о завершении сеанса. Ниже приведен пример элемента `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Элемент `LogoutRequest` , передаваемый в Azure AD, должен иметь следующие атрибуты.

* `ID` : признак запроса на выход. Значение `ID` не должно начинаться с цифры. Обычно здесь указывается строковое представление идентификатора GUID с добавлением перед ним строки **id** .
* `Version` : установите для этого элемента значение **2.0**. Это обязательное значение.
* `IssueInstant`: это строка `DateTime` со значением в формате всемирного времени (UTC) и с [преобразованием без потери данных ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD ожидает значение такого типа, но не требует его наличия.

### <a name="issuer"></a>Издатель
Элемент `Issuer` в `LogoutRequest` должен точно соответствовать одному из имен из списка **ServicePrincipalNames** в облачной службе в Azure AD. Обычно здесь передается **URI идентификатора приложения** , указанный во время регистрации приложения.

### <a name="nameid"></a>NameID
Значение элемента `NameID` должно точно совпадать с параметром `NameID` для пользователя, выполняющего выход.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD отправляет `LogoutResponse` в ответ на элемент `LogoutRequest`. Ниже приведен фрагмент кода с элементом `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD устанавливает значения `ID`, `Version` и `IssueInstant` для элемента `LogoutResponse`. Он также задает для элемента `InResponseTo` значение, взятое из атрибута `ID` для `LogoutRequest`, который запрашивал ответ.

### <a name="issuer"></a>Издатель
Azure AD устанавливает для него значение `https://login.microsoftonline.com/<TenantIdGUID>/`, где <TenantIdGUID> — это идентификатор клиента Azure AD.

Чтобы оценить значение элемента `Issuer` , используйте значение **URI идентификатора приложения** , указанное при регистрации приложения.

### <a name="status"></a>Status
Azure AD использует элемент `StatusCode`, содержащийся в элементе `Status`, чтобы указать, успешно ли выполнен выход. После неудачной попытки выхода элемент `StatusCode` может также содержать пользовательские сообщения об ошибках.
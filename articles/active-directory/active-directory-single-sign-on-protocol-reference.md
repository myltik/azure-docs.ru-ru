<properties
	pageTitle="Протокол SAML единого входа в Azure | Microsoft Azure"
	description="В этой статье описывается протокол единого входа SAML в Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Протокол единого входа SAML

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

В этой статье рассматриваются запросы и ответы проверки подлинности SAML 2.0, которые поддерживаются Azure Active Directory (Azure AD) для единого входа.

На схеме протокола ниже описана последовательность единого входа. Облачная служба (поставщик услуг) использует привязку перенаправления HTTP для передачи `AuthnRequest` (запроса проверки подлинности) в Azure AD (поставщику удостоверений). Затем Azure AD использует привязку HTTP POST, чтобы опубликовать элемент `Response` в облачной службе.

![Рабочий процесс единого входа](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## AuthnRequest

Чтобы запросить проверку подлинности пользователя, облачные службы отправляют элемент `AuthnRequest` в Azure AD. Пример `AuthnRequest` SAML 2.0 может выглядеть следующим образом:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Параметр | | Описание |
| ----------------------- | ------------------------------- | --------------- |
| ИД | обязательно | Azure AD использует этот атрибут для заполнения атрибута `InResponseTo` возвращенного ответа. Идентификатор не должен начинаться с цифры, поэтому общая стратегия предусматривает добавление такой строки, как id, в начало строкового представления GUID. Например, `id6c1c178c166d486687be4aaf5e482730` — допустимый идентификатор. |
| Версия | обязательно | Должна быть версия **2.0**.|
| IssueInstant | обязательно | Это строка DateTime со значением в формате UTC и [в формате кругового пути ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Для Azure AD нужно значение DateTime этого типа, но служба не оценивает и не использует его. |
| AssertionConsumerServiceUrl | необязательный | Если указан, то он должен соответствовать параметру `RedirectUri` облачной службы в Azure AD. |
| ForceAuthn | необязательный | Если указан, то у него должно быть значение false. Любое другое значение приводит к возникновению ошибки.|
| IsPassive | необязательный | Если указан, то у него должно быть значение false. Любое другое значение приводит к возникновению ошибки. |  

Все остальные атрибуты `AuthnRequest`, такие как Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex и ProviderName, **игнорируются**.

Azure AD также игнорирует элемент `Conditions` в `AuthnRequest`.

### Issuer

Элемент `Issuer` в `AuthnRequest` должен точно соответствовать одному из имен в списке **ServicePrincipalNames** для облачной службы в Azure AD. Обычно здесь передается **URI идентификатора приложения**, указанный во время регистрации приложения.

Фрагмент примера SAML, в котором содержится элемент `Issuer`, выглядит так:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### NameIDPolicy

Этот элемент запрашивает определенный формат идентификатора имени в ответе и является необязательным в элементах `AuthnRequest`, отправленных в Azure AD.

Пример элемента `NameIdPolicy` выглядит таким образом:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Если элемент `NameIDPolicy` указан, можно включить его необязательный атрибут `Format`. У атрибута `Format` может быть только одно из следующих значений, так как любое другое значение приводит к возникновению ошибки:

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` — Azure Active Directory выдает утверждение NameID в качестве парного идентификатора.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` — Azure Active Directory выдает утверждение NameID в формате адреса электронной почты.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` — это значение позволяет Azure Active Directory выбирать формат утверждений. Azure Active Directory выдает NameID в качестве парного идентификатора.

Не включайте атрибут `SPNameQualifer`. Azure AD игнорирует атрибут `AllowCreate`.

### RequestAuthnContext

Элемент `RequestedAuthnContext` указывает нужные методы проверки подлинности. Он необязателен в элементах `AuthnRequest`, отправленных в Azure AD. Azure AD поддерживает только одно значение `AuthnContextClassRef` — `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### Scoping

Элемент `Scoping`, который включает список поставщиков удостоверений, необязателен в элементах `AuthnRequest`, отправленных в Azure AD.

Если он указан, не включайте атрибут `ProxyCount`, а также элемент `IDPListOption` или `RequesterID`, так как они не поддерживаются.

### Подпись

Не включайте элемент `Signature` в элементы `AuthnRequest`, так как Azure AD не поддерживает подписанные запросы проверки подлинности.

### Субъект

Azure AD игнорирует элемент `Subject` элементов `AuthnRequest`.

## Ответ

После успешного выполнения запрошенного входа Azure AD отправляет ответ в облачную службу. Пример ответа на успешную попытку входа выглядит аналогично следующему:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### Ответ

Элемент `Response` включает результат запроса на авторизацию. Azure AD также устанавливает значения `ID`, `Version` и `IssueInstant` для элемента `Response`. Он также задает следующие атрибуты:

- `Destination`. После успешного входа для этого атрибута задается значение `RedirectUri` поставщика услуг (облачной службы).
- `InResponseTo`. Для этого атрибута задается значение `ID` элемента `AuthnRequest`, инициировавшего ответ.

### Issuer

Azure AD устанавливает для элемента `Issuer` значение `https://login.microsoftonline.com/<TenantIDGUID>/`, где <TenantIDGUID> — это идентификатор клиента Azure AD.

Пример ответа с элементом Issuer может выглядеть, к примеру, следующим образом:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### Подпись

Azure AD подписывает элемент `Response` после успешного входа. В элементе `Signature` содержится цифровая подпись, которую может использовать приложение для проверки подлинности источника и проверки целостности ответа.

Azure AD использует ключ подписывания, указанный в элементе `IDPSSODescriptor` документа метаданных. Дополнительные сведения см. в [документе метаданных федерации](active-directory-federation-metadata.md).

Azure AD также подписывает элемент `Assertion`, но эти два элемента подписи не зависят друг от друга.

Пример элемента `Signature` в ответе выглядит следующим образом:

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
```

### Состояние

Элемент `Status` передает сведения об успешном входе или его ошибке. Он включает элемент `StatusCode`, в котором содержится код или набор блоков вложенного кода, представляющего состояние запроса. Он также включает элемент `StatusMessage`, в котором содержатся настраиваемые сообщения об ошибках, создаваемые во время входа.

<!-- TODO: Add a authentication protocol error reference -->

Ниже приведен ответ SAML на неудачную попытку входа.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### Assertion

В дополнение к `ID`, `IssueInstant` и `Version` Azure AD устанавливает следующие элементы в элементе `Assertion` ответа.

#### Issuer

Ему задается значение `https://sts.windows.net/<TenantIDGUID>/`, где <TenantIDGUID> — это идентификатор клиента Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### Подпись

Azure AD подписывает утверждение в ответ на успешный вход. В элементе `Signature` содержится цифровая подпись, которую может использовать облачная служба для проверки подлинности источника, чтобы проверять целостность утверждения.

Чтобы создать цифровую подпись, Azure AD использует ключ подписывания в элементе `IDPSSODescriptor` документа метаданных.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### Субъект

Указывает субъект, который является субъектом операторов в утверждении. В нем содержится элемент `NameID`, который представляет пользователя, прошедшего проверку подлинности. Значение `NameID` — целевой идентификатор, который направляется только поставщику услуг, который является аудиторией маркера. Оно носит постоянный характер. Это значение можно отменить, но нельзя переназначить. Оно также непрозрачно, так как не раскрывает никаких сведений о пользователе и его нельзя использовать в качестве идентификатора для запросов на атрибуты.

Для атрибута `Method` элемента `SubjectConfirmation` всегда задано значение `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### Условия

Этот элемент указывает условия, определяющие приемлемое использование утверждений SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Атрибуты `NotBefore` и `NotOnOrAfter` указывают интервал, в течение которого утверждение допустимо.

- Значение атрибута `NotBefore` равно значению атрибута `IssueInstant` в элементе `Assertion` или немного меньше него (менее чем на секунду). Azure AD не учитывает разницу между своим временем и временем облачной службы (поставщика услуг), а также не добавляет ничего к этому времени.
- Значение атрибута `NotOnOrAfter` на 70 минут меньше, чем значение атрибута `NotBefore`.

#### Аудитория

Он содержит код URI, который определяет предполагаемую аудиторию. Azure AD присваивает этому элементу значение элемента `Issuer` `AuthnRequest`, инициировавшего вход. Чтобы оценить значение `Audience`, используйте значение `App ID URI`, указанное при регистрации приложения.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Как и значение `Issuer`, `Audience` должно точно соответствовать одному из имен субъектов-служб, представляющих облачную службу в Azure AD. Но если значения элемента `Issuer` и URI отличаются, то значение `Audience` в ответе — это `Issuer` с префиксом `spn:`.

#### AttributeStatement

В этом элементе содержатся утверждения о субъекте или пользователе. В следующем фрагменте приведен пример элемента `AttributeStatement`. Многоточие указывает, что в элементе могут содержаться несколько атрибутов и их значений.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```		

- **Утверждение Name**. Значение атрибута `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) — имя субъекта-пользователя, прошедшего проверку подлинности, например `testuser@managedtenant.com`.
- **Утверждение ObjectIdentifier**. Значение атрибута `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) — это `ObjectId` объекта каталога, который представляет пользователя, прошедшего проверку подлинности в Azure AD. Значение `ObjectId` неизменяемое, глобально уникальное и повторно использует безопасный идентификатор пользователя, прошедшего аутентификацию.

#### AuthnStatement

Этот элемент служит утверждением того, что проверка подлинности субъекта утверждения выполнена определенным средством в определенное время.

- Атрибут `AuthnInstant` указывает время проверки подлинности пользователя с помощью Azure AD.
- Элемент `AuthnContext` указывает контекст проверки подлинности, использованный для проверки подлинности пользователя.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```

<!---HONumber=AcomDC_0601_2016-->
<properties 
   pageTitle="Поддерживаемые токены и типы утверждений"
   description="Руководство с общими данными и анализом утверждений в токенах SAML 2.0 и веб-токенах JSON (JWT), выдаваемых службой Azure Active Directory (AAD)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2015"
   ms.author="mbaldwin"/>

# Поддерживаемые токены и типы утверждений

Этот раздел поможет вам лучше разобраться и научиться анализировать утверждения в создаваемых службой Azure Active Directory (Azure AD) токенах SAML 2.0 и JSON Web Tokens (JWT).

Раздел начинается с описания каждого утверждения токена и соответствующих примеров этих утверждений в токене SAML или JWT. Утверждения, которые находятся в состоянии предварительного просмотра, перечислены отдельно. Заканчивается раздел примерами токенов, что позволяет рассмотреть утверждения в контексте.

В Azure утверждения постепенно добавляются в токены, что делает возможным использование новых сценариев. Как правило, эти утверждения представлены в состоянии предварительной версии. После тестового периода мы преобразовываем их и обеспечиваем их полную поддержку. Чтобы подготовиться к изменению утверждений, нужно предусмотреть, чтобы приложения, принимающие токены из Azure AD, игнорировали незнакомые утверждения токенов, в результате чего новые утверждения не будут приводить к сбою приложения. Приложения, использующие утверждения в состоянии предварительной версии, не должны зависеть от этих утверждений и порождать исключения, если в токене отсутствует утверждение. Если приложению требуются утверждения, которые отсутствуют в токенах SAML или JWT, выпускаемых Azure AD, используйте раздел «Дополнения сообщества» внизу этой страницы, чтобы предложить и обсудить новые типы утверждений.

## Справочник по утверждениям токенов

В этом разделе перечислены и описаны утверждения в возвращаемых Azure AD токенах. Здесь приводятся две версии утверждения, SAML и JWT, а также описание утверждений и их использования. Утверждения перечислены в алфавитном порядке.

### Идентификатор приложения

Утверждение Application ID идентифицирует приложение, использующее токен для доступа к ресурсу. Приложение может действовать самостоятельно или от имени пользователя. Идентификатор приложения, как правило, представляет объект приложения, но может также представлять и объект субъекта-службы в Azure AD.

Azure AD не поддерживает утверждение Application ID в токене SAML.

В токене JWT идентификатор приложения отображается в утверждении appid.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Аудитория
Аудитория токена — это предполагаемый получатель токена. Приложение, которое получает токен, должно проверять, верно ли значение, обозначающее аудиторию, и отклонять любые токены, предназначенные для другой аудитории.

Значение, обозначающее аудиторию, является строкой. Как правило, это базовый адрес ресурса, к которому осуществляется доступ, например https://contoso.com. В токенах Azure AD аудитория — это универсальный код ресурса идентификатора приложения для запросившего токен приложения. Если у приложения (являющегося аудиторией) несколько URI идентификатора приложения, URI идентификатора приложения в утверждении Audience токена соответствует URI идентификатора приложения в запросе токена. В токене SAML утверждение Audience определяется в элементе Audience элемента AudienceRestriction.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

В токене JWT аудитория отображается в утверждении aud.

    "aud":"https://contoso.com"

### Application Authentication Context Class Reference

Утверждение Application Authentication Context Class Reference указывает на использованный способ аутентификации клиента. Для общедоступного клиента значение равно 0. При использовании идентификатора и секрета клиента значение равно 1.

В токене JWT значение ссылки на класс контекста аутентификации отображается в утверждении appidacr (ACR-значение для приложения).

    "appidacr": "0"

### Authentication Context Class Reference
Утверждение Authentication Context Class Reference показывает, как проведена аутентификация субъекта, в отличие от клиента в утверждении Application Authentication Context Class Reference. Значение 0 означает, что аутентификация конечного пользователя не соответствовала требованиям ISO/IEC 29115.

- В токене JWT утверждение Authentication Context Class Reference отображается в утверждении acr (ACR-значение для пользователя).

    "acr": "0"

### Authentication Instant

Утверждение Authentication Instant фиксирует дату и время аутентификации.

В токене SAML момент аутентификации отображается в атрибуте AuthnInstant элемента AuthnStatement. Он представляет дату и время в формате UTC (Z).

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

В Azure AD отсутствует эквивалентное утверждение в токенах JWT.

### Authentication Method

Утверждение Authentication Method указывает на то, как аутентифицирован субъект токена. В этом примере для аутентификации пользователя поставщик удостоверений использовал пароль. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

В токене SAML значение метода аутентификации отображается в элементе AuthnContextClassRef.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

В токене JWT значение метода аутентификации отображается в утверждении amr.

    “amr”: ["pwd"]

###Имя

Утверждение First Name или given name указывает на имя или заданное имя пользователя, которое задали в объекте пользователя Azure AD.

В токене SAML имя (или заданное имя) отображается в утверждении в составе токена SAML givenname элемента Attribute.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

В токене JWT имя отображается в утверждении given_name.

    "given_name": "Frank"

### Группы

Утверждение Groups предоставляет идентификаторы объектов, представляющие членство субъекта в группах. Эти значения уникальны (см. раздел «Object ID»), их можно безопасно использовать для управления доступом, например для принудительной авторизации для доступа к ресурсу. Группы, входящие в утверждение Groups, настраиваются для конкретного приложения с помощью свойства groupMembershipClaims манифеста приложения. Если установлено значение null, исключаются все группы, если значение SecurityGroup — включается только членство в группах безопасности Active Directory, а значение All подразумевает включение как групп безопасности, так и списков рассылки Office 365. В любой конфигурации утверждение Groups представляет членство в транзитивных группах субъекта.

В токене SAML утверждение Groups отображается в атрибуте groups.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

В токене JWT утверждение Groups отображается в утверждении Groups.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Identity Provider

Утверждение Identity Provider фиксирует поставщика удостоверений, который выполнил аутентификацию субъекта токена. Это значение идентично значению утверждения Issuer за исключением случаев, когда учетная запись пользователя и издатель принадлежат разным клиентам.

В токене SAML поставщик удостоверений отображается в утверждении в составе токена SAML identityprovider элемента Attribute.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

В токене JWT поставщик удостоверений отображается в утверждении idp.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

Утверждение IssuedAt указывает время выдачи токена. Оно часто используется для определения времени существования токена. В токене SAML значение IssuedAt отображается в утверждении IssueInstant.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

В токене JWT значение IssuedAt отображается в утверждении iat. Значение выражается в секундах, начиная с 1970-01-010:0:0Z по времени в формате UTC.

    "iat": 1390234181

### Issuer

Утверждение Issuer идентифицирует службу токенов безопасности (STS), которая создает и возвращает токен и клиент каталога Azure AD. В возвращаемых службой Azure AD токенах указан следующий издатель: sts.windows.net. Идентификатор GUID в значении утверждения Issuer — это идентификатор клиента каталога Azure AD. Идентификатор клиента представляет собой неизменяемый и надежный идентификатор каталога.

В токене SAML утверждение Issuer отображается в элементе Issuer.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

В токене JWT издатель отображается в утверждении iss.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Фамилия

Утверждение Last Name указывает фамилию пользователя в соответствии с определением в объекте пользователя Azure AD. В токене SAML утверждение Last Name отображается в утверждении в составе токена SAML surname элемента Attribute.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

В токене JWT фамилия отображается в утверждении family_name.

    "family_name": "Miller"

### Имя

Утверждение Name предоставляет удобное для восприятия значение, которое идентифицирует субъект токена. Это значение не обязательно должно быть уникальным в пределах клиента. Оно предназначено только для отображения. В токене SAML имя отображается в утверждении Name.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

В токене JWT имя отображается в утверждении unique_name.

    "unique_name": "frankm@contoso.com"

### Object ID

Утверждение Object ID содержит уникальный идентификатор объекта в Azure AD. Это значение является неизменным, его невозможно назначить другому объекту или использовать повторно. Следовательно, это значение можно использовать для безопасного проведения проверок авторизации, например, когда токен используется для доступа к ресурсу. Используйте идентификатор объекта для идентификации объекта в запросах, адресованных Azure AD. В токене SAML идентификатор объекта отображается в атрибуте objectidentifier.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

В токене JWT идентификатор объекта отображается в утверждении oid.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### контроллере

Утверждение Roles предоставляет понятные строки, представляющие назначение ролей приложения субъекта в Azure AD, и может использоваться для реализации контроля доступа на основе ролей. Роли приложения определяются на уровне приложения с использованием свойства appRoles манифеста приложения. Свойство value каждой роли приложения представляет значение, которое отображается в утверждении Roles. Роли, включенные в утверждение Roles, представляют все роли приложения, предоставленные субъекту напрямую или косвенно через членство в группе. В токене SAML утверждение Roles отображается в атрибуте roles.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

В токене JWT утверждение Roles отображается в утверждении roles.

    “roles”: ["Admin", … ]

### Область

Утверждение Scope токена обозначает предоставленные клиентскому приложению разрешения на олицетворение. По умолчанию используется разрешение user_impersonation. Владелец защищаемого ресурса может регистрировать дополнительные значения в Azure AD.

В токене JWT область действия токена указана в утверждении scp.

    "scp": "user_impersonation"

### Тема

Subject — это субъект, информацию о котором утверждает токен, например данные о пользователе приложения. Это значение является неизменным, его невозможно назначить другому объекту или использовать повторно. Следовательно, это значение можно использовать для безопасного проведения проверок авторизации, например, когда токен используется для доступа к ресурсу. Так как субъект всегда присутствует в выдаваемых Azure AD токенах, мы советуем использовать это значение в системе авторизации общего назначения.

В токене SAML субъект токена указывается в элементе NameID элемента Subject. NameID — это уникальный идентификатор субъекта без возможности повторного использования, который может обозначать пользователя, приложение или службу.

SubjectConfirmation — не утверждение. Он описывает, как проверяется субъект токена. Значение Bearer указывает, что субъект подтвержден благодаря владению токеном.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

В токене JWT субъект отображается в утверждении sub.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### Tenant ID
Tenant ID — неизменяемый идентификатор без возможности повторного использования, который идентифицирует клиент каталога, выдавший токен. Это значение можно использовать для доступа к ресурсам каталога конкретного клиента в мультитенантном приложении. Например, это значение можно использовать для идентификации клиента при вызове API Graph.

В токене SAML идентификатор клиента отображается в утверждении в составе токена SAML tenantid элемента Attribute.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

В токене JWT идентификатор клиента отображается в утверждении tid.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Token Lifetime
Утверждение Token Lifetime определяет интервал времени, в течение которого действителен токен. Служба, проверяющая токен, должна проверить, не выходит ли текущая дата за пределы периода действия токена. Если выходит, токен должен быть отклонен. Служба может разрешить использовать токен в течение пяти минут после окончания срока его действия, тем самым учитывая возможные различия во времени («Отклонение во времени») между Azure AD и службой.

В токене SAML утверждение Token Lifetime определяется в элементе Conditions с использованием атрибутов NotBefore и NotOnOrAfter.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

В токене JWT время существования определяется утверждениями nbf (не ранее) и exp (срок действия). Значение этих утверждений выражается в секундах, начиная с 1970-01-010:0:0Z по времени в формате UTC. Дополнительную информацию см. в документе RFC 3339.

    "nbf":1363289634,
    "exp":1363293234

### User Principal Name
Утверждение User Principal Name обеспечивает хранение имени субъекта-пользователя.

В токене JWT имя субъекта-пользователя отображается в утверждении upn.

    "upn": frankm@contoso.com

### Версия
Утверждение Version обеспечивает хранение номера версии токена. В токене JWT имя субъекта-пользователя отображается в утверждении ver.

    "ver": "1.0"

## Примеры токенов

В этом разделе приводятся примеры токенов SAML и JWT, которые возвращает служба Azure AD. Они позволяют увидеть утверждения в контексте. Токен SAML

Ниже приведен пример типичного токена SAML.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Токен JWT — олицетворение пользователя

Ниже приведен пример типичного веб-токена JSON (JWT), используемого в веб-потоке олицетворения пользователя. Помимо утверждений токен включает номер версии в утверждениях **ver** и **appidacr**, а также ссылку на класс контекста аутентификации, указывающий на способ аутентификации клиента. Для общедоступного клиента значение равно 0. При использовании идентификатора или секрета клиента значение равно 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##См. также

[Протоколы аутентификации Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
 

<!---HONumber=58_postMigration-->
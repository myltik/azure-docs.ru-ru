<properties
   pageTitle="Метаданные федерации"
   description="В этой статье описываются конечные точки метаданных и объясняется содержимое документа метаданных, который необходимо использовать службам, принимающим токены Azure AD."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# Метаданные федерации
Служба Azure Active Directory (Azure AD) публикует документ метаданных федерации для служб, которые настроены на прием токенов безопасности, выдаваемых службой Azure Active Directory. Формат документа метаданных федерации описан в стандарте [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) (язык федерации веб-служб (WS-Federation) версии 1.2), который является расширением стандарта [Metadata for the OASIS Security Assertion Markup Language (SAML) V2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf) (метаданные для языка разметки утверждений безопасности OASIS (SAML) V2.0).

В этой статье описываются конечные точки метаданных и объясняется содержимое документа, которое должны использовать службы, принимающие токены Azure AD.

##Клиентские и общие конечные точки

Azure AD публикует клиентские и общие конечные точки. Клиентские конечные точки предназначены только для одного конкретного клиента. Клиентские метаданные федерации содержат сведения о конкретном клиенте, в том числе данные о его издателе и конечной точке. Приложения, которые предлагают доступ только к одному клиенту, используют клиентские конечные точки.

Общие конечные точки предоставляют сведения, общие для всех клиентов Azure AD. Эти сведения относятся к клиентам, которые размещены в домене *login.windows.net*, и используются на всех клиентах. Мы рекомендуем использовать общие конечные точки для мультитенантных приложений, так как они не связаны с каким-либо определенным клиентом.

## Конечные точки метаданных федерации

Azure AD публикует метаданные федерации в файле *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*, где значение <TenantDomainName> может быть общим или относиться к конкретному клиенту. К конечным точкам можно обращаться: вы можете перейти на указанный в адресе сайт и просмотреть метаданные федерации для клиента.

Для **клиентских конечных точек** <TenantDomainName> может быть одного из следующих типов:

- зарегистрированное доменное имя клиента Azure AD, например contoso.onmicrosoft.com;

- неизменяемый идентификатор клиента домена, например 72f988bf-86f1-41af-91ab-2d7cd011db45.

Для **общих конечных точек** <TenantDomainName> является **общим**. Это имя указывает, что на login.windows.net размещены только элементы метаданных федерации, которые являются общими для всех клиентов Azure AD.

Примером клиентской конечной точки может быть **https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*. Общая конечная точка выглядит так: **https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*.

## Содержимое метаданных федерации

В следующем разделе приведены сведения, необходимые для служб, которые используют токены, выданные службой Azure AD.

### Атрибут EntityID

Элемент **EntityDescriptor** содержит атрибут **EntityID**. Значение атрибута **EntityID** представляет издателя, то есть службу токенов безопасности, которая выдала токен. Обязательно проверяйте издателя, чтобы знать, кем выдан токен.

В следующих метаданных показан пример клиентского элемента **EntityDescriptor** с элементом **EntityID**.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

**EntityID** в общей конечной точке предоставляет шаблон, который может использоваться для создания значения **EntityID** для конкретного клиента. Замените литерал {tenant} в общей конечной точке значением **TenantID** из токена. Полученное значение будет совпадать со значением издателя токена. Такая стратегия позволяет мультитенантному приложению проверять издателя для заданного клиента.

В следующих метаданных приведен пример общего элемента **EntityID**. В этом элементе {tenant} является литералом, а не заполнителем.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### Сертификаты для подписывания токенов
Когда служба получает токен, выданный клиентом Azure AD, подпись токена нужно проверить с помощью ключа подписывания, опубликованного в документе метаданных федерации.

В метаданных федерации содержатся открытые ключи сертификатов, которые используются клиентами для подписывания токенов. Сертификат в «сыром» виде можно увидеть в элементе **KeyDescriptor**. Сертификат можно использовать для подписывания токена, только если атрибут **use** имеет значение **signing**.

Документ метаданных федерации, опубликованный службой Azure AD, может иметь несколько ключей подписывания, к примеру, когда Azure AD готовится к обновлению сертификата для подписывания. Если документ метаданных федерации содержит более одного сертификата, служба, проверяющая токены, должна поддерживать все сертификаты в документе.

В следующих метаданных приведен пример элемента **KeyDescriptor** с ключом подписывания.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

Элемент **KeyDescriptor** отображается в двух местах в документе метаданных федерации: в разделах WS-Federation и SAML. Сертификаты, опубликованные в обоих разделах, будут одинаковыми.

В разделе WS-Federation средство чтения метаданных WS-Federation будет читать сертификаты из элемента **RoleDescriptor** типа **SecurityTokenServiceType**.

В следующих метаданных приведен пример элемента **RoleDescriptor**.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

В разделе SAML средство чтения метаданных WS-Federation будет читать сертификаты из элемента **IDPSSODescriptor**.

В следующих метаданных приведен пример элемента **IDPSSODescriptor**.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

Форматы клиентских и общих сертификатов не отличаются.

### URL-адрес конечной точки WS-Federation
Метаданные федерации включают URL-адрес, используемый службой Azure AD для единого входа и единого выхода в протоколе WS-Federation. Эта конечная точка отображается в элементе **PassiveRequestorEndpoint**.

В следующих метаданных приведен пример элемента **PassiveRequestorEndpoint** для клиентской конечной точки.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

Для общей конечной точки URL-адрес WS-Federation отображается в конечной точке WS-Federation, как показано в следующем примере.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### URL-адрес конечной точки протокола SAML

Метаданные федерации содержат URL-адрес, используемый службой Azure AD для единого входа и единого выхода в протоколе SAML 2.0. Эти конечные точки отображаются в элементе **IDPSSODescriptor**.

URL-адреса входа и выхода отображаются в элементах **SingleSignOnService** и **SingleLogoutService**. В следующих метаданных приведен пример клиентской конечной точки **PassiveResistorEndpoint**.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

Так же конечные точки для общих конечных точек протокола SAML 2.0 публикуются в общих метаданных федерации, как показано в следующем примере.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## См. также
[Протоколы аутентификации Azure Active Directory](active-directory-authentication-protocols.md)

[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=August15_HO6-->
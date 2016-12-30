---
title: "Взаимная проверка подлинности между службами Azure AD с помощью OAuth 2.0 | Документация Майкрософт"
description: "В этой статье описывается, как использовать HTTP-сообщения для проверки подлинности между службами с помощью потока предоставления учетных данных клиента OAuth2.0."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 3fdd561bba898b0e790f69a82ed731d7fd87d8c8
ms.openlocfilehash: 7cf9fe659a91816e42394c1377232e122fe924a6


---
# <a name="service-to-service-calls-using-client-credentials"></a>Служба обслуживания вызовов с помощью учетных данных клиента
Процесс предоставления учетных данных клиента OAuth 2.0 позволяет веб-службе ( *конфиденциальный клиент*) вместо олицетворения пользователя использовать свои собственные учетные данные для проверки подлинности при вызове другой веб-службы. В этом сценарии клиент обычно является службой среднего уровня, веб-службой, службой управляющей программы или веб-сайтом.

## <a name="client-credentials-grant-flow-diagram"></a>Схема потока предоставления учетных данных клиента
На следующей схеме показано, как работает поток предоставления учетных данных клиента в Azure Active Directory (Azure AD).

![Поток предоставления учетных данных клиента OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Клиентское приложение выполняет проверку подлинности на конечной точке выдачи маркера Azure AD и запрашивает маркер доступа.
2. Конечная точка выдачи маркера Azure AD выдает маркер доступа.
3. Маркер доступа используется для проверки подлинности при обращении к защищенному ресурсу.
4. Данные из защищенного ресурса возвращаются в веб-приложение.

## <a name="register-the-services-in-azure-ad"></a>Регистрация служб в Azure AD
Зарегистрируйте вызывающую службу и службу, принимающую вызов, в Azure Active Directory (Azure AD). Подробные инструкции см. в статье [Интеграция приложений с Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>Запрос маркера доступа
Чтобы запросить маркер доступа, используйте запрос HTTP POST к конечной точке Azure AD конкретного клиента.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Запрос маркера взаимного доступа между службами
Запрос маркера взаимного доступа между службами содержит следующие параметры.

| Параметр |  | Описание |
| --- | --- | --- |
| grant_type |обязательно |Указывает запрашиваемый тип ответа. В потоке предоставления учетных данных клиента этот параметр должен иметь значение **client_credentials**. |
| client_id |обязательно |Указывает идентификатор клиента Azure AD вызывающей веб-службы. Чтобы узнать идентификатор клиента вызывающего приложения, на портале управления Azure щелкните **Active Directory**, выберите каталог и приложение, а затем щелкните **Настройка**. |
| client_secret |обязательно |Введите ключ, зарегистрированный для вызывающей веб-службы в Azure AD. Чтобы создать ключ, на портале управления Azure щелкните **Active Directory**, выберите каталог и приложение, а затем щелкните **Настройка**. |
| resource |обязательно |Введите URI идентификатора приложения принимающей вызов веб-службы. Чтобы узнать URI идентификатора приложения, на портале управления Azure щелкните **Active Directory**, выберите каталог и приложение, а затем щелкните **Настройка**. |

## <a name="example"></a>Пример
Следующий запрос HTTP POST запрашивает маркер доступа для веб-службы https://service.contoso.com/. Параметр `client_id` определяет веб-службу, которая запрашивает маркер доступа.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Ответ маркера взаимного доступа между службами
Если доступ предоставлен, ответ будет содержать JSON-файл OAuth 2.0 со следующими параметрами.

| Параметр | Описание |
| --- | --- |
| access_token |Запрашиваемый маркер доступа. Вызывающая веб-служба может использовать этот маркер для проверки подлинности принимающей веб-службы. |
| access_type |Указывает значение типа токена. Единственный тип, поддерживаемый Azure AD — **носитель**. Дополнительные сведения о маркерах носителей см. в спецификации [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (OAuth2.0 Authorization Framework: использование маркера носителя (RFC 6750)). |
| expires_in |Срок действия маркера доступа (в секундах). |
| expires_on |Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров. |
| resource |URI идентификатора приложения принимающей вызов веб-службы. |

## <a name="example"></a>Пример
В следующем примере показано сообщение о предоставлении доступа в ответ на запрос маркера доступа к веб-службе.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Дополнительные материалы
* [OAuth 2.0 в Azure AD](active-directory-protocols-oauth-code.md)



<!--HONumber=Dec16_HO4-->



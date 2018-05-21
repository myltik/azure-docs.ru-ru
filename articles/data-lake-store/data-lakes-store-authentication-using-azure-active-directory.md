---
title: Аутентификация в Data Lake Store с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как выполнять аутентификацию в Data Lake Store с помощью Azure Active Directory.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 061a44540cb618074764ae02fecb3f43d47fe5dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Аутентификация в Data Lake Store с помощью Azure Active Directory

Azure Data Lake Store использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с Azure Data Lake Store, необходимо решить, как для него выполнять аутентификацию в Azure Active Directory (Azure AD). 

## <a name="authentication-options"></a>Параметры проверки подлинности

* **Аутентификация пользователей**. Для аутентификации в Data Lake Store используются учетные данные пользователя Azure. Приложение, создаваемое для работы с Data Lake Store, запрашивает эти учетные данные пользователя. В результате такой механизм аутентификации является *интерактивным*, и приложение запускается в контексте пользователя, вошедшего в систему. Дополнительные сведения и инструкции приведены в разделе [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Аутентификация между службами**. Используйте этот вариант, если требуется, чтобы приложение проходило аутентификацию в Data Lake Store. В таких случаях можно создать приложение Azure Active Directory (AD) и использовать ключ из приложения Azure AD для аутентификации в Data Lake Store. Такой механизм аутентификации будет *неинтерактивным*. Дополнительные сведения и инструкции приведены в разделе [Service-to-service authentication with Data Lake Store using Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) (Аутентификация между службами при использовании Data Lake Store и Azure Active Directory).

В следующей таблице показано, как механизмы аутентификации пользователей и служб поддерживаются для Data Lake Store. Ниже приведены инструкции к этой таблице:

* символ ✔* означает, что этот вариант аутентификации поддерживается, и можно перейти по ссылке на статью, в которой показано, как его использовать; 
* символ ✔ означает, что этот вариант аутентификации поддерживается; 
* пустые ячейки означают, что этот вариант аутентификации не поддерживается.


|Использование варианта аутентификации для…                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Аутентификация пользователей (без MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(не рекомендуется)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Аутентификация пользователей (с MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Аутентификация между службами (с помощью ключа клиента)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Аутентификация между службами (с помощью сертификата клиента) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Щелкните символ <b>✔\*</b>. Это ссылка.</i><br>
<i>** MFA — многофакторная идентификация (Multi-Factor Authentication).</i>

Дополнительные сведения об использовании Azure Active Directory для аутентификации см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="next-steps"></a>Дополнительная информация

* [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Аутентификация между службами](data-lake-store-service-to-service-authenticate-using-active-directory.md)



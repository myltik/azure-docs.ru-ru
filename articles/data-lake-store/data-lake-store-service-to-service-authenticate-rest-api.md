---
title: Аутентификация между службами в Data Lake Store с помощью Azure Active Directory и REST API | Документация Майкрософт
description: Узнайте, как реализовать аутентификацию между службами в Data Lake Store с помощью Azure Active Directory и REST API.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ffa9b7408475820735e35a82edc0b1751abeb08a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624855"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Аутентификация между службами в Data Lake Store с помощью REST API
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

В этой статье описывается, как использовать REST API для аутентификации между службами с помощью Azure Data Lake Store. См. дополнительные сведения об [аутентификации пользователей с помощью Data Lake Store и REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>предварительным требованиям
* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создайте веб-приложение Azure Active Directory**. Вам нужно выполнить инструкции по [аутентификации между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Взаимодействие между службами
В этом сценарии приложение предоставляет собственные учетные данные для выполнения операций. Для этого необходимо отправить запрос POST, аналогичный следующему: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Выходные данные этого запроса будут содержать маркер авторизации (`access-token` в приведенных ниже выходных данных) для дальнейшей передачи в вызовах REST API. Сохраните маркер аутентификации в текстовый файл. Он понадобится вам при выполнении вызовов REST к Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

В этой статье используется **неинтерактивный** подход. Дополнительные сведения о неинтерактивном подходе (вызовы между службами) см. в [этой статье](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Дополнительная информация
В этой статье описывается, как использовать аутентификацию между службами, чтобы реализовать проверку подлинности с помощью Azure Data Lake Store и REST API. Дополнительные сведения об использовании REST API для работы с Azure Data Lake Store см. в следующих статьях.

* [Начало работы с Azure Data Lake Store с использованием REST API](data-lake-store-get-started-rest-api.md).
* [Операции с данными в Azure Data Lake Store с использованием REST API](data-lake-store-data-operations-rest-api.md).


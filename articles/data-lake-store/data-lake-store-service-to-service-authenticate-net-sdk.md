---
title: "Аутентификация между службами в Data Lake Store с помощью Azure Active Directory и пакета SDK для .NET | Документация Майкрософт"
description: "Узнайте, как реализовать аутентификацию между службами в Data Lake Store с помощью Azure Active Directory и пакета SDK для .NET"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: 72e9e2e10992d928dcfd85538497ba12c6e1c6f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Аутентификация между службами в Data Lake Store с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

В этой статье описывается, как использовать пакет SDK для .NET для аутентификации между службами с помощью Azure Data Lake Store. Дополнительные сведения об аутентификации пользователей с помощью Data Lake Store и пакета SDK для .NET см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Аутентификация пользователя с помощью Data Lake Store и .NET SDK).


## <a name="prerequisites"></a>Предварительные требования
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создайте веб-приложение Azure Active Directory**. Вам нужно выполнить шаги по реализации [аутентификации между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Создание приложения .NET
1. Откройте Visual Studio и создайте консольное приложение.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. В окне **Новый проект**введите или выберите следующие значения.

   | Свойство | Значение |
   | --- | --- |
   | Категория |Templates/Visual C#/Windows |
   | Шаблон |Консольное приложение |
   | Имя |CreateADLApplication |
4. Нажмите кнопку **ОК** , чтобы создать проект.

5. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **диспетчера пакетов NuGet** в качестве **источника пакета** выберите **nuget.org** и установите флажок **включения предварительных выпусков**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.Management.DataLake.Store`. В этом руководстве используется предварительная версия 2.1.3.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`. В этом руководстве используется версия 2.2.12.

        ![Добавление источника NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Создание учетной записи Azure Data Lake")
   4. Закройте **диспетчер пакетов NuGet**.

6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;
        
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="service-to-service-authentication-with-client-secret"></a>Аутентификация между службами с помощью секрета клиента
Добавьте следующий фрагмент в клиентское приложение .NET. Замените значения заполнителей значениями, полученными в веб-приложении Azure AD (перечислено как предварительное условие).  Этот фрагмент кода позволяет выполнять аутентификацию приложения **не в интерактивном режиме** с помощью Data Lake Store и ключа или секрета клиента для веб-приложения Azure AD. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientSecret = "<AAD-application-client-secret>";
        var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
        var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;
    }

## <a name="service-to-service-authentication-with-certificate"></a>Аутентификация между службами с помощью сертификата

Добавьте следующий фрагмент кода в клиентское приложение .NET. Замените значения заполнителей значениями, полученными в веб-приложении Azure AD (перечислено как предварительное условие). Этот фрагмент кода позволяет выполнять аутентификацию приложения **не в интерактивном режиме** с помощью Data Lake Store и сертификата для веб-приложения Azure AD. Инструкции по созданию приложения Azure AD см. в руководстве по [созданию субъекта-служба с помощью сертификатов](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientCert = <AAD-application-client-certificate>
        var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
        var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;
    }


## <a name="next-steps"></a>Дальнейшие действия
В этой статье описывается, как использовать аутентификацию между службами, чтобы реализовать аутентификацию с помощью Azure Data Lake Store и пакета SDK для .NET. Дополнительные сведения об использовании пакета SDK для .NET для работы с Azure Data Lake Store см. в следующих статьях.

* [Начало работы с Azure Data Lake Store с помощью пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Операции с данными в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-data-operations-net-sdk.md)



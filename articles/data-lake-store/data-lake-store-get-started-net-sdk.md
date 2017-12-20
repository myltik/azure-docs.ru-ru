---
title: "Пакет SDK для .NET. Операции управления учетными записями в Azure Data Lake Store | Документация Майкрософт"
description: "Используйте пакет SDK для .NET, чтобы выполнять операции управления учетными записями в Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2017
ms.author: nitinme
ms.openlocfilehash: 018cf772f458bb29712ef9516c3af4e969a94564
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Операции управления учетными записями в Azure Data Lake Store с использованием пакета SDK для .NET
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

В этой статье содержатся сведения о выполнении операций управления учетными записями в Data Lake Store с использованием пакета SDK для .NET. К операциям управления учетными записями относятся создание учетной записи Data Lake Store, перечисление учетных записей в подписке Azure, удаление учетных записей и т. д.

Дополнительные сведения о том, как выполнять операции управления данными в Data Lake Store с помощью Python, см. в статье [Filesystem operations on Azure Data Lake Store using .NET SDK](data-lake-store-data-operations-net-sdk.md) (Операции файловой системы в Azure Data Lake Store с использованием пакета SDK для .NET).

## <a name="prerequisites"></a>Предварительные требования
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

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
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.Management.DataLake.Store`. В этом руководстве используется предварительная версия 2.1.3.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`. В этом руководстве используется версия 2.2.12.

        ![Добавление источника NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Создание учетной записи Azure Data Lake")
   4. Закройте **диспетчер пакетов NuGet**.
6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Объявите переменные и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

В остальных разделах статьи рассматривается использование доступных методов .NET при выполнении таких операций, как проверка подлинности, отправка файла и т. д.

## <a name="authentication"></a>Аутентификация

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Аутентификация пользователей в Data Lake Store с использованием пакета SDK для .NET).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Аутентификация между службами в Data Lake Store с использованием пакета SDK для .NET).

## <a name="create-client-object"></a>Создание клиентского объекта
В следующем фрагменте кода создается объект клиента учетной записи Data Lake Store, который используется для выдачи запросов управления учетной записью для службы, таких как создание учетной записи, удаление учетной записи и т. д.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Создание учетной записи хранения озера данных
В следующем фрагменте кода создается учетная запись Data Lake Store в подписке Azure, предоставленной при создании объекта клиента учетной записи Data Lake Store.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Получение списка всех учетных записей Data Lake Store в рамках подписки
Добавьте в определение класса следующий метод. С помощью следующего фрагмента можно получить список всех учетных записей Data Lake Store в рамках определенной подписки Azure.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Удаление учетной записи хранения озера данных
Следующий фрагмент кода удаляет учетную запись Data Lake Store, созданную ранее.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>См. также
* [Операции файловой системы в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Store .NET Reference (Справочник по пакету SDK .NET для Data Lake Store)](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)

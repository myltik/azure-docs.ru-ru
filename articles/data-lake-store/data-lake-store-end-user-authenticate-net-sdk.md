---
title: "Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory и пакета SDK для .NET | Документация Майкрософт"
description: "Узнайте, как реализовать аутентификацию пользователей в Data Lake Store с помощью Azure Active Directory и пакета SDK для .NET"
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
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: a2a9754b462e39713e6580baab84c064237306a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Аутентификация пользователей в Data Lake Store с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Использование пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-end-user-authenticate-python.md)
> * [Использование REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

В этой статье описывается, как использовать пакет SDK для .NET для аутентификации пользователей с помощью Azure Data Lake Store. См. дополнительные сведения [аутентификации между службами с помощью Data Lake Store и пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Предварительные требования
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создание собственного приложения Active Directory** Вам нужно выполнить шаги по реализации [аутентификации пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

## <a name="end-user-authentication"></a>Аутентификация пользователей
Добавьте следующий фрагмент кода в клиентское приложение .NET. Замените значения заполнителей значениями, полученными в собственном приложении Azure AD (перечислено как предварительное условие). Этот фрагмент кода позволяет выполнять аутентификацию приложения **интерактивно** с помощью Data Lake Store. Это значит, что вам будет предложено ввести учетные данные Azure.

Для удобства использования в следующем фрагменте кода для идентификатора клиента и URI перенаправления используются значения по умолчанию, которые действительны для любой подписки Azure. В следующем фрагменте кода нужно указать только значение для идентификатора клиента. См. инструкции по [получению идентификатор клиента](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
    private static void Main(string[] args)
    {
        // User login via interactive popup
        // Use the client ID of an existing AAD native application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
        var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
        var nativeClientApp_applicationId = "1950a258-227b-4e31-a9cf-717495945fc2";
        var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_applicationId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
        var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;
    }

Важные сведения о предыдущем фрагменте кода:

* Для быстрого завершения работы с руководством в этом фрагменте кода используется домен Azure AD и идентификатор клиента, доступный по умолчанию для всех подписок Azure. Таким образом, вы можете **использовать в приложении этот фрагмент в исходном виде**.
* Если вы не хотите использовать свой домен Azure AD и идентификатор клиента приложения, необходимо создать собственное приложение Azure AD и использовать идентификатор клиента Azure AD, идентификатор клиента и URI перенаправления этого приложения. Инструкции см. в статье [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

  
## <a name="next-steps"></a>Дальнейшие действия
В этой статье описывается, как использовать аутентификацию пользователей, чтобы реализовать аутентификацию с помощью Azure Data Lake Store и пакета SDK для .NET. Дополнительные сведения об использовании пакета SDK для .NET для работы с Azure Data Lake Store см. в следующих статьях.

* [Начало работы с Azure Data Lake Store с помощью пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Операции с данными в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-data-operations-net-sdk.md)


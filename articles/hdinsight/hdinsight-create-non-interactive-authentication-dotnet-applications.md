---
title: Создание приложений .NET с неинтерактивной проверкой подлинности в Azure HDInsight | Документация Майкрософт
description: Узнайте, как создавать приложения Microsoft .NET с неинтерактивной проверкой подлинности в Azure HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 497c67132b6d5ebc1301f56fde72031ca3e7b976
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199706"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Создание приложения .NET HDInsight с неинтерактивной проверкой подлинности
Приложение Microsoft .NET Azure HDInsight можно запустить с помощью его собственного удостоверения (неинтерактивный режим) или удостоверения пользователя, вошедшего в приложение (интерактивный режим). В этой статье описано, как создать приложение .NET с неинтерактивной проверкой подлинности для подключения к Azure и управления HDInsight. Пример интерактивного приложения см. в разделе [Подключение к Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Из неинтерактивного приложения .NET, вам потребуется следующее:

* Идентификатор клиента для подписки Azure (также называется *идентификатором каталога*). Дополнительные сведения см. в разделе [Получение идентификатора клиента](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Идентификатор клиента для приложения Azure Active Directory (Azure AD). Дополнительные сведения см. в разделах [Создание приложения Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) и [Получение идентификатора приложения и ключа проверки подлинности](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Секретный ключ приложения Azure AD. Дополнительные сведения см. в разделе [Получение идентификатора приложения и ключа проверки подлинности](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>предварительным требованиям
* Кластер HDInsight. Дополнительные сведения см. в [руководстве по началу работы](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Назначение роли приложению Azure AD
Назначьте приложению Azure AD определенную [роль](../role-based-access-control/built-in-roles.md), чтобы предоставить ему разрешения на выполнение действий. Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать данные группы ресурсов и всех содержащихся в ней ресурсов. В этом руководстве показано, как задать область действия на уровне группы ресурсов. Дополнительные сведения см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md).

**Добавление роли владельца в приложение Azure AD**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева выберите **Группа ресурсов**.
3. Выберите группу ресурсов с кластером HDInsight, в котором позднее будет выполняться ваш запрос Hive. Если у вас много групп ресурсов, для поиска нужной можно использовать фильтр.
4. В меню группы ресурсов щелкните **Управление доступом (IAM)**.
5. В разделе **Пользователи** выберите **Добавить**.
6. Выполните инструкции по добавлению роли владельца в приложение Azure AD. После успешного добавления роли приложение отобразится в списке **Пользователи** с ролью владельца. 

## <a name="develop-an-hdinsight-client-application"></a>Разработка клиентского приложения HDInsight

1. Создайте консольное приложение на C#.
2. Добавьте следующие пакеты NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Выполните следующий код:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Дополнительная информация
* [Создание приложения Azure Active Directory и субъекта-службы на портале Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Узнайте, как [выполнять проверку подлинности субъекта-службы в Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Узнайте, как [управлять доступом на основе ролей (RBAC) в Azure](../role-based-access-control/role-assignments-portal.md).

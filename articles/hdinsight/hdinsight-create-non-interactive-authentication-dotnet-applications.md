---
title: "Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности | Документация Майкрософт"
description: "Узнайте, как создавать приложения .NET HDInsight с неинтерактивной проверкой подлинности."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a423975e8a091183154217678706817694f3e346
ms.openlocfilehash: d5256250d6d3a6d7df3a90ae4a0801af131b830e
ms.lasthandoff: 02/11/2017


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности
Приложение .NET Azure HDInsight можно запустить с помощью его собственного удостоверения (неинтерактивный режим) или удостоверения пользователя, вошедшего в приложение (интерактивный режим). Пример интерактивного приложения см. в разделе [Подключение к Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). В этой статье описано, как создать приложение .NET с неинтерактивной аутентификацией для подключения к Azure и управления HDInsight.

Из неинтерактивного приложения .NET, вам потребуется следующее:

* Идентификатор клиента для подписки Azure (то есть идентификатор каталога). Дополнительные сведения см. в разделе [Получение идентификатора клиента](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Идентификатор клиента для приложения Azure Directory. Дополнительные сведения см. в разделах [Создание приложения Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-active-directory-application) и [Получение идентификатора приложения](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Секретный ключ приложения Azure Directory. Дополнительные сведения см. в разделе [Получение идентификатора приложения и ключа аутентификации](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Предварительные требования
* Кластер HDInsight. Дополнительные сведения см. в [руководстве по началу работы](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).



## <a name="assign-ad-application-to-role"></a>Назначение приложения AD роли
Необходимо назначить приложение [роли](../active-directory/role-based-access-built-in-roles.md) , чтобы предоставить ему разрешения для выполнения действий. Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуются на более низких уровнях области действия (например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы). В этом учебнике описано, как задать область действия на уровне группы ресурсов. Дополнительные сведения см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md).

**Добавление роли владельца для приложения AD**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Группа ресурсов** на левой панели.
3. Выберите группу ресурсов с кластером HDInsight, где позднее будет выполняться ваш запрос Hive. Если имеется слишком много групп ресурсов, можно использовать фильтр.
4. В меню группы ресурсов щелкните **Управление доступом (IAM)**.
5. В колонке **Пользователи** щелкните **Добавить**.
6. Следуйте инструкциям, чтобы добавить роль **Владелец** в приложение AD, созданное в предыдущей процедуре. После успешного выполнения этой процедуры приложение должно появиться в колонке "Пользователи" с ролью "Владелец".

## <a name="develop-hdinsight-client-application"></a>Разработка клиентского приложения HDInsight

1. Создайте консольное приложение на C#.
2. Добавьте следующие пакеты NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Используйте следующий пример кода:

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
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
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

                /// Get the access token for a service principal and provided key                
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

## <a name="next-steps"></a>Дальнейшие действия
* [Создание приложения Active Directory и субъекта-службы с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Проверка подлинности субъекта-службы в Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Управление доступом на основе ролей в Azure](../active-directory/role-based-access-control-configure.md)


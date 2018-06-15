---
title: Управление ресурсами учетной записи пакетной службы с помощью клиентской библиотеки для .NET в Azure | Документация Майкрософт
description: Создание, удаление и изменение ресурсов учетных записей пакетной службы Azure с помощью библиотеки .NET для управления пакетной службой.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2d0b261a991dc02b670488c14392697ea984ec
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316959"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Управление учетными записями и квотами пакетной службы с помощью клиентской библиотеки .NET для управления пакетной службой

> [!div class="op_single_selector"]
> * [портал Azure](batch-account-create-portal.md)
> * [Библиотека .NET для управления пакетной службой](batch-management-dotnet.md)
> 
> 

С помощью [библиотеки .NET для управления пакетной службой][api_mgmt_net] можно снизить издержки на обслуживание приложений пакетной службы Azure. Эта библиотека позволяет автоматизировать создание и удаление учетных записей пакетной службы, управление ключами и определение квот.

* **Создание и удаление учетных записей пакетной службы** в любом регионе. Например, вы являетесь независимым поставщиком программного обеспечения и оказываете услуги клиентам, каждому из которых назначена соответствующая учетная запись пакетной службы для выставления счетов. Для повышения удобства вы можете добавить на портал для пользователей возможность создания и удаления учетных записей.
* **Получение и повторное создание ключей учетных записей** для всех учетных записей пакетной службы программным образом. Это особенно удобно для обеспечения соответствия политикам безопасности, которые могут требовать периодической смены ключей и определять сроки действия ключей учетных записей. Если у вас есть несколько учетных записей пакетной службы в разных регионах Azure, автоматизация смены ключей повысит эффективность вашего решения.
* **Проверка квот учетных записей** и исключение метода проб и ошибок из процедуры определения ограничений учетных записей пакетной службы. Проверка квот учетной записи до запуска заданий, создание пулов или добавление вычислительных узлов позволит вам заранее выбирать время и место создания вычислительных ресурсов. Вы можете определить учетные записи, требующие повышения квот, прежде чем выделить дополнительные ресурсы в этих учетных записях.
* **Объедините функции других служб Azure** для создания полнофункционального приложения для управления, в котором одновременно используются библиотека .NET для управления пакетной службой, [Azure Active Directory][aad_about] и [Azure Resource Manager][resman_overview]. С помощью этих функций и соответствующих API вы можете предоставлять клиентам удобные возможности проверки подлинности, создания и удаления групп ресурсов, а также доступа к описанным выше функциям.

> [!NOTE]
> Несмотря на то, что в этой статье акцент сделан на программное управление учетными записями пакетной службы, ключами и квотами, большую часть описываемых действий можно выполнить на [портале Azure][azure_portal]. Дополнительные сведения см. в статьях [Создание учетной записи пакетной службы Azure на портале Azure](batch-account-create-portal.md) и [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Создание и удаление учетных записей пакетной службы
Как упоминалось выше, одной из основных функций API управления пакетной службой является возможность создания и удаления учетных записей пакетной службы в определенном регионе Azure. Для этого используйте методы [BatchManagementClient.Account.CreateAsync][net_create] и [DeleteAsync][net_delete], а также их синхронные аналоги.

В следующем фрагменте кода создается учетная запись, выполняется получение созданной учетной записи из пакетной службы, а затем она удаляется. В этом и других фрагментах кода, приведенных в этой статье, `batchManagementClient` представляет собой полностью инициализированный экземпляр [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Приложениям, использующим библиотеку .NET для управления пакетной службой и класс BatchManagementClient, необходимы права **администратора службы** или **соадминистратора** для доступа к подписке, которой принадлежит управляемая учетная запись пакетной службы. Дополнительные сведения можно получить, ознакомившись с разделом [Azure Active Directory](#azure-active-directory) ниже и примером кода [AccountManagement][acct_mgmt_sample].
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Получение и повторное создание ключей учетных записей
Получите первичный и вторичный ключи из любой учетной записи пакетной службы в своей подписке с помощью метода [ListKeysAsync][net_list_keys]. Для повторного создания этих ключей используется метод [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Вы можете упростить процедуру подключения в своем приложении для управления. Сначала получите ключ учетной записи пакетной службы, которой вы хотите управлять, с помощью метода [ListKeysAsync][net_list_keys]. Затем используйте этот ключ при инициализации класса [BatchSharedKeyCredentials][net_sharedkeycred] библиотеки .NET пакетной службы, который применяется при инициализации [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Проверка подписки Azure и квот учетной записи пакетной службы
Подписки Azure и отдельные службы Azure, такие как пакетная служба, имеют стандартные квоты для ограничения количества определенных в них сущностей. Квоты по умолчанию для подписок Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md). Квоты пакетной службы по умолчанию см. в статье [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md). С помощью библиотеки .NET для управления пакетной службой можно проверять эти квоты в приложениях. Это позволяет принимать решения о выделении ресурсов перед добавлением учетных записей или вычислительных ресурсов, таких как пулы и вычислительные узлы.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Определение квот для учетной записи пакетной службы в подписке Azure
Прежде чем создавать учетную запись пакетной службы в определенном регионе, вы можете проверить данные подписки Azure, чтобы узнать о возможности создания учетной записи в этом регионе.

В следующем фрагменте кода мы сначала используем метод [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts], чтобы получить коллекцию всех учетных записей пакетной службы в подписке. После получения этой коллекции мы определяем количество учетных записей в целевом регионе. Затем мы используем метод [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] для получения квоты учетной записи пакетной службы и определения количества учетных записей, которые могут создаваться в этом регионе (если таковые имеются).

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

В приведенном выше фрагменте `creds` является экземпляром [TokenCloudCredentials][azure_tokencreds]. Пример создания этого объекта см. в примере [AccountManagement][acct_mgmt_sample] на GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Определение квоты вычислительных ресурсов для учетной записи пакетной службы
Прежде чем увеличивать количество вычислительных ресурсов в решении пакетной службы, убедитесь, что выделяемые ресурсы не превысят квоты для учетной записи. В следующем фрагменте кода мы выводим сведения о квотах для учетной записи пакетной службы с именем `mybatchaccount`. В своем приложении с помощью этих сведений можно определить, способна ли учетная запись обрабатывать дополнительные ресурсы, которые вы хотите создать.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Несмотря на наличие стандартных квот для подписок и служб Azure, многие из ограничений можно увеличить путем создания соответствующего запроса на [портале Azure][azure_portal]. В качестве примера обратитесь к инструкциям по увеличению квот для учетной записи пакетной службы в статье [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md) .
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Использование Azure AD с библиотекой .NET для управления пакетной службой

Данная библиотека является клиентом поставщика ресурсов Azure и используется совместно с [Azure Resource Manager][resman_overview] для программного управления ресурсами учетных записей. Служба Azure AD необходима для аутентификации запросов, выполненных с помощью любого клиента поставщика ресурсов Azure, а также библиотеки .NET для управления пакетной службой и [Azure Resource Manager][resman_overview]. Сведения об использовании Azure AD с библиотекой .NET для управления пакетной службой см. в разделе [Authenticate from Batch solutions with Active Directory](batch-aad-auth.md) (Аутентификация решений пакетной службы в Active Directory). 

## <a name="sample-project-on-github"></a>Пример проекта на сайте GitHub

Работу библиотеки .NET для управления пакетной службой можно посмотреть на примере проекта [AccountManagment][acct_mgmt_sample] на сайте GitHub. В примере приложения AccountManagment демонстрируются следующие операции.

1. Получение маркера безопасности из Active Directory Azure с помощью библиотеки [ADAL][aad_adal]. Если пользователь не выполнил вход, ему будет предложено ввести учетные данные Azure.
2. Создание [SubscriptionClient][resman_subclient] для отправки запроса к Azure на получение списка подписок, связанных с учетной записью, с помощью маркера безопасности, полученного из Azure AD. Пользователь может выбрать подписку из списка, если он содержит более одной подписки.
3. Получение учетных данных, связанных с выбранной подпиской.
4. Создание объекта [ResourceManagementClient][resman_client] с использованием учетных данных.
5. Создание группы ресурсов с помощью объекта [ResourceManagementClient][resman_client].
6. Выполнение нескольких операций с учетной записью пакетной службы с помощью объекта [BatchManagementClient][net_mgmt_client].
   * Создание учетной записи пакетной службы в новой группе ресурсов.
   * Получение созданной учетной записи из пакетной службы.
   * Вывод ключей учетной записи для новой учетной записи.
   * Повторное создание первичного ключа для учетной записи.
   * Вывод сведений о квотах для учетной записи.
   * Вывод сведений о квотах для подписки.
   * Вывод всех учетных записей в подписке.
   * Удаление созданной учетной записи.
7. Удалите ее.

Перед удалением новой учетной записи пакетной службы или группы ресурсов вы можете просмотреть их на [портале Azure][azure_portal].

Для успешного выполнения примера приложения необходимо зарегистрировать его в клиенте Azure AD на портале Azure и предоставить разрешения для API Azure Resource Manager. Выполните инструкции, описанные в статье [Аутентификация решений по управлению пакетной службой с помощью Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "Что такое Microsoft Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Сценарии аутентификации в Azure Active Directory"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Интеграция приложений с Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

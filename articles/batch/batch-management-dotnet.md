<properties
	pageTitle="Управление учетными записями с помощью библиотеки .NET для управления пакетной службой | Microsoft Azure"
	description="Создание, удаление и изменение учетных записей пакетной службы Azure в приложениях с помощью библиотеки .NET для управления пакетной службой."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="11/10/2015"
	ms.author="v-marsma"/>

# Управление квотами и учетными записями пакетной службы Azure с помощью библиотеки .NET для управления пакетной службой

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Вы можете снизить издержки на обслуживание своих приложений пакетной службы Azure с помощью [библиотеки .NET для управления пакетной службой][api_mgmt_net]. Это решение позволяет автоматизировать процессы создания и удаления учетных записей пакетной службы, управления ключами и определения квот.

- **Создание и удаление учетных записей пакетной службы** в любом регионе. Например, вы являетесь независимым поставщиком программного обеспечения и оказываете услуги клиентам, каждому из которых назначена соответствующая учетная запись пакетной службы для выставления счетов. Для повышения удобства вы можете добавить на портал для пользователей возможность создания и удаления учетных записей.
- **Получение и повторное создание ключей учетных записей** для всех учетных записей пакетной службы программным образом. Это особенно удобно для обеспечения соответствия политикам безопасности, которые могут требовать периодической замены ключей и определять сроки действия ключей учетных записей. Если у вас есть несколько учетных записей пакетной службы в разных регионах Azure, автоматизация замены ключей повысит эффективность вашего решения.
- **Проверка квот учетных записей** и исключение метода проб и ошибок из процедуры определения ограничений учетных записей пакетной службы. Проверка квот учетной записи до запуска задания, создание пулов или добавление вычислительных узлов позволит вам заранее выбирать время и место создания ресурсов вычисления. Вы можете определить учетные записи, требующие повышения квот, прежде чем выделить дополнительные ресурсы в этих учетных записях.
- **Объединение функций других служб Azure** для создания полнофункционального приложения для управления, в котором одновременно используются библиотека .NET для управления пакетной службой, [Azure Active Directory][aad_about] и [диспетчер ресурсов Azure][resman_overview]. С помощью этих функций и соответствующих API вы можете предоставлять клиентам удобные возможности проверки подлинности, создания и удаления групп ресурсов, а также доступа к описанным выше функциям.

> [AZURE.NOTE]Несмотря на то что в этой статье акцент сделан на программное управление учетными записями пакетной службы, ключами и квотами, большую часть описываемых действий можно выполнить на [портале Azure][azure_portal]. Дополнительные сведения см. в статьях [Создание учетной записи пакетной службы Azure на портале Azure и управление ею](batch-account-create-portal.md) и [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md).

## Создание и удаление учетных записей пакетной службы

Как было сказано выше, одной из основных функций API управления пакетной службой является возможность создания и удаления учетных записей пакетной службы в определенном регионе Azure. Для этого предназначены методы [BatchManagementClient.Accounts.CreateAsync][net_create] и [DeleteAsync][net_delete], а также их синхронные аналоги.

В следующем фрагменте кода создается учетная запись, выполняется получение созданной учетной записи из пакетной службы, а затем она удаляется. В этом и других фрагментах кода, приведенных в этой статье, `batchManagementClient` представляет собой полностью инициализированный экземпляр [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE]Приложения, использующие библиотеку .NET для управления пакетной службой и класс BatchManagementClient, требуют наличия прав **администратора службы** или **соадминистратора** для доступа к подписке, которой принадлежит управляемая учетная запись пакетной службы. Дополнительные сведения см. в разделе [Azure Active Directory](#aad) ниже и образце кода [AccountManagement][acct_mgmt_sample].

## Получение и повторное создание ключей учетной записи

Получить первичный и дополнительный ключ из любой учетной записи пакетной службы в рамках вашей подписки можно с помощью метода [ListKeysAsync][net_list_keys]. Для повторного создания этих ключей используется метод [RegenerateKeyAsync][net_regenerate_keys].

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP]Вы можете упростить процедуру подключения в своем приложении для управления. Сначала с помощью метода [ListKeysAsync][net_list_keys] нужно получить ключ учетной записи пакетной службы, которую нужно изменить. Затем этот ключ используется при инициализации объекта [BatchSharedKeyCredentials][net_sharedkeycred] из библиотеки .NET для пакетной службы, который используется при инициализации [BatchClient][net_batch_client].

## Проверка подписки Azure и квот учетной записи пакетной службы

Подписки Azure и отдельные службы Azure, такие как пакетная служба, имеют стандартные квоты для ограничения количества определенных в них сущностей. Сведения о квотах по умолчанию для подписок Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](./../azure-subscription-service-limits.md), а квоты для пакетной службы см. в статье [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md). Библиотека .NET для управления пакетной службой предоставляет возможность получения сведений об этих квотах из ваших приложений. Следовательно, вы можете принимать решения о размещении ресурсов перед добавлением учетных записей или вычислительных ресурсов, таких как пулы и вычислительные узлы.

### Определение квот для учетной записи пакетной службы в подписке Azure

Прежде чем создавать учетную запись пакетной службы в определенном регионе, вы можете проверить данные подписки Azure, чтобы узнать о возможности создания учетной записи в этом регионе.

В следующем фрагменте кода мы сначала используем метод [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts], чтобы получить коллекцию всех учетных записей пакетной службы в подписке. Получив эту коллекцию, мы определяем количество учетных записей в целевом регионе, а затем используем свойство [BatchManagementClient.Subscriptions][net_mgmt_subscriptions], чтобы получить данные о квоте для учетной записи пакетной службы. На основании полученных данных мы определяем количество учетных записей, которые можно создать в этом регионе (если это вообще возможно).

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

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

### Определение квоты вычислительных ресурсов для учетной записи пакетной службы

Перед увеличением вычислительных ресурсов в решении пакетной службы вы можете убедиться, что выделяемые ресурсы не превысят действующие квоты для учетной записи. В следующем фрагменте кода мы просто выводим сведения о квотах для учетной записи пакетной службы с именем `mybatchaccount`. Однако в реальном приложении вы можете использовать эту информацию, чтобы определить возможность размещения дополнительных ресурсов, которые требуется выделить для учетной записи.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT]Несмотря на наличие стандартных квот для подписок и служб Azure, многие из ограничений можно увеличить путем создания соответствующего запроса на [портале Azure][azure_portal]. Например, инструкции по увеличению квот для учетной записи пакетной службы приведены в статье [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md).

## Библиотека .NET для управления пакетной службой, AAD и диспетчер ресурсов

При работе с библиотекой .NET для управления пакетной службой вам зачастую нужно будет использовать возможности [Azure Active Directory][aad_about] (AAD) и [диспетчера ресурсов Azure][resman_overview]. В рассматриваемом далее примере показано, как можно использовать Azure Active Directory и диспетчер ресурсов в сочетании с библиотекой .NET для управления пакетной службой.

### <a name="aad"></a>Azure Active Directory

Инфраструктура Azure уже использует Active Directory Azure (AAD) для проверки подлинности клиентов, администраторов служб и пользователей организации. В контексте библиотеки .NET для управления пакетной службой вы будете использовать AAD для проверки подлинности администраторов и соадминистраторов подписки, которые будут настраивать отправку запросов в пакетную службу и выполнять операции, описанные в этой статье.

В описываемом далее примере проекта для авторизации пользователей с помощью учетных данных Майкрософт будет использоваться [библиотека проверки подлинности Active Directory][aad_adal] (ADAL). Приложение, получив учетные данные от администратора или соадминистратора службы, сможет отправлять запросы в Azure для получения списка подписок, а также создавать и удалять группы ресурсов и учетных записей пакетной службы.

### Диспетчер ресурсов

Создание учетных записей пакетной службы с помощью библиотеки .NET для управления пакетной службой обычно осуществляется в рамках [группы ресурсов][resman_overview]. Вы можете программно создать группу ресурсов с помощью класса [ResourceManagementClient][resman_client] из библиотеки [.NET для диспетчера ресурсов][resman_api]. Также вы можете добавить учетную запись в существующую группу ресурсов, созданную ранее с помощью [портала Azure][azure_portal].

## <a name="sample"></a>Пример проекта на сайте GitHub

Работу библиотеки .NET для управления пакетной службой можно посмотреть на примере проекта [AccountManagment][acct_mgmt_sample] на сайте GitHub. Это консольное приложение показывает процессы создания и использования объектов [BatchManagementClient][net_mgmt_client] и [ResourceManagementClient][resman_client], а также демонстрирует использование [библиотеки проверки подлинности Active Directory][aad_adal] (ADAL), необходимой обоим клиентам.

> [AZURE.IMPORTANT]Для успешного запуска примера приложения вам потребуется зарегистрировать его в Azure Active Directory с помощью портала управления Azure. Обратитесь к разделу **Добавление приложения** статьи [Интеграция приложений в Azure Active Directory][aad_integrate] и следуйте указаниям, чтобы зарегистрировать приложение в собственной учетной записи.

В примере приложения демонстрируются следующие операции.

1. Получение маркера безопасности из Active Directory Azure с помощью библиотеки [ADAL][aad_adal]. Если пользователь не выполнил вход, ему предлагается ввести учетные данные Azure.
2. Использование маркера безопасности, полученного из AAD, для создания [SubscriptionClient][resman_subclient], с помощью которого создается запрос к Azure для получения списка подписок учетной записи. Затем, если найдено несколько подписок, пользователю предлагается выбрать один вариант.
3. Создание нового объекта учетных данных, связанного с выбранной подпиской.
4. Создание [ResourceManagementClient][resman_client] с помощью новых учетных данных.
5. Использование [ResourceManagementClient][resman_client] для создания новой группы ресурсов.
6. Использование [BatchManagementClient][net_mgmt_client] для выполнения ряда операций с учетной записью пакетной службы:
  - создание новой учетной записи пакетной службы в созданной группе ресурсов;
  - получение созданной учетной записи из пакетной службы;
  - печать ключей новой учетной записи;
  - повторное создание нового первичного ключа для учетной записи;
  - печать сведений о квотах для учетной записи;
  - печать сведений о квотах для подписки;
  - печать всех учетных записей в подписке;
  - удаление созданной учетной записи;
7. удаление группы ресурсов.

Перед удалением новой учетной записи пакетной службы или группы ресурсов вы можете просмотреть оба этих объекта на [портале Azure][azure_portal]\:

![Группа ресурсов и учетная запись пакетной службы на портале Azure][1] <br /> *Новая группа ресурсов и учетная запись пакетной службы на портале Azure*

[aad_about]: ../active-directory/active-directory-whatis.md "Что такое Microsoft Azure Active Directory"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Сценарии проверки подлинности в Azure AD"
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
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png

<!---HONumber=AcomDC_1203_2015-->
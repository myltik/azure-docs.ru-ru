---
title: Настройка полной смены ключей и аудита в хранилище ключей Azure | Документация Майкрософт
description: Узнайте, как настроить смену ключей и отслеживание журналов хранилища ключей.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: stgriffi
ms.openlocfilehash: 01f1f719545b554b22ef79b38f95087341c65e83
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Настройка полной смены ключей и аудита в хранилище ключей
## <a name="introduction"></a>Введение
Созданное хранилище ключей Azure можно использовать для хранения ключей и секретов. В приложении больше не нужно сохранять ключи и секреты. При необходимости их можно получить из хранилища ключей. Это позволяет обновлять и администрировать ключи и секреты, не оказывая влияние на поведение приложения.

>[!IMPORTANT]
> Примеры в этой статье приведены исключительно для демонстрации. Они не предназначены для использования в рабочей среде. 

В этой статье рассматривается пример использования хранилища ключей Azure для хранения секрета (ключа учетной записи для службы хранилища Azure), к которому обращается приложение. Здесь также описан процесс плановой смены этого ключа. И в заключение мы покажем, как выполнять мониторинг журналов аудита для хранилища ключей и настройку оповещений о непредвиденных запросах.

> [!NOTE]
> В этом руководстве не рассматривается первоначальная настройка хранилища ключей. Соответствующие сведения см. в статье [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md). Инструкции по кроссплатформенному интерфейсу командной строки см. в статье [Управление хранилищем ключей с помощью CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Настройка хранилища ключей
Чтобы приложение могло получить секрет из хранилища ключей, секрет нужно создать и передать в хранилище. Для этого запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

```powershell
Connect-AzureRmAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. PowerShell получит все подписки, связанные с этой учетной записью. По умолчанию PowerShell будет использовать первую из них.

Если у вас есть несколько подписок, вы можете указать ту, с помощью которой вы создали хранилище ключей Azure. Чтобы просмотреть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzureRmSubscription
```

Затем укажите подписку, связанную с хранилищем ключей, данные которого будут регистрироваться. Для этого выполните следующую команду:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Так как в этой статье в качестве секрета используется ключ учетной записи хранения, вам необходимо получить его.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Преобразуйте полученный секрет (ключ учетной записи хранения) в защищенную строку, а затем создайте секрет с этим значением в хранилище ключей.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
После этого получите URI секрета, который вы создали. Это значение в дальнейшем позволит получить секрет из хранилища ключей. Выполните следующую команду PowerShell и запишите значение идентификатора, которое используется в качестве URI секрета.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Настройка приложения
Теперь, когда вы сохранили секрет, можно создать код для его получения и использования. Для этого нужно выполнить несколько шагов. Первый и самый важный — зарегистрируйте приложение в Azure Active Directory. Затем предоставьте хранилищу ключей сведения о приложении, от которого оно будет принимать запросы.

> [!NOTE]
> Приложение и хранилище ключей необходимо создать в одном и том же клиенте Azure Active Directory.
>
>

Откройте вкладку "Приложения" в Azure Active Directory.

![Вкладка "Приложения" в Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Щелкните **Добавить**, чтобы добавить приложение в клиент Azure Active Directory.

![Выбор пункта "Добавить"](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Сохраните для параметра "Тип" значение **Веб-приложение и/или веб-API** и введите имя приложения.

![Ввод имени приложения](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Укажите нужные значения в полях **URL-адрес входа** и **URI идентификатора приложения**. Для этого примера можно указать любые значения. Позже их можно изменить.

![Предоставление необходимых URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Когда приложение будет добавлено в Azure Active Directory, откроется страница приложения. Щелкните вкладку **Настройка**, а затем найдите и скопируйте значение **идентификатора клиента**. Запишите это значение, так как оно понадобится для выполнения последующих действий.

Далее создайте ключ для вашего приложения, чтобы оно могло взаимодействовать с Azure Active Directory. Для этого перейдите в раздел **Ключи** на вкладке **Настройка**. Запишите значение созданного ключа приложения Azure Active Directory. Оно понадобится вам на следующем этапе.

![Ключи приложения Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Прежде чем отправлять вызовы из приложения в хранилище ключей, необходимо предоставить сведения о приложении и его разрешениях. Выполните следующую команду, указав имя хранилища и идентификатор клиента для приложения Azure Active Directory, чтобы предоставить приложению разрешение на получение данных (**Get**) из хранилища ключей.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Теперь можно создавать код для вызовов из приложения. Установите в приложении пакеты NuGet, необходимые для взаимодействия с хранилищем ключей Azure и Azure Active Directory. Для этого введите приведенные ниже команды в консоли диспетчера пакетов Visual Studio. На момент написания этой статьи версия самого свежего пакета Active Directory — 3.10.305231913. Узнайте последнюю версию пакета и при необходимости выполните соответствующие обновления.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

В коде приложения создайте класс, который будет использоваться при проверке подлинности в Azure Active Directory. В этом примере ему присвоено имя **Utils**. Добавьте следующую инструкцию using:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Добавьте приведенный ниже метод для получения маркера JWT из Azure Active Directory. Чтобы обеспечить поддержку, перенесите жестко заданные строковые значения в веб-конфигурацию или конфигурацию приложения.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Добавьте необходимый код для обращения к хранилищу ключей и получения значения секрета. Сначала следует добавить следующий оператор using.

```csharp
using Microsoft.Azure.KeyVault;
```

Добавьте метод, который будет обращаться к хранилищу ключей и получать секрет. Добавьте в этот метод URI секрета, сохраненный на предыдущем шаге. Обратите внимание, что мы используем метод **GetToken** из ранее созданного класса **Utils**.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Запустив приложение, вы можете выполнить проверку подлинности в Azure Active Directory и извлечь значение секрета из хранилища ключей Azure.

## <a name="key-rotation-using-azure-automation"></a>Смена ключей с помощью службы автоматизации Azure
Реализовать смену значений секретов в хранилище ключей Azure можно разными способами. Их можно сменить вручную, программным методом с помощью вызовов API или с использованием скрипта службы автоматизации. В примере из этой статьи вы измените ключ доступа к учетной записи службы хранилища Azure с помощью Azure PowerShell и службы автоматизации Azure. Затем с помощью нового ключа вы обновите секрет хранилища ключей.

Чтобы служба автоматизации Azure могла задавать значения секретов в хранилище ключей, получите идентификатор клиента для подключения AzureRunAsConnection, созданный при установке экземпляра службы автоматизации Azure. Это значение можно получить в окне **Активы** в экземпляре службы автоматизации Azure. В этом окне выберите пункт **Подключения**, а затем щелкните субъект-службу **AzureRunAsConnection**. Запишите значение **идентификатора приложения**.

![Идентификатор клиента службы автоматизации Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

В окне **Активы** выберите пункт **Модули**. В окне **Модули** выберите элемент **Коллекция**, а затем найдите и **импортируйте** обновленные версии каждого из следующих модулей.

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> На момент написания этой статьи требовалось обновить только перечисленные выше модули, чтобы запустить следующий скрипт. Если задание по автоматизации завершится сбоем, убедитесь, что вы импортировали все необходимые модули и зависимости.
>
>

Когда вы получите идентификатор приложения для подключения к службе автоматизации Azure, предоставьте приложению право обновлять секреты в хранилище ключей. Для этого нужно выполнить следующую команду PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

В экземпляре службы автоматизации Azure выберите пункт **Модули Runbook**, а затем — **Добавить Runbook**. Выберите **Быстрое создание**. Введите имя модуля Runbook и выберите значение **PowerShell** в качестве типа модуля Runbook. Вы можете также добавить описание (необязательно). Наконец, нажмите кнопку **Создать**.

![Создание модуля Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

В области редактора нового модуля Runbook вставьте следующий сценарий PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

В области редактора выберите **Область тестирования**, чтобы выполнить тестирование сценария. Когда скрипт будет работать без ошибок, выберите параметр **Публикация** и на панели конфигурации для модуля Runbook настройте его расписание.

## <a name="key-vault-auditing-pipeline"></a>Конвейер аудита для хранилища ключей
При настройке хранилища ключей вы можете включить аудит, чтобы собирать журналы запросов на доступ к этому хранилищу. Эти журналы хранятся в назначенной учетной записи службы хранилища Azure. Их можно извлекать, отслеживать и анализировать. В следующем сценарии создается конвейер с помощью журналов аудита для хранилища ключей, функций Azure и Azure Logic Apps. Этот конвейер отправляет сообщения электронной почты, если секреты из хранилища получает приложение, которое не соответствует идентификатору веб-приложения.

Сначала нужно включить ведение журнала в хранилище ключей. Это можно сделать с помощью следующих команд PowerShell (процесс описан в статье [Ведение журнала хранилища ключей Azure](key-vault-logging.md)).

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Включенные журналы аудита начнут сбор данных в назначенную учетную запись хранения. В этих журналах содержатся сведения о том, кто, как и когда осуществлял доступ к хранилищам ключей.

> [!NOTE]
> Регистрируемые в журналах сведения становятся доступны через 10 минут после выполнения операции с хранилищем ключей. Обычно они доступны даже раньше.
>
>

Теперь необходимо [создать очередь служебной шины Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Туда будут помещаться журналы аудита хранилища ключей. Приложение логики принимает сообщения журнала аудита, помещенные в очередь, и обрабатывает их. Выполните следующие действия, чтобы создать служебную шину.

1. Создайте пространство имен для служебной шины (если оно уже создано, перейдите к шагу 2).
2. Перейдите к служебной шине на портале Azure и выберите пространство имен, в котором нужно создать очередь.
3. Последовательно выберите **Создать ресурс**, **Интеграция с предприятием**, **Служебная шина**, а затем введите требуемые сведения.
4. Получите сведения о подключении к служебной шине. Для этого выберите пространство имен и щелкните **Сведения о подключении**. Эти сведения понадобятся в следующем разделе.

Затем [создайте функцию Azure](../azure-functions/functions-create-first-azure-function.md), которая будет опрашивать журналы хранилища ключей в учетной записи хранения и извлекать новые события. Эта функция будет запускаться по расписанию.

Чтобы создать функцию Azure, выберите **Создать ресурс**, выполните поиск в Marketplace по фразе _приложение-функция_ и нажмите кнопку **Создать**. Можно использовать для этого действующий план размещения или создать новый. Кроме того, можно использовать динамическое размещение. Дополнительные сведения о вариантах размещения функции см. в статье [Масштабирование функций Azure](../azure-functions/functions-scale.md).

Перейдите к созданной функции Azure и выберите функцию таймера и C\#. Затем щелкните **Создать эту функцию**.

![Начальная колонка функций Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

На вкладке **Разработка** замените код run.csx приведенным ниже.

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Обязательно замените в коде переменные. Они должны указывать на учетную запись хранения, в которую записываются журналы хранилища ключей, на созданную ранее служебную шину и на определенный путь к журналам хранилища ключей.
>
>

Функция получает из учетной записи хранения, в которою записываются журналы хранилища ключей, последний файл журнала, извлекает из него последние события и передает их в очередь служебной шины. Так как в файле может содержаться несколько событий, создайте файл sync.txt, в котором будут отображаться данные о просматриваемой функцией метке времени для последнего извлеченного события. Это гарантирует, что одно и то же событие не будет отправлено в служебную шину несколько раз. В файле sync.txt содержится метка времени для последнего обработанного события. Чтобы журналы были упорядочены должным образом, при загрузке их необходимо отсортировать по метке времени.

Для поддержки этой возможности мы предоставили несколько дополнительных библиотек, которые недоступны по умолчанию в Функциях Azure. Эти библиотеки необходимо добавить в функции Azure с помощью NuGet. Щелкните **Просмотреть файлы**.

![Элемент "Просмотреть файлы"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Затем добавьте новый файл project.json со следующим содержимым:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Когда вы **сохраните** изменения, функции Azure скачают необходимые двоичные файлы.

Перейдите на вкладку **Интеграция** и введите для параметра "Таймер" понятное имя, которое будет использоваться в функции. В приведенном выше коде ожидается, что таймеру будет присвоено имя *myTimer*. Укажите [выражение CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) для таймера, который инициирует запуск функции каждую минуту, следующим образом: 0 \* \* \* \* \*.

На вкладке **Интеграция** добавьте входной параметр типа **Хранилище BLOB-объектов Azure**. Он указывает на файл sync.txt, содержащий метку времени для последнего события, просмотренного функцией. В коде функции можно получить эту информацию по имени параметра. В приведенном выше коде ожидается, что параметру входного хранилища BLOB-объектов Azure будет присвоено имя *inputBlob*. Выберите учетную запись хранилища, в которой будет находиться файл sync.txt (это может быть та же или другая учетная запись хранения). В соответствующем поле укажите путь размещения файла в формате {имя_контейнера}/путь_к_файлу/sync.txt.

Добавьте выходной параметр типа *Хранилище BLOB-объектов Azure*. Он будет указывать на файл sync.txt, определенный выше в качестве входа. Функция использует выходной параметр, чтобы записать метку времени для последнего просмотренного события. В приведенном выше коде ожидается, что этому параметру будет присвоено имя *outputBlob*.

Теперь функция готова. Перейдите на вкладку **Разработка** и сохраните код. Если в окне вывода появились ошибки компиляции, исправьте их соответствующим образом. Если код скомпилируется успешно, функция сразу начнет проверять журналы хранилища ключей каждую минуту и передавать новые события в указанную очередь служебной шины. При каждом запуске функции в окне журнала будут отображаться данные журнала.

### <a name="azure-logic-app"></a>Приложение логики Azure
Сейчас вам нужно создать приложение логики Azure, которое будет получать события, отправленные функцией в очередь служебной шины, анализировать их содержимое и отправлять сообщения электронной почты при выполнении заданного условия.

Чтобы [создать приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md), последовательно выберите **Создать -> Приложение логики**.

Создав приложение логики, перейдите к нему и выберите команду **Изменить**. Чтобы подключить приложение к очереди, в редакторе приложения логики выберите **Очередь служебной шины** и введите учетные данные служебной шины.

![Служебная шина приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Затем выберите команду **Добавить условие**. Откройте расширенный редактор и введите следующий код, заменив параметр APP_ID реальным значением идентификатора веб-приложения:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Если свойство *appid* из входящего события (текст сообщения служебной шины) не соответствует идентификатору *appid* приложения, выражение вернет значение **false**.

Теперь создайте действие в разделе **If no, do nothing…** (Если нет, ничего не предпринимать).

![Выбор действия приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Для примера создайте действие **Office 365 — отправить электронное письмо**. Заполните поля, чтобы создать электронное письмо, которое будет отправляться, когда определенное условие возвращает значение **false**. Если у вас нет Office 365, можно создать такое же действие для другой службы.

На этом этапе вы создали конвейер, который каждую минуту будет проверять новые записи в журналах аудита для хранилища ключей. Он отправляет обнаруженные записи в очередь служебной шины. Приложение логики запускается каждый раз при появлении в очереди нового сообщения. Если параметр *appid* для нового события не совпадает с идентификатором зарегистрированного приложения, отправляется сообщение электронной почты.

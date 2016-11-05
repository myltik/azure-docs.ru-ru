---
title: Как настроить полную смену ключей и аудит в хранилище ключей | Microsoft Docs
description: Узнайте, как настроить смену ключей и отслеживание журналов хранилища ключей.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: ''
tags: ''

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: jodehavi;stgriffi

---
# Как настроить полную смену ключей и аудит в хранилище ключей
## Введение
После создания хранилище ключей Azure можно использовать для хранения ключей и секретов. В приложении больше не нужно сохранять ключи и секреты. При необходимости их можно получить из хранилища ключей. Это позволяет обновлять ключи и секреты, не влияя при этом на поведение приложения, что, в свою очередь, предоставляет более широкие возможности управления поведением ключей и секретов.

В этой статье рассматривается пример использования хранилища ключей Azure для хранения секрета (в этом примере ключа учетной записи службы хранилища Azure), к которому получает доступ приложение. Здесь также приведены шаги по реализации плановой смены этого ключа. И наконец, здесь представлены сведения о мониторинге журналов аудита хранилища ключей и настройке оповещений о получении непредвиденных запросов.

> [!NOTE]
> В этом руководстве не рассматривается первоначальная настройка хранилища ключей Azure. Соответствующие сведения см. в статье [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md). Инструкции по кроссплатформенному интерфейсу командной строки см. в [этом учебнике](key-vault-manage-with-cli.md).
> 
> 

## Настройка хранилища ключей
Чтобы приложение могло получить секрет из хранилища ключей Azure, секрет сначала необходимо создать и загрузить в хранилище. Это можно с легкостью сделать с помощью PowerShell, как показано ниже.

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:

```powershell
Login-AzureRmAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получит все подписки, связанные с этой учетной записью, и по умолчанию будет использовать первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzureRmSubscription
```

Затем укажите подписку, связанную с хранилищем ключей, данные которого будут регистрироваться. Для этого введите следующую команду:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Так как в этой статье в качестве секрета используется ключ учетной записи хранения, вам необходимо получить его.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Полученный секрет (в этом примере ключ учетной записи хранения) необходимо преобразовать в защищенную строку, а затем нужно создать секрет с этим значением в хранилище ключей.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Теперь требуется получить URI созданного секрета. Это значение понадобится в дальнейшем при получении секрета из хранилища ключей. Выполните следующую команду PowerShell и запишите значение идентификатора, которое используется в качестве URI секрета.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## Настройка приложения
После загрузки секрета в хранилище его можно извлечь и использовать из кода. Для этого потребуется выполнить несколько шагов. Первый и самый важный шаг — зарегистрировать приложение в Azure Active Directory. Затем нужно предоставить хранилищу ключей Azure сведения о приложении, после чего оно сможет принимать запросы от этого приложения.

> [!NOTE]
> Приложение и хранилище ключей необходимо создать в одном и том же клиенте Azure Active Directory.
> 
> 

Перейдите на вкладку "Приложения" в Azure Active Directory.

![Открытие вкладки "Приложения" в Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Щелкните "Добавить", чтобы добавить новое приложение в Azure AD.

![Выбор элемента "Добавить приложение"](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Выберите для параметра "Тип" значение "Веб-приложение и/или веб-API" и введите имя приложения.

![Указание имени приложения](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Укажите значения в полях "URL-адрес входа" и "URI идентификатора приложения". Для этого демонстрационного проекта можно указать любые значения. Позже их можно изменить.

![Предоставление необходимых URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

После добавления приложения в Azure AD откроется страница приложения. Перейдите на вкладку "Настройка", а затем найдите и скопируйте значение идентификатора клиента. Запишите это значение, так как оно понадобится для выполнения последующих действий.

Теперь необходимо создать ключ приложения, требуемый для взаимодействия с Azure AD. Для этого перейдите в раздел "Ключи" на вкладке "Настройка". Запишите значение созданного ключа приложения Azure AD. Оно понадобится вам в дальнейшем.

![Ключи приложения Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Перед отправкой вызовов из приложения в хранилище ключей необходимо предоставить сведения о приложении и его разрешениях. Выполните следующую команду, указав имя хранилища и идентификатор клиента приложения Azure AD, чтобы предоставить приложению разрешение на получение данных из хранилища ключей.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

На этом этапе можно приступить к созданию вызовов из приложения. Для взаимодействия с хранилищем ключей Azure и Azure Active Directory установите в приложении пакеты NuGet. Для этого введите приведенные ниже команды в консоли диспетчера пакетов Visual Studio. Обратите внимание, что на момент написания этой статьи использовался пакет ActiveDirectory версии 3.10.305231913. Узнайте последнюю версию пакета и при необходимости выполните соответствующие обновления.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

В коде приложения создайте класс, который будет использоваться при аутентификации в Active Directory. В этом примере применяется класс Utils. Вам также понадобится добавить следующий оператор using.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Добавьте приведенный ниже метод для получения маркера JWT из Azure AD. Для удобства поддержки переместите в веб-конфигурацию или конфигурацию приложения жестко заданные значения строки.

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

Теперь можно добавить необходимый код для вызова хранилища ключей и получения значения секрета. Сначала необходимо добавить следующий оператор using.

```csharp
using Microsoft.Azure.KeyVault;
```

Затем добавьте метод для обращения к хранилищу ключей и получения секрета. Добавьте в этот метод значение URI-секрета, сохраненное на предыдущем шаге. Обратите внимание на использование метода GetToken из ранее созданного класса Utils.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Теперь после запуска ваше приложение сможет пройти аутентификацию в Azure Active Directory и извлечь значение секрета из хранилища ключей Azure.

## Смена ключей с помощью службы автоматизации Azure
Реализовать смену значений секретов в хранилище ключей Azure можно разными способами. Их можно сменить вручную, программным методом с помощью вызовов API или с использованием сценария автоматизации. В рамках этой статьи мы изменим ключ доступа к учетной записи службы хранилища Azure с помощью Azure PowerShell и службы автоматизации Azure, а затем обновим секрет хранилища ключей.

Чтобы предоставить службе автоматизации Azure возможность задавать значения секретов в хранилище ключей, требуется идентификатор клиента подключения AzureRunAsConnection, созданный при установке экземпляра службы автоматизации Azure. Это значение можно получить в окне "Активы" в экземпляре службы автоматизации Azure. В этом окне выберите пункт "Подключения", а затем щелкните субъект-службу AzureRunAsConnection. Запишите значение идентификатора приложения.

![Идентификатор клиента службы автоматизации Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

В окне "Активы" выберите пункт "Модули". В окне "Модули" выберите элемент "Коллекция", а затем найдите и импортируйте обновленные версии каждого из следующих модулей.

    Azure
    Azure.Storage    
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> На момент написания этой статьи для выполнения приведенного ниже сценария требовалось обновить только перечисленные выше модули. Если задание по автоматизации завершилось сбоем, убедитесь в наличии всех необходимых модулей и их зависимостей.
> 
> 

После получения идентификатора приложения, необходимого для подключения службы автоматизации Azure, приложению следует предоставить права на обновление секретов в хранилище ключей Azure. Для этого нужно выполнить следующую команду PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

В экземпляре службы автоматизации Azure выберите ресурс "Модули Runbook", а затем — "Добавить Runbook". Щелкните "Быстрое создание". Введите имя модуля Runbook и выберите значение PowerShell для параметра "Тип Runbook". При необходимости можно ввести описание. Наконец, нажмите кнопку "Создать".

![Создание модуля Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

В области редактора нового модуля Runbook вставьте следующий сценарий PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

В области редактора выберите "Тестовая область", чтобы выполнить тестирование сценария. Если сценарий запустился без ошибок, выберите параметр "Публикация" и на панели конфигурации настройте расписание для модуля Runbook.

## Конвейер аудита в хранилище ключей
При настройке хранилища ключей Azure можно включить функцию аудита. Это позволяет собирать журналы со сведениями о запросах на доступ к хранилищу ключей. Эти журналы хранятся в назначенной учетной записи службы хранилища Azure. Их можно извлекать, отслеживать и анализировать. Ниже описан сценарий создания конвейера с использованием журналов аудита хранилища ключей, Функций Azure и приложений логики Azure для отправки сообщения электронной почты в случае извлечения секретов приложением, которое не соответствует идентификатору веб-приложения.

Сначала в хранилище ключей необходимо включить ведение журнала. Это можно сделать с помощью следующих команд PowerShell (дополнительные сведения см. [здесь](key-vault-logging.md)).

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

После включения журналы аудита начнут сбор данных в назначенную учетную запись хранения. В этих журналах содержатся сведения о том, кто, как и когда осуществлял доступ к хранилищам ключей.

> [!NOTE]
> Регистрируемые в журналах сведения доступны не позже чем через 10 минут после выполнения операции с хранилищем ключей. В большинстве случаев это будет еще быстрее.
> 
> 

Теперь необходимо [создать очередь служебной шины Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Туда будут помещаться журналы аудита хранилища ключей. Приложение логики извлекает журналы из очереди и обрабатывает их. Процесс создания служебной шины относительно прост. Ниже приведены действия верхнего уровня, необходимые для выполнения этого процесса.

1. Создайте пространство имен служебной шины (если оно уже создано, перейдите к шагу 2).
2. Перейдите к служебной шине на портале и выберите пространство имен, в котором необходимо создать очередь.
3. Щелкните "Создать", последовательно выберите "Служебная шина" -> "Очередь" и введите необходимые сведения.
4. Получите сведения о подключении к служебной шине. Для этого выберите пространство имен и щелкните *Сведения о подключении*. Эти сведения потребуются для выполнения последующих действий.

Затем необходимо [создать функцию Azure](../azure-functions/functions-create-first-azure-function.md), которая будет опрашивать журналы хранилища ключей в учетной записи хранения и извлекать новые события. Эта функция будет запускаться по расписанию.

Создайте функцию Azure (на портале последовательно выберите "Создать" -> "Приложение-функция"). Во время выполнения этого процесса можно использовать действующий план размещения или создать новый. Кроме того, можно использовать динамическое размещение. Дополнительные сведения о вариантах размещения функции см. [здесь](../azure-functions/functions-scale.md).

После создания функции Azure перейдите к ней, выберите значения "Таймер" и C#, а затем на начальном экране нажмите кнопку **Создать**.

![Начальная колонка Функций Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

На вкладке *Разработка* замените код run.csx на приведенный ниже.

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
> Обязательно замените в коде переменные, чтобы они указывали на учетную запись хранения, в которую записываются журналы хранилища ключей, на созданную ранее служебную шину и на путь к журналам хранилища ключей.
> 
> 

Функция получает из учетной записи хранения, в которою записываются журналы хранилища ключей, последний файл журнала, извлекает из него последние события и передает их в очередь служебной шины. Так как в файле может содержаться несколько событий (например, в течение полного часа), необходимо создать файл *sync.txt*, в котором будут отображаться сведения о метке времени последнего извлеченного события. Это гарантирует, что одно и то же событие не будет отправлено в служебную шину несколько раз. В файле *sync.txt* содержатся метки времени возникновения последнего события. Чтобы журналы были упорядочены должным образом, при загрузке их необходимо отсортировать по метке времени.

Для поддержки этой возможности мы предоставили несколько дополнительных библиотек, которые недоступны по умолчанию в Функциях Azure. Эти библиотеки необходимо добавить в Функции Azure с помощью NuGet. Щелкните *Просмотреть файлы*

![Элемент "Просмотреть файлы"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

и добавьте новый файл *project.json* со следующим содержимым:

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
После *применения* изменений Функции Azure скачают необходимые двоичные файлы.

Перейдите на вкладку **Интеграция** и введите для параметра "Таймер" понятное имя, которое будет использоваться в функции. В приведенном выше коде для этого параметра необходимо задать имя *myTimer*. Укажите [выражение CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) для таймера, который инициирует запуск функции каждую минуту, следующим образом: 0 * * * * *.

На вкладке **Интеграция** добавьте входной параметр типа *Хранилище BLOB-объектов Azure*. Он указывает на файл *sync.txt*, содержащий метку времени последнего события, просмотренного функцией. Для поддержки этой возможности в строку функции необходимо добавить имя параметра. В приведенном выше коде для входного параметра хранилища BLOB-объектов Azure необходимо задать имя *inputBlob*. Выберите учетную запись хранения, в которой будет находиться файл *sync.txt* (это может быть та же или другая учетная запись хранения), и укажите в соответствующем поле путь к расположению файла в формате {имя\_контейнера}/path/to/sync.txt.

Добавьте выходной параметр типа *Хранилище BLOB-объектов Azure*. Он будет указывать на файл *sync.txt*, определенный во входном параметре. Функция использует выходной параметр для записи метки времени последнего просмотренного события. В приведенном выше коде для этого параметра необходимо задать имя *outputBlob*.

Теперь функция готова. Перейдите на вкладку **Разработка** и *сохраните* код. Если в окне вывода есть ошибки компиляции, исправьте их соответствующим образом. Если код компилируется, он будет запущен: каждую минуту будут проверяться журналы хранилища ключей,а новые события будут отправляться в определенную очередь служебной шины. При каждом запуске функции в окне журнала будут отображаться регистрируемые сведения.

### Приложение логики Azure
Сейчас нам нужно создать приложение логики Azure, которое отвечает за извлечение событий, отправленных функцией в очередь служебной шины, анализ содержимого и отправку сообщений на основе соответствующего условия.

Чтобы [создать приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md), последовательно выберите "Создать" -> "Приложение логики".

После создания перейдите к нему и выберите команду *Изменить*. Чтобы подключить служебную шину к очереди, в редакторе приложения логики выберите управляемый API *очереди служебной шины* и введите ее учетные данные.

![Служебная шина приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Затем выберите команду *Добавить условие*. Откройте *расширенный редактор* и введете следующую строку, заменив параметр APP\_ID реальным значением идентификатора веб-приложения:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Если свойство **appid** из входящего события (текст сообщения служебной шины) не соответствует **идентификатору** приложения, выражение вернет значение **false**.

Теперь необходимо создать действие в параметре *If no, do nothing…* (Если нет, ничего не предпринимать…).

![Выбор действия приложения логики Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Для примера создайте действие *Office 365 — отправить электронное письмо*. Заполните поля, чтобы создать электронное письмо, которое будет отправляться, когда определенное условие возвращает значение false. Если Office 365 отсутствует, можно создать такое же действие для другой службы.

На этом этапе вы создали конвейер, который каждую минуту будет выполнять поиск новых журналов аудита хранилища ключей. Найденные им журналы отправляются в очередь служебной шины. Приложение логики запускается после поступления нового приложения в очередь. Если идентификатор приложения события не соответствует идентификатору вызывающего приложения, отправляется сообщение электронной почты.

<!---HONumber=AcomDC_0928_2016-->
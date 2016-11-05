
---
title: Создание защищенного кластера Service Fabric с помощью Azure Resource Manager | Microsoft Docs
description: В этой статье описывается настройка защищенного кластера Service Fabric в Azure с помощью Azure Resource Manager, хранилища ключей Azure и Azure Active Directory (AAD) для проверки подлинности клиента.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek

---
# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Создание кластера Service Fabric в Azure с помощью Azure Resource Manager
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов Azure](service-fabric-cluster-creation-via-arm.md)
> * [Портал Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Это пошаговое руководство поможет выполнить настройку защищенного кластера Azure Service Fabric в Azure с помощью Azure Resource Manager. Данное руководство описывает следующие действия:

* Настройка хранилища ключей для управления ключами для обеспечения безопасности кластера и приложений.
* Создание защищенного кластера в Azure с помощью Azure Resource Manager.
* Аутентификация пользователей с помощью Azure Active Directory (AAD) для управления кластером.

Защищенный кластер — это кластер, который предотвращает несанкционированный доступ к операциям управления, включая развертывание, обновление и удаление приложений, служб и данных, которые они содержат. Незащищенный кластер — это кластер, к которому в любое время может подключиться любой пользователь и выполнять операции управления. Хотя можно создать незащищенный кластер, **настоятельно рекомендуется создать защищенный кластер**. Незащищенный кластер **невозможно будет защитить позднее** , для этого нужно будет создать новый кластер.

Принципы создания безопасных кластеров в Linux или Windows аналогичны. Дополнительные сведения о создании безопасных кластеров Linux, а также вспомогательные скрипты см. в разделе [Создание безопасных кластеров в Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Вход в Azure
В этом руководстве используется [Azure PowerShell][azure-powershell]. При запуске нового сеанса PowerShell войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.

Войдите в свою учетную запись Azure.

```powershell
Login-AzureRmAccount
```

Выберите свою подписку.

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Настройка хранилища ключей
В этом разделе описано, как создать хранилище ключей для кластера Service Fabric в Azure и приложений Service Fabric. Полное описание хранилища ключей см. в [руководстве по началу работы с хранилищем ключей][key-vault-get-started].

Service Fabric использует сертификаты X.509 для защиты кластера и обеспечения функций безопасности приложений. Хранилище ключей Azure используется для управления сертификатами кластеров Service Fabric в Azure. При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из хранилища ключей и устанавливает их на виртуальные машины кластера.

На приведенной ниже схеме показана связь между хранилищем ключей, кластером Service Fabric и поставщиком ресурсов Azure, который использует сертификаты из хранилища ключей при создании кластера.

![Установка сертификата][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Первый шаг — создание группы ресурсов специально для хранилища ключей. Мы рекомендуем размещать хранилище ключей в отдельной группе ресурсов. Так вы сможете удалять группы ресурсов для вычислений и хранения, включая группу ресурсов, в которой размещен кластер Service Fabric, без потери ключей и секретов. Группа ресурсов с хранилищем ключей должна находиться в том же регионе, что и кластер, который ее использует.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Создание хранилища ключей
Создайте хранилище ключей в новой группе ресурсов. Хранилище ключей **должно быть доступно для развертывания**. Это позволит поставщику ресурсов Service Fabric получить из него сертификаты и установить их на узлах кластера.

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

При наличии существующего хранилища ключей можно сделать его доступным для развертывания с помощью командной строки Azure (Azure CLI).

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-key-vault"></a>Добавление сертификатов в хранилище ключей
Сертификаты используются в Service Fabric, чтобы обеспечить функции аутентификации и шифрования для защиты различных аспектов кластера и его приложений. Дополнительные сведения о том, как сертификаты используются в Service Fabric, см. в статье [Сценарии защиты кластера Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-(required)"></a>Сертификат кластера и сервера (обязательно)
Этот сертификат требуется для защиты кластера и предотвращения несанкционированного доступа к нему. Он обеспечивает безопасность кластера несколькими способами.

* **Аутентификация в кластере** позволяет аутентифицировать обмен данными между узлами для федерации кластера. Только узлы, которые могут подтвердить свою подлинность с помощью этого сертификата, могут присоединиться к кластеру.
* **Аутентификация сервера** позволяет аутентифицировать конечные точки управления кластера в клиенте управления, чтобы он "знал", что обращается к настоящему кластеру. Этот сертификат также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS.

Для этого сертификат должен отвечать следующим требованиям:

* Сертификат должен содержать закрытый ключ.
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Невозможно получить SSL-сертификат от центра сертификации (ЦС) для домена `.cloudapp.azure.com` . Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

### <a name="application-certificates-(optional)"></a>Сертификаты приложения (необязательно)
В кластере можно установить любое количество дополнительных сертификатов для обеспечения безопасности приложений. Перед созданием кластера рассмотрите сценарии безопасности приложений, которые требуют установки сертификатов на узлах, в том числе:

* шифрование и расшифровка значений конфигурации приложений;
* шифрование данных между узлами во время репликации; 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>форматирование сертификатов для использования поставщиком ресурсов Azure.
Файлы закрытого ключа (PFX-файлы) можно добавить в хранилище ключей и использовать непосредственно из него. Тем не менее поставщику ресурсов Azure необходимо, чтобы ключи хранились в специальном формате JSON, включающем в себя PFX-файл в виде строки в кодировке Base64 и пароль закрытого ключа. Чтобы соблюсти эти требования, ключи должны быть помещены в строку JSON и сохранены как *секреты* в хранилище ключей.

Для упрощения этого процесса [на сайте GitHub][service-fabric-rp-helpers] доступен соответствующий модуль PowerShell. Ниже приведены указания по использованию этого модуля.

1. Скачайте все содержимое репозитория в локальный каталог. 
2. Импортируйте модуль в окне PowerShell.
   
   ```powershell
   PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
   ```

Команда `Invoke-AddCertToKeyVault` в этом модуле PowerShell автоматически форматирует закрытый ключ сертификата в строку JSON и передает ее в хранилище ключей. Используйте его для добавления сертификата кластера и всех дополнительных сертификатов приложения в хранилище ключей. Повторите этот шаг для всех дополнительных сертификатов, которые нужно установить в кластере.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Это все обязательные компоненты хранилища ключей, необходимые для настройки шаблона Resource Manager кластера Service Fabric. Этот шаблон устанавливает сертификаты для проверки подлинности на узлах, для защиты конечных точек управления и проверки подлинности на них, а также для любых дополнительных функций безопасности в приложениях, которые используют сертификаты X.509. На этом этапе в Azure должна быть представлена следующая конфигурация:

* группа ресурсов хранилища ключей;
  * хранилище ключей;
    * сертификат проверки подлинности сервера кластера;
    * сертификаты приложения.

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Настройка Azure Active Directory для проверки подлинности клиента
AAD позволяет организациям (известным как клиенты) управлять доступом пользователей к приложениям, которые делятся на приложения с веб-интерфейсом входа и приложения с собственным интерфейсом входа клиента. В этом документе предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory][active-directory-howto-tenant].

Кластеры Service Fabric предлагают несколько точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer][service-fabric-visualizing-your-cluster] и [Visual Studio][service-fabric-manage-application-in-visual-studio]. В итоге вы получаете два приложения AAD для управления доступом к кластеру: одно веб-приложение и одно собственное приложение.

Чтобы упростить некоторые шаги по настройке AAD с кластером Service Fabric, мы создали набор сценариев Windows PowerShell.

> [!NOTE]
> Эти действия необходимо выполнить *перед* созданием кластера, поэтому в тех случаях, когда сценариям необходимы имена кластеров и конечные точки, нужно использовать плановые значения, а не те кластеры и конечные точки, которые вы уже создали.
> 
> 

1. [Загрузите сценарии][sf-aad-ps-script-download] на компьютер.
2. Щелкните ZIP-файл правой кнопкой мыши, выберите **Свойства**, установите флажок **Разблокировать** и примените изменения.
3. Извлеките ZIP-файл.
4. Запустите `SetupApplications.ps1`, указав TenantId (идентификатор клиента), ClusterName (имя кластера) и WebApplicationReplyUrl (URL-адрес ответа веб-приложения) в качестве параметров. Например:
   
    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```
   
    Чтобы определить **TenantId** , найдите URL-адрес клиента на классическом портале Azure. Идентификатор GUID, включенный в этот URL-адрес, и есть идентификатор клиента TenantId. Например:
   
    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users
   
    Значение **ClusterName** используется в качестве префикса для приложений AAD, создаваемых сценарием. Это имя может не совпадать с фактическим именем кластера, так как оно лишь позволяет связать артефакты AAD с кластерами Service Fabric, с которыми они используются.
   
    **WebApplicationReplyUrl** — это конечная точка по умолчанию, которую AAD будет предлагать выполнившим вход пользователям. В этом параметре следует указать конечную точку Service Fabric Explorer для кластера, по умолчанию это:
   
    https://&lt;cluster_domain&gt;:19080/Explorer
   
    Вам будет предложено войти в учетную запись с правами администратора для клиента AAD. После этого скрипт начнет создавать веб-приложение и собственное приложение для представления кластера Service Fabric. Если посмотреть на список приложений клиента на [классическом портале Azure][azure-classic-portal], вы увидите два новых приложения:
   
   * *имя_кластера*\_Кластер
   * *имя_кластера*\_Клиент
     
     Этот скрипт выводит код JSON для шаблона Azure Resource Manager, который вы будете использовать в следующем разделе для создания кластера. Не закрывайте пока окно PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Создание шаблона Resource Manager для кластера Service Fabric
В этом разделе выходные данные предыдущей команды PowerShell переносятся в шаблон Resource Manager для кластера Service Fabric.

Примеры шаблонов Resource Manager доступны в [коллекции шаблонов быстрого запуска Azure на сайте GitHub][azure-quickstart-templates]. Их можно использовать в качестве отправной точки для создания шаблона кластера. 

### <a name="create-the-resource-manager-template"></a>Создание шаблона Resource Manager
В этом руководстве используется пример шаблона [безопасного кластера с 5 узлами][service-fabric-secure-cluster-5-node-1-nodetype-wad] и параметры шаблона. Скачайте файлы `azuredeploy.json` и `azuredeploy.parameters.json` на компьютер и откройте их в текстовом редакторе.

### <a name="add-certificates"></a>Добавление сертификатов
Сертификаты добавляются в шаблон Resource Manager кластера с помощью ссылок на хранилище ключей, которое содержит ключи сертификата. Рекомендуется поместить эти значения хранилища ключей в файл параметров шаблона Resource Manager, чтобы этот шаблон можно было использовать повторно и без значений параметров для конкретного развертывания.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Добавление всех сертификатов в osProfile VMSS
Каждый сертификат, который требуется установить в кластере, должен быть настроен в разделе osProfile ресурса VMSS (Microsoft.Compute/virtualMachineScaleSets). Это указывает, что поставщик ресурсов должен установить сертификат на виртуальных машинах. Сюда входит сертификат кластера, а также все сертификаты безопасности приложений, которые планируется использовать для приложений.

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Настройка сертификата кластера Service Fabric
Сертификат аутентификации кластера нужно также настроить в ресурсе кластера Service Fabric (Microsoft.ServiceFabric/clusters) и расширении Service Fabric для VMSS в ресурсе VMSS. Это позволяет поставщику ресурсов Service Fabric настроить его, чтобы использовать для аутентификации кластера и сервера на конечных точках управления.

##### <a name="vmss-resource:"></a>Ресурс VMSS.
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource:"></a>Ресурс Service Fabric.
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Вставка конфигурации AAD
Конфигурацию AAD, созданную ранее, можно вставить непосредственно в шаблон Resource Manager. Тем не менее рекомендуется сначала извлечь значения параметров в файл параметров, чтобы шаблон Resource Manager можно было использовать повторно, без значений для конкретного развертывания.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="<a-"configure-arm"-></a>configure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Настройка параметров шаблона Resource Manager
Наконец, используйте полученные значения из хранилища ключей и с помощью команд AAD PowerShell для заполнения файла параметров.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
На этом этапе должна быть представлена следующая конфигурация:

* группа ресурсов хранилища ключей;
  * хранилище ключей;
  * сертификат проверки подлинности сервера кластера;
  * сертификат шифрования данных;
* клиент Azure Active Directory; 
  * приложение AAD для управления через Интернет и Service Fabric Explorer;
  * приложение AAD для управления собственными клиентами;
  * пользователи с назначенными ролями; 
* шаблон Resource Manager для кластера Service Fabric;
  * сертификаты, настроенные с помощью хранилища ключей;
  * настроенная среда Azure Active Directory. 

На приведенной ниже схеме показано, как конфигурация хранилища ключей и AAD помещается в шаблон Resource Manager.

![Сопоставление зависимостей Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Создание кластера
Теперь вы готовы создать кластер с помощью [развертывания ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Тестирование
Используйте следующую команду PowerShell для тестирования шаблона Resource Manager с помощью файла параметров.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Развертывание
Если шаблон Resource Manager прошел проверку, то введите следующую команду PowerShell для развертывания этого шаблона с использованием файла параметров.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Назначение пользователей ролям
Создав приложения, представляющие кластер, необходимо сопоставить пользователей с ролями, поддерживаемыми Service Fabric и разрешающими доступ на чтение и администрирование. Это можно сделать на [классическом портале Azure][azure-classic-portal].

1. Перейдите к своему клиенту и выберите "Приложения".
2. Выберите веб-приложение с именем вида `myTestCluster_Cluster`.
3. Откройте вкладку "Пользователи".
4. Выберите пользователя для назначения и нажмите кнопку **Назначить** в нижней части экрана.
   
    ![Кнопка "Назначить пользователей ролям"][assign-users-to-roles-button]
5. Выберите роль для назначения пользователю.
   
    ![Назначение пользователей ролям][assign-users-to-roles-dialog]

> [!NOTE]
> Дополнительные сведения о ролях в Service Fabric см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).
> 
> 

 <a name="secure-linux-cluster"></a> 

## <a name="create-secure-clusters-on-linux"></a>Создание безопасных кластеров в Linux
Для упрощения процесса мы поместили [сюда](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)вспомогательный скрипт. Чтобы использовать этот скрипт, интерфейс CLI Azure должен быть установлен в стандартном расположении. Убедитесь, что скрипт имеет разрешения на выполнение, запустив `chmod +x cert_helper.py` после скачивания. Сначала войдите в учетную запись Azure, выполнив в интерфейсе командной строки команду `azure login` . Войдя в учетную запись Azure, используйте вспомогательный метод, передав ему подписанный сертификат ЦС, как показано в следующем примере:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Эта команда возвращает следующие три строки выходных данных: 

1. SourceVaultID — идентификатор новой группы ресурсов (KeyVault ResourceGroup), которая создана для вас; 
2. CertificateUrl — адрес для доступа к сертификату;
3. CertificateThumbprint — используется для проверки подлинности.

Ниже представлен пример использования этой команды:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Представленная выше команда вернет эти три строки:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить протокол SSL для конечных точек управления HTTPS в кластере и Service Fabric Explorer. Невозможно получить SSL-сертификат от центра сертификации (ЦС) для домена `.cloudapp.azure.com` . Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Это записи, которые потребуются для создания безопасного кластера Service Fabric (без AAD). Процесс создания описан в разделе [Настройка параметров шаблона Resource Manager](#configure-arm). Инструкции по подключению к безопасному кластеру см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md). Кластеры Linux предварительной версии не поддерживают проверку подлинности AAD. Можно назначить роли администратора и клиента, как описано в разделе [Назначение пользователей ролям](#assign-roles). Указывая роли администратора и клиента для предварительной версии кластера Linux, необходимо предоставить отпечатки сертификатов для проверки подлинности (в отличие от имени субъекта, так как в этом предварительном выпуске не выполняется проверка допустимости цепочки или ее отзыв).

Если вы хотите использовать для тестирования самозаверяющий сертификат, можно создать его и загрузить в хранилище ключей с помощью этого же скрипта. Для этого вместо расположения и имени сертификата укажите флаг `ss`. Вот пример команды для создания и передачи самозаверяющего сертификата:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Эта команда возвращает те же параметры SourceVault, CertificateUrl и CertificateThumbprint, которые использовались для создания безопасного кластера Linux, а также расположение созданного самозаверяющего сертификата. Самозаверяющий сертификат потребуется для подключения к кластеру.  Инструкции по подключению к безопасному кластеру см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md). Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить протокол SSL для конечных точек управления HTTPS в кластере и Service Fabric Explorer. Невозможно получить SSL-сертификат от центра сертификации (ЦС) для домена `.cloudapp.azure.com` . Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Параметры, которые возвращает вспомогательный скрипт, можно указать на портале, как описано в разделе [Create a cluster in the Azure portal](service-fabric-cluster-creation-via-portal.md#create-cluster-portal)(Создание кластера на портале Azure).

## <a name="next-steps"></a>Дальнейшие действия
На этом этапе у вас имеется защищенный кластер с Azure Active Directory для аутентификации управления. Далее [подключитесь к этому кластеру](service-fabric-connect-to-secure-cluster.md) и узнайте, как [управлять секретами приложений](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Устранение неполадок с настройкой Azure Active Directory для проверки подлинности клиента
При возникновении проблем во время настройки Azure Active Directory для проверки подлинности клиента ознакомьтесь со следующими вариантами возможных решений.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service Fabric Explorer предлагает выбрать сертификат
#### <a name="problem"></a>Проблема
После успешного входа на странице входа AAD в Service Fabric Explorer браузер возвращается на домашнюю страницу, но выводит диалоговое окно для выбора сертификата.

![Диалоговое окно выбора сертификата в Service Fabric Explorer][sfx-select-certificate-dialog]

#### <a name="reason"></a>Причина
Пользователю не была назначена роль в приложении кластера AAD. Таким образом, проверка подлинности AAD в кластере Service Fabric завершается ошибкой. Service Fabric Explorer воспользуется проверкой подлинности на основе сертификата.

#### <a name="solution"></a>Решение
Следуйте инструкциям по настройке AAD и назначению ролей пользователя. Кроме того, рекомендуется включить параметр "ДЛЯ ДОСТУПА К ПРИЛОЖЕНИЮ ТРЕБУЕТСЯ НАЗНАЧЕНИЕ ПОЛЬЗОВАТЕЛЕЙ", как это делается в `SetupApplications.ps1`.

### <a name="connect-with-powershell-fails-with-error:-the-specified-credentials-are-invalid"></a>Подключение с помощью PowerShell завершается ошибкой: указанные учетные данные недействительны.
#### <a name="problem"></a>Проблема
При использовании PowerShell для подключения к кластеру в режиме безопасности AzureActiveDirectory после успешного входа на странице входа в систему AAD подключение обрывается и выводится ошибка: указанные учетные данные недействительны.

#### <a name="solution"></a>Решение
То же, что и выше.

### <a name="service-fabric-explorer-signing-in-return-failure:-aadsts50011"></a>При входе в систему в Service Fabric Explorer возникает ошибка: AADSTS50011
#### <a name="problem"></a>Проблема
После входа на странице AAD в Service Fabric Explorer выдается ошибка входа: "AADSTS50011: адрес ответа &lt;URL-адрес&gt; не совпадает с адресами ответа, настроенными для приложения: &lt;guid&gt;". 

![Несовпадение адреса ответа в Service Fabric Explorer][sfx-reply-address-not-match]

#### <a name="reason"></a>Причина
Приложение кластера (веб-приложение), представляющее Service Fabric Explorer, пытается выполнить проверку подлинности в AAD и в составе запроса предоставляет URL-адрес ответа для перенаправления. Однако он отсутствует в списке "URL-АДРЕСОВ ОТВЕТА" приложения AAD.

#### <a name="solution"></a>Решение
Добавьте URL-адрес Service Fabric Explorer в список "URL-АДРЕСОВ ОТВЕТА" на вкладке настройки приложения кластера (веб-приложения) или замените один из элементов в списке. Затем сохраните список.

![URL-адрес ответа веб-приложения][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters?"></a>Можно ли использовать один клиент AAD для нескольких кластеров?
#### <a name="answer"></a>Ответ
Да. Но не забудьте добавить URL-адрес Service Fabric Explorer в приложение кластера (веб-приложение). В противном случае Service Fabric Explorer не будет работать.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled?"></a>Почему при включенной службе AAD все равно требуется сертификат сервера?
#### <a name="answer"></a>Ответ
FabricClient и FabricGateway выполняют взаимную проверку подлинности. При проверке подлинности AAD интеграция AAD предоставляет удостоверение клиента серверу, а сертификат сервера используется для проверки подлинности сервера. Дополнительные сведения об использовании сертификатов в Service Fabric см. в разделе [Сертификаты X.509 и Service Fabric][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


<!--HONumber=Oct16_HO2-->



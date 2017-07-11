---
title: "Создание кластера Azure Service Fabric из шаблона | Документация Майкрософт"
description: "В этой статье описывается настройка защищенного кластера Service Fabric в Azure с помощью Azure Resource Manager, Azure Key Vault и Azure Active Directory (Azure AD) для аутентификации клиента."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8d876a0f2168ee9375a3905d5d5a562ab1194cf3
ms.openlocfilehash: 9159f40fed17e52e6576efa1ea7e8a2dee98728e
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017


---
<a id="create-a-service-fabric-cluster-by-using-azure-resource-manager" class="xliff"></a>

# Создание кластера Service Fabric в Azure с помощью Azure Resource Manager
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов Azure](service-fabric-cluster-creation-via-arm.md)
> * [Портал Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Это пошаговое руководство поможет выполнить настройку защищенного кластера Azure Service Fabric в Azure с помощью Azure Resource Manager. Мы понимаем, что эта статья получилась довольно объемной. Тем не менее, если вы уже хорошо знакомы с ее содержимым, убедитесь, что тщательно выполняете каждое действие.

В этом руководстве описываются следующие процедуры.

* Настройка Azure Key Vault для отправки сертификатов для безопасности кластера и приложений
* Создание защищенного кластера в Azure с помощью Azure Resource Manager
* Аутентификация пользователей с помощью Azure Active Directory для управления кластером

Безопасный кластер — это кластер, в котором предотвращается несанкционированный доступ к операциям управления. Это предусматривает развертывание, обновление и удаление приложений, служб и данных, которые они содержат. Незащищенный кластер — это кластер, к которому в любое время может подключиться любой пользователь и выполнять операции управления. Хотя кластер можно создать небезопасным, настоятельно рекомендуется с самого начала создать безопасный кластер. Поскольку незащищенный кластер невозможно будет защитить позднее, нужно создать новый кластер.

Концепция создания безопасного кластер в Linux или Windows ничем не отличается. Дополнительные сведения о создании безопасных кластеров Linux, а также вспомогательные скрипты см. в разделе [Создание безопасных кластеров в Linux](#secure-linux-clusters).

<a id="sign-in-to-your-azure-account" class="xliff"></a>

## Вход в учетную запись Azure
В этом руководстве используется [Azure PowerShell][azure-powershell]. При запуске нового сеанса PowerShell войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.

Вход в учетную запись Azure:

```powershell
Login-AzureRmAccount
```

Выберите свою подписку.

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

<a id="set-up-a-key-vault" class="xliff"></a>

## Настройка хранилища ключей
В этом разделе описывается, как создать хранилище ключей для кластера Service Fabric в Azure и приложений Service Fabric. Полное руководство по использованию Azure Key Vault см. в статье [Приступая к работе с хранилищем ключей Azure][key-vault-get-started].

Service Fabric использует сертификаты X.509 для защиты кластера и обеспечения функций безопасности приложений. Key Vault используется для управления сертификатами кластеров Service Fabric в Azure. При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из Key Vault и устанавливает их на виртуальные машины кластера.

На приведенной ниже схеме показана связь между Azure Key Vault, кластером Service Fabric и поставщиком ресурсов Azure, который использует сертификаты из Key Vault при создании кластера.

![Схема установки сертификата][cluster-security-cert-installation]

<a id="create-a-resource-group" class="xliff"></a>

### Создание группы ресурсов
Первый шаг — создание группы ресурсов специально для хранилища ключей. Рекомендуется поместить хранилище ключей в собственной группе ресурсов. Так вы сможете удалять группы ресурсов для вычислений и хранения, включая группу ресурсов, в которой размещен кластер Service Fabric, без потери ключей и секретов. Группа ресурсов с хранилищем ключей _должна находиться в том же регионе_, что и кластер, который ее использует.

Если вы планируете развертывание кластеров в нескольких регионах, рекомендуется выбрать имя для группы ресурсов и хранилища ключей, чтобы обозначить область, к которой они относятся.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
Выходные данные должны выглядеть так:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

<a id="create-a-key-vault-in-the-new-resource-group" class="xliff"></a>

### Создание хранилища ключей в новой группе ресурсов.
Хранилище ключей _должно быть доступно для развернутой службы_. Это позволит поставщику вычислительных ресурсов получить из него сертификаты и установить их на экземплярах виртуальных машин.

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

Выходные данные должны выглядеть так:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
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
<a id="existing-key-vault"></a>

<a id="use-an-existing-key-vault" class="xliff"></a>

## Использование существующего хранилища ключей

Чтобы использовать существующее хранилище ключей, _необходимо включить его для развернутой службы_, благодаря чему поставщик вычислительных ресурсов сможет получить из него сертификаты и установить его на узлах кластера:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

<a id="add-certificates-to-your-key-vault" class="xliff"></a>

## Добавление сертификатов в хранилище ключей

Сертификаты используются в Service Fabric, чтобы обеспечить функции аутентификации и шифрования для защиты различных аспектов кластера и его приложений. Дополнительные сведения о том, как сертификаты используются в Service Fabric, см. в статье [Сценарии защиты кластера Service Fabric][service-fabric-cluster-security].

<a id="cluster-and-server-certificate-required" class="xliff"></a>

### Сертификат кластера и сервера (обязательно)
Этот сертификат требуется для защиты кластера и предотвращения несанкционированного доступа к нему. Он обеспечивает безопасность кластера двумя способами.

* Аутентификация в кластере позволяет аутентифицировать обмен данными между узлами для федерации кластера. Только узлы, которые могут подтвердить свою подлинность с помощью этого сертификата, могут присоединиться к кластеру.
* Аутентификация сервера позволяет аутентифицировать конечные точки управления кластера в клиенте управления, чтобы он "знал", что обращается к настоящему кластеру. Этот сертификат также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS.

Для этого сертификат должен отвечать следующим требованиям:

* Сертификат должен содержать закрытый ключ.
* Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене .cloudapp.azure.com. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

<a id="application-certificates-optional" class="xliff"></a>

### Сертификаты приложения (необязательно)
В кластере можно установить любое количество дополнительных сертификатов для обеспечения безопасности приложений. Перед созданием кластера рассмотрите сценарии безопасности приложений, которые требуют установки сертификатов на узлах, в том числе:

* шифрование и расшифровка значений конфигурации приложений;
* шифрование данных между узлами во время репликации.

<a id="formatting-certificates-for-azure-resource-provider-use" class="xliff"></a>

### форматирование сертификатов для использования поставщиком ресурсов Azure.
Файлы закрытых ключей (PFX) можно добавить и использовать непосредственно в своем хранилище ключей. Однако для поставщика вычислительных ресурсов Azure необходимо хранить ключи в специальном формате нотации объектов JavaScript (JSON). В этом формате PFX-файл представлен в виде строки в кодировке Base64 и пароля закрытого ключа. Чтобы соблюсти эти требования, ключи должны быть помещены в строку JSON и сохранены как "секреты" в хранилище ключей.

Для упрощения этого процесса [на сайте GitHub доступен соответствующий модуль PowerShell][service-fabric-rp-helpers]. Чтобы использовать этот модуль, выполните следующие действия.

1. Скачайте все содержимое репозитория в локальный каталог.
2. Перейдите в локальный каталог.
2. Импортируйте модуль ServiceFabricRPHelpers в окне PowerShell.

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

Команда `Invoke-AddCertToKeyVault` в этом модуле PowerShell автоматически форматирует закрытый ключ сертификата в строку JSON и передает ее в хранилище ключей. Используйте эту команду для добавления сертификата кластера и всех дополнительных сертификатов приложения в хранилище ключей. Повторите этот шаг для всех дополнительных сертификатов, которые нужно установить в кластере.

<a id="uploading-an-existing-certificate" class="xliff"></a>

#### Загрузка существующего сертификата

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Если появляется сообщение об ошибке, такое как показано здесь, обычно это означает наличие конфликта URL-адресов ресурсов. Чтобы устранить конфликт, измените имя хранилища ключей.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

После устранения конфликта результат должен выглядеть следующим образом:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Чтобы настроить безопасность кластера Service Fabric и получить все сертификаты приложения, которые, возможно, будут использоваться для обеспечения безопасности приложения, необходимы три строки — CertificateThumbprint, SourceVault и CertificateURL. Если не сохранить эти строки, позднее могут возникнуть трудности с их получением путем запроса в хранилище ключей.

<a id="add-self-signed-certificate-to-key-vault"></a>

<a id="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault" class="xliff"></a>

#### Создание самозаверяющего сертификата и передача его в хранилище ключей

Если сертификат уже отправлен в хранилище ключей, пропустите этот шаг. На этом шаге создается новый самозаверяющий сертификат и выполняется его передача в хранилище ключей. После изменения параметров в следующем сценарии и его выполнения вам будет предложено ввести пароль сертификата.  

```powershell

$ResouceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Если появляется сообщение об ошибке, такое как показано здесь, обычно это означает наличие конфликта URL-адресов ресурсов. Чтобы устранить конфликт, измените имя хранилища ключей, имя группы ресурсов и т. д.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

После устранения конфликта результат должен выглядеть следующим образом:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Чтобы настроить безопасность кластера Service Fabric и получить все сертификаты приложения, которые, возможно, будут использоваться для обеспечения безопасности приложения, необходимы три строки — CertificateThumbprint, SourceVault и CertificateURL. Если не сохранить эти строки, позднее могут возникнуть трудности с их получением путем запроса в хранилище ключей.

 На этом этапе должны быть доступны следующие элементы:

* группа ресурсов хранилища ключей;
* хранилище ключей и его URL-адрес (вызываемое SourceVault в предыдущем выводе PowerShell);
* сертификат аутентификации сервера кластера и его URL-адрес в хранилище ключей;
* сертификаты приложений и их URL-адреса в хранилище ключей.


<a id="add-AAD-for-client"></a>

<a id="set-up-azure-active-directory-for-client-authentication" class="xliff"></a>

## Настройка Azure Active Directory для проверки подлинности клиента

Azure AD позволяет организациям (известным как клиенты) управлять доступом пользователей к приложениям. Приложения можно разделить на две группы: те, в которых есть пользовательский веб-интерфейс входа в систему, и те, в которых вход выполняется с помощью собственного клиентского приложения. В этой статье предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory][active-directory-howto-tenant].

Кластеры Service Fabric предлагают несколько точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer][service-fabric-visualizing-your-cluster] и [Visual Studio][service-fabric-manage-application-in-visual-studio]. В итоге вы получаете два приложения Azure AD для управления доступом к кластеру: одно веб-приложение и одно собственное приложение.

Чтобы упростить некоторые шаги по настройке Azure AD с кластером Service Fabric, мы создали набор сценариев Windows PowerShell.

> [!NOTE]
> Перед созданием кластера необходимо выполнить следующие действия. Поскольку в сценариях предварительно заданы имена кластеров и конечные точки, эти имена нужно выбрать заблаговременно, при этом они должны отличаться от созданных ранее имен.

1. [Скачайте сценарии][sf-aad-ps-script-download] на компьютер.
2. Щелкните правой кнопкой мыши ZIP-файл, выберите **Свойства**, установите флажок **Разблокировать** и нажмите кнопку **Применить**.
3. Извлеките ZIP-файл.
4. Запустите `SetupApplications.ps1` и укажите TenantId (идентификатор клиента), ClusterName (имя кластера) и WebApplicationReplyUrl (URL-адрес ответа веб-приложения) в качестве параметров. Например:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Чтобы узнать TenantId, можно выполнить команду PowerShell `Get-AzureSubscription`. После выполнения этой команды для каждой подписки отображается TenantId.

    Значение ClusterName используется в качестве префикса для приложений Azure AD, создаваемых сценарием. Точное совпадение с именем реального кластера не требуется. Оно предназначено только для упрощения сопоставления артефактов Azure AD с кластером Service Fabric, с которым они используются.

    WebApplicationReplyUrl является конечной точкой по умолчанию, которую Azure AD возвращает пользователям после завершения ими входа. Эту конечную точку следует назначить конечной точкой Service Fabric Explorer для кластера, по умолчанию это:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Вам будет предложено войти в учетную запись с правами администратора для клиента Azure AD. После входа сценарий начнет создавать веб-приложение и собственное приложение для представления кластера Service Fabric. Если посмотреть на список приложений клиента на [классическом портале Azure][azure-classic-portal], вы увидите два новых приложения:

   * *имя_кластера*\_Кластер
   * *имя_кластера*\_Клиент

   Этот скрипт выводит код JSON для шаблона Azure Resource Manager, который вы будете использовать в следующем разделе для создания кластера. Рекомендуем не закрывать пока окно PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="create-a-service-fabric-cluster-resource-manager-template" class="xliff"></a>

## Создание шаблона Resource Manager для кластера Service Fabric
В этом разделе выходные данные предыдущей команды PowerShell переносятся в шаблон Resource Manager для кластера Service Fabric.

Примеры шаблонов Resource Manager доступны в [коллекции шаблонов быстрого запуска Azure на сайте GitHub][azure-quickstart-templates]. Их можно использовать в качестве отправной точки для создания шаблона кластера.

<a id="create-the-resource-manager-template" class="xliff"></a>

### Создание шаблона Resource Manager
В этом руководстве используется пример шаблона [защищенного кластера с 5 узлами][service-fabric-secure-cluster-5-node-1-nodetype] и его параметры. Скачайте файлы `azuredeploy.json` и `azuredeploy.parameters.json` на компьютер и откройте их в текстовом редакторе.

<a id="add-certificates" class="xliff"></a>

### Добавление сертификатов
Сертификаты добавляются в шаблон Resource Manager кластера с помощью ссылок на хранилище ключей, которое содержит ключи сертификата. Рекомендуется поместить значения хранилища ключей в файл параметров шаблона Resource Manager. Благодаря этому файл шаблона Resource Manager доступен для повторного использования и не зависит от значений, связанных с определенной развернутой службой.

<a id="add-all-certificates-to-the-virtual-machine-scale-set-osprofile" class="xliff"></a>

#### Добавление всех сертификатов в osProfile набора масштабирования виртуальных машин
Каждый сертификат, который устанавливается в кластере, должен быть настроен в разделе osProfile ресурса VMSS (Microsoft.Compute/virtualMachineScaleSets). Это указывает, что поставщик ресурсов должен установить сертификат на виртуальных машинах. Эта установка содержит сертификат кластера, а также все сертификаты безопасности приложений, которые планируется использовать для приложений.

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

<a id="configure-the-service-fabric-cluster-certificate" class="xliff"></a>

#### Настройка сертификата кластера Service Fabric
Сертификат аутентификации кластера должен быть настроен в ресурсе кластера Service Fabric (Microsoft.ServiceFabric/clusters) и расширении Service Fabric для масштабируемых наборов виртуальных машин в ресурсе масштабируемого набора виртуальных машин. Это позволяет поставщику ресурсов Service Fabric настроить его, чтобы использовать для аутентификации кластера и сервера на конечных точках управления.

<a id="virtual-machine-scale-set-resource" class="xliff"></a>

##### Ресурс масштабируемого набора виртуальных машин:
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

<a id="service-fabric-resource" class="xliff"></a>

##### Ресурс Service Fabric.
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

<a id="insert-azure-ad-configuration" class="xliff"></a>

### Получение конфигурации Azure AD
Конфигурацию Azure AD, которая была создана ранее, можно добавить непосредственно в шаблон Resource Manager. Тем не менее рекомендуется сначала извлечь значения в файл параметров, чтобы сохранить возможность повторного использования шаблона Resource Manager и не привязываться к значениям, относящимся к определенной развернутой службе.

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

### <a "configure-arm" ></a>Настройка параметров шаблона Resource Manager
Наконец, используйте полученные значения из хранилища ключей и с помощью команд PowerShell Azure AD для заполнения файла параметров.

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
На этом этапе должны быть доступны следующие элементы:

* группа ресурсов хранилища ключей;
  * Хранилище ключей
  * сертификат проверки подлинности сервера кластера;
  * сертификат шифрования данных;
* клиент Azure Active Directory;
  * приложение Azure AD для управления через Интернет и Service Fabric Explorer;
  * приложение Azure AD для управления собственными клиентами.
  * Пользователи и назначенные им роли
* шаблон Resource Manager для кластера Service Fabric;
  * Сертификаты, настроенные с помощью хранилища ключей
  * настроенная среда Azure Active Directory.

На приведенной ниже схеме показано, как конфигурация хранилища ключей и Azure AD помещается в шаблон Resource Manager.

![Сопоставление зависимостей Resource Manager][cluster-security-arm-dependency-map]

<a id="create-the-cluster" class="xliff"></a>

## Создание кластера
Теперь можно создать кластер с помощью [развертывания шаблона ресурсов Azure][resource-group-template-deploy].

<a id="test-it" class="xliff"></a>

#### Тестирование
Используйте следующую команду PowerShell для тестирования шаблона Resource Manager с помощью файла параметров.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a id="deploy-it" class="xliff"></a>

#### Развертывание
Если шаблон Resource Manager прошел проверку, то введите следующую команду PowerShell для развертывания этого шаблона с использованием файла параметров.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

<a id="assign-users-to-roles" class="xliff"></a>

## Назначение пользователей ролям
Создав приложения, представляющие кластер, сопоставьте пользователей с ролями, поддерживаемыми Service Fabric и разрешающими доступ на чтение и администрирование. Роли можно назначить с помощью [классического портала Azure][azure-classic-portal].

1. На портале Azure перейдите к нужному клиенту и выберите элемент **Приложения**.
2. Выберите веб-приложение с именем вида `myTestCluster_Cluster`.
3. Откройте вкладку **Пользователи** .
4. Выберите пользователя для назначения и нажмите кнопку **Назначить** в нижней части экрана.

    ![Кнопка "Назначить пользователей ролям"][assign-users-to-roles-button]
5. Выберите роль для назначения пользователю.

    ![Диалоговое окно "Назначить пользователей"][assign-users-to-roles-dialog]

> [!NOTE]
> Дополнительные сведения о ролях в Service Fabric см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-cluster"></a>

<a id="create-secure-clusters-on-linux" class="xliff"></a>

## Создание безопасных кластеров в Linux
Чтобы упростить процесс, мы предоставили [вспомогательный сценарий](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Прежде чем использовать этот вспомогательный сценарий, убедитесь, что интерфейс командной строки Azure уже установлен и указан в пути. Убедитесь, что скрипт имеет разрешения на выполнение, запустив `chmod +x cert_helper.py` после скачивания. Первым делом войдите в учетную запись Azure с помощью команды `azure login` интерфейса командной строки. Войдя в учетную запись Azure, используйте вспомогательный сценарий, передав ему подписанный сертификат ЦС, как показано в следующем примере:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

Параметр -ifile позволяет загрузить начальные данные из PFX-файла или PEM-файла с указанием соответствующего типа сертификата (pfx, pem или ss, если это самозаверяющий сертификат).
Параметр -h позволяет вывести текст справки.


Эта команда возвращает следующие три строки выходных данных:

* SourceVaultID — идентификатор новой группы ресурсов (KeyVault ResourceGroup), которая создана для вас;
* CertificateUrl — адрес для доступа к сертификату;
* CertificateThumbprint — используется для аутентификации.

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

Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат из ЦС для домена `.cloudapp.azure.com`. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Эти имена субъектов являются записями, которые необходимы для создания безопасного кластера Service Fabric (без Azure AD), как описано в разделе [Настройка параметров шаблона Resource Manager](#configure-arm). Инструкции по подключению к безопасному кластеру см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md). Кластеры Linux предварительной версии не поддерживают аутентификацию Azure AD. Можно назначить роли администратора и клиента, как описано в разделе [Назначение ролей пользователям](#assign-roles). При указании роли администратора и клиента для предварительной версии кластера Linux нужно предоставить отпечатки сертификатов для аутентификации. (Отсутствует имя получателя, так как в этом предварительном выпуске не выполняется цепочка проверки или отзыва.)

Если для тестирования нужно использовать самозаверяющий сертификат, можно использовать тот же сценарий для его создания. Затем можно передать сертификат в хранилище ключей, указав флаг `ss` вместо указания пути и имени сертификата. Вот пример команды для создания и передачи самозаверяющего сертификата:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Эта команда возвращает те же три строки: SourceVault, CertificateUrl и CertificateThumbprint. Затем эти строки можно использовать для создания безопасного кластера Linux и расположения для размещения самозаверяющего сертификата. Самозаверяющий сертификат потребуется для подключения к кластеру. Инструкции по подключению к безопасному кластеру см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md).

Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат из ЦС для домена `.cloudapp.azure.com`. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Параметры можно заполнить данными из вспомогательного сценария на портале Azure, как описано в разделе [Создание кластера на портале Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal).

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
На этом этапе у вас имеется защищенный кластер с Azure Active Directory для аутентификации управления. Далее [подключитесь к этому кластеру](service-fabric-connect-to-secure-cluster.md) и узнайте, как [управлять секретами приложений](service-fabric-application-secret-management.md).

<a id="troubleshoot-setting-up-azure-active-directory-for-client-authentication" class="xliff"></a>

## Устранение неполадок с настройкой Azure Active Directory для проверки подлинности клиента
Если при настройке Azure AD для аутентификации клиента возникли проблемы, возможные решения можно найти в этом разделе.

<a id="service-fabric-explorer-prompts-you-to-select-a-certificate" class="xliff"></a>

### Service Fabric Explorer предлагает выбрать сертификат
<a id="problem" class="xliff"></a>

#### Проблема
После успешного входа в Azure AD в Service Fabric Explorer браузер отображает домашнюю страницу, но на экране отображается предложение выбрать сертификат.

![Диалоговое окно выбора сертификата в Service Fabric Explorer][sfx-select-certificate-dialog]

<a id="reason" class="xliff"></a>

#### Причина
Пользователю не назначена роль в приложении кластера Azure AD. Таким образом, аутентификация Azure AD в кластере Service Fabric завершается ошибкой. Service Fabric Explorer воспользуется проверкой подлинности на основе сертификата.

<a id="solution" class="xliff"></a>

#### Решение
Следуйте инструкциям по настройке Azure AD и назначению ролей пользователей. Кроме того, рекомендуется включить параметр "Для доступа к приложению требуется назначение пользователей", как делает сценарий `SetupApplications.ps1`.

<a id="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid" class="xliff"></a>

### Подключение с помощью PowerShell завершается ошибкой: "Указанные учетные данные недействительны".
<a id="problem" class="xliff"></a>

#### Проблема
После успешного входа в Azure AD при использовании PowerShell для подключения к кластеру с помощью режима безопасности AzureActiveDirectory возникла ошибка, подключение завершается с ошибкой: "Указанные учетные данные недействительны".

<a id="solution" class="xliff"></a>

#### Решение
Решение этой проблемы такое же, как и в предыдущем случае.

<a id="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011" class="xliff"></a>

### Service Fabric Explorer при входе возвращает ошибку "AADSTS50011"
<a id="problem" class="xliff"></a>

#### Проблема
При попытке входа на страницу Azure AD в Service Fabric Explorer выдается ошибка: "AADSTS50011: адрес ответа &lt;URL-адрес&gt; не совпадает с адресами ответа, настроенными для приложения: &lt;guid&gt;".

![Несовпадение адреса ответа в Service Fabric Explorer][sfx-reply-address-not-match]

<a id="reason" class="xliff"></a>

#### Причина
Приложение кластера (веб-приложение), представляющее Service Fabric Explorer, пытается выполнить аутентификацию в Azure AD и в составе запроса предоставляет URL-адрес ответа для перенаправления. URL-адрес не указан в списке **URL-АДРЕС ОТВЕТА** приложения Azure AD.

<a id="solution" class="xliff"></a>

#### Решение
Добавьте URL-адрес Service Fabric Explorer в список **URL-АДРЕСОВ ОТВЕТА** на вкладке **Настройки** приложения кластера (веб-приложения) или замените один из элементов в списке. После завершения сохраните изменения.

![URL-адрес ответа веб-приложения][web-application-reply-url]

<a id="connect-the-cluster-by-using-azure-ad-authentication-via-powershell" class="xliff"></a>

### Подключение к кластеру с помощью аутентификации Azure AD с использованием PowerShell
Чтобы подключить кластер Service Fabric, воспользуйтесь следующим примером команды PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Дополнительные сведения о командлете Connect-ServiceFabricCluster см. в [этой статье](https://msdn.microsoft.com/library/mt125938.aspx).

<a id="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters" class="xliff"></a>

### Можно ли повторно использовать один и тот же клиент Azure AD в нескольких кластерах?
Да. Не забудьте добавить URL-адрес Service Fabric Explorer в (веб-)приложение кластера. В противном случае — Service Fabric Explorer не работает.

<a id="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled" class="xliff"></a>

### Почему мне все еще нужен сертификат сервера при включенном Azure AD?
FabricClient и FabricGateway выполняют взаимную аутентификацию. Во время аутентификации Azure AD интеграция Azure AD предоставляет удостоверение клиента серверу и для аутентификации сервера используется сертификат сервера. Дополнительные сведения о сертификатах Service Fabric см. в разделе [Сертификаты X.509 и Service Fabric][x509-certificates-and-service-fabric].

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
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
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


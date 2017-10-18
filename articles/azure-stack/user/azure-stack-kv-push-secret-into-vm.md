---
title: "Развертывание виртуальной машины с сертификатом, безопасно хранящимся в Azure Stack | Документация Майкрософт"
description: "Узнайте, как развернуть виртуальную машину и отправить в нее сертификат с помощью хранилища ключей в Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Создание виртуальной машины и добавление сертификатов, полученных из хранилища ключей

В этой статье показано, как создать виртуальную машину в Azure Stack и отправить в нее сертификаты. 

## <a name="prerequisites"></a>Предварительные требования

* Необходимо подписаться на предложение, включающее службу хранилища ключей. 
* [Установите PowerShell для Azure Stack.](azure-stack-powershell-install.md)  
* [Настройте пользовательскую среду PowerShell в Azure Stack.](azure-stack-powershell-configure-user.md)

Хранилище ключей в Azure Stack используется для хранения сертификатов. Сертификаты полезны в различных сценариях. Предположим, у вас есть виртуальная машина в Azure Stack, где выполняется приложение, которому нужен сертификат. Этот сертификат может использоваться для шифрования, проверки подлинности в Active Directory или для SSL-соединения на веб-сайте. Если сертификат содержится в хранилище ключей, это гарантирует, что он безопасный.

В этой статье рассматриваются шаги, необходимые для отправки сертификата на виртуальную машину Windows в Azure Stack. Эти шаги можно выполнить либо из комплекта разработки Azure Stack, либо из внешнего клиента на базе Windows при подключении через VPN.

Ниже представлены шаги, необходимые для отправки сертификата на виртуальную машину:

1. Создание секрета хранилища ключей.
2. Обновление файла azuredeploy.parameters.json соответствующим образом.
3. Развертывание шаблона

## <a name="create-a-key-vault-secret"></a>Создание секрета хранилища ключей

Следующий скрипт создает сертификат в формате PFX, создает хранилище ключей и сохраняет в нем сертификат в качестве секрета. Используйте параметр `-EnabledForDeployment` при создании хранилища ключей. Благодаря этому параметру на хранилище ключей можно ссылаться из шаблонов Azure Resource Manager.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

В результате выполнения предыдущего скрипта был выведен URI секрета. Запишите его. На него необходимо указать ссылку при [отправке сертификата в шаблон Resource Manager для Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Скачайте папку [vm-push-certificate-windows template](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) на компьютер разработки. Эта папка содержит файлы `azuredeploy.json` и `azuredeploy.parameters.json`, которые необходимы на следующих шагах.

Измените файл `azuredeploy.parameters.json`, указав значения для своей среды. Особый интерес представляют параметры имени хранилища, группы ресурсов хранилища и URI секрета (сгенерированный предыдущим скриптом). Ниже приведен пример файла параметров:

## <a name="update-the-azuredeployparametersjson-file"></a>Обновление файла azuredeploy.parameters.json

Обновите файл azuredeploy.parameters.json, указав vaultName, URI секрета, VmName и другие значения для своей среды. Ниже приведен пример JSON-файла параметров шаблона: 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Теперь разверните шаблон с помощью следующего скрипта PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

При успешном развертывании шаблона выводятся следующие выходные данные:

![Выходные данные развертывания](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Когда эта виртуальная машина развернута, Azure Stack отправляет на нее сертификат. В Windows сертификат добавляется в расположение сертификата LocalMachine с помощью хранилища сертификатов, предоставленного пользователем. В Linux сертификат размещается в каталоге /var/lib/waagent: файл сертификата X509 с именем &lt;Верхний_регистр&gt;.crt и файл закрытого ключа с именем &lt;Верхний_регистр&gt;.prv.

## <a name="retire-certificates"></a>Списание сертификатов

В предыдущем разделе мы показали, как отправить новый сертификат на виртуальную машину. Старый сертификат по-прежнему находится на виртуальной машине и не может быть удален. Однако более раннюю версию секрета можно отключить с помощью командлета `Set-AzureKeyVaultSecretAttribute`. Ниже приведен пример использования этого командлета. Обязательно замените имя хранилища, имя секрета и значения версии в соответствии с вашей средой:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание виртуальной машины с помощью пароля из хранилища ключей](azure-stack-kv-deploy-vm-with-secret.md)
* [Пример приложения, использующего ключи и секретные данные, хранящиеся в хранилище ключей](azure-stack-kv-sample-app.md)



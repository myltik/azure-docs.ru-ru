---
title: "Развертывание виртуальной машины с паролем, безопасно хранящимся в Azure Stack | Документация Майкрософт"
description: "Узнайте, как развернуть виртуальную машину с использованием пароля, хранящегося в хранилище ключей Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Создание виртуальной машины путем извлечения пароля, хранящегося в хранилище ключей

Если в процессе развертывания необходимо передать безопасное значение (например, пароль), его можно сохранить как секретный код в хранилище ключей Azure Stack и вставить ссылку на это значение в шаблоны Azure Resource Manager. Не нужно вручную вводить секрет каждый раз, когда вы развертываете ресурсы. Можно также указать, какие пользователи или субъекты-службы могут получить доступ к секрету. 

В этой статье рассматриваются шаги, необходимые для развертывания виртуальной машины Windows в Azure Stack с получением пароля, который хранится в хранилище ключей. Поэтому пароль никогда не помещается в виде обычного текста в файле параметров шаблона. Эти шаги можно выполнить либо из комплекта разработки Azure Stack, либо из внешнего клиента при подключении через VPN.

## <a name="prerequisites"></a>Предварительные требования
 
* Необходимо подписаться на предложение, включающее службу хранилища ключей.  
* [Установите PowerShell для Azure Stack.](azure-stack-powershell-install.md)  
* [Настройте пользовательскую среду PowerShell в Azure Stack.](azure-stack-powershell-configure-user.md)

Следующие шаги описывают процесс, необходимый для создания виртуальной машины путем извлечения пароля, хранящегося в хранилище ключей:

1. Создайте секрет хранилища ключей.
2. Обновите файл azuredeploy.parameters.json соответствующим образом.
3. Разверните шаблон.

## <a name="create-a-key-vault-secret"></a>Создание секрета хранилища ключей

Следующий скрипт создает хранилище ключей и сохраняет в нем пароль в виде секрета. Используйте параметр `-EnabledForDeployment` при создании хранилища ключей. Благодаря этому параметру на хранилище ключей можно ссылаться из шаблонов Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

В результате выполнения предыдущего скрипта был выведен URI секрета. Запишите его. На него необходимо указать ссылку при [развертывании виртуальной машины Windows с паролем, хранящимся в шаблоне хранилища ключей](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Скачайте папку [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) на компьютер разработчика. Эта папка содержит файлы `azuredeploy.json` и `azuredeploy.parameters.json`, которые необходимы на следующих шагах.

Измените файл `azuredeploy.parameters.json`, указав значения для своей среды. Особый интерес представляют параметры имени хранилища, группы ресурсов хранилища и URI секрета (сгенерированный предыдущим скриптом). Ниже приведен пример файла параметров:

## <a name="update-the-azuredeployparametersjson-file"></a>Обновление файла azuredeploy.parameters.json

В файле azuredeploy.parameters.json укажите значения универсального кода ресурса (URI) KeyVault, secretName, adminUsername виртуальной машины в соответствии со средой. Ниже приведен пример JSON-файла параметров шаблона: 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Развертывание шаблона

Теперь разверните шаблон с помощью следующего скрипта PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
При успешном развертывании шаблона выводятся следующие выходные данные:

![Выходные данные развертывания](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Дальнейшие действия
[Sample application that uses keys and secrets stored in a key vault](azure-stack-kv-sample-app.md) (Пример приложения, использующего ключи и секреты из хранилища ключей)

[Create a virtual machine and include certificate retrieved from a key vault](azure-stack-kv-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)


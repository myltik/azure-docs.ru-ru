<properties
   pageTitle="Использование секретного кода хранилища ключей в шаблоне диспетчера ресурсов | Microsoft Azure"
   description="Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="tomfitz"/>

# Передача безопасных значений в процессе развертывания

Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например пароль), его можно сохранить как секретный код в [хранилище ключей Azure](./key-vault/key-vault-whatis.md) и вставить ссылку на это значение в другие шаблоны диспетчера ресурсов. В шаблоне указывается только ссылка на секретный код, в то время как сам секретный код не раскрывается, и вам не придется вводить его значение вручную при каждом развертывании ресурсов. Укажите, какие пользователи или субъекты-службы смогут получить доступ к секретному коду.

## Развертывание хранилища ключей и секретного кода

Чтобы создать хранилище ключей, на которое смогут ссылаться другие шаблоны диспетчера ресурсов, необходимо указать для свойства **enabledForTemplateDeployment** значение **true** и предоставить доступ пользователю или субъекту-службе, который будет выполнять развертывание со ссылкой на секретный код.

Дополнительные сведения о развертывании хранилища ключей и секретного кода см. в статьях [Схема хранилища ключей](resource-manager-template-keyvault.md) и [Схема секретного кода хранилища ключей](resource-manager-template-keyvault-secret.md).

## Ссылка на секретный код

Ссылки на секретный код включаются в файл параметров, который передает значения в шаблон. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Весь файл параметров может выглядеть следующим образом:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Принимать секретный код должен параметр **securestring**. В следующем примере показаны соответствующие разделы шаблона, который развертывает SQL Server и требует пароля администратора.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## Дальнейшие действия

- Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).
- Сведения об использовании хранилища ключей с виртуальной машиной см. в разделе [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).
- Полные примеры ссылок на секретные коды ключей см. в статье [Примеры хранилища ключей](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0224_2016-->
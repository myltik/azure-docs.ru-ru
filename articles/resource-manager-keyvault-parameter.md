<properties
   pageTitle="Секретный код хранилища ключей в шаблоне диспетчера ресурсов | Microsoft Azure"
   description="Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Передача безопасных значений в процессе развертывания

Если в процессе развертывания в качестве параметра необходимо передать безопасное значение (например пароль), его можно сохранить как секретный код в [хранилище ключей Azure](./key-vault/key-vault-whatis.md) и вставить ссылку на это значение в другие шаблоны диспетчера ресурсов. В шаблоне указывается только ссылка на секретный код, в то время как сам секретный код не раскрывается, и вам не придется вводить его значение вручную при каждом развертывании ресурсов. Укажите, какие пользователи или субъекты-службы смогут получить доступ к секретному коду.

## Развертывание хранилища ключей и секретного кода

Чтобы создать хранилище ключей, на которое смогут ссылаться другие шаблоны диспетчера ресурсов, необходимо указать для свойства **enabledForTemplateDeployment** значение **true** и предоставить доступ пользователю или субъекту-службе, который будет выполнять развертывание со ссылкой на секретный код.

Дополнительные сведения о развертывании хранилища ключей и секретного кода см. в статьях [Схема хранилища ключей](resource-manager-template-keyvault.md) и [Схема секретного кода хранилища ключей](resource-manager-template-keyvault-secret.md).

## Ссылка на секретный код со статическим идентификатором

Ссылки на секретный код включаются в файл параметров, который передает значения в шаблон. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В этом примере секретный код хранилища ключей уже должен существовать; при этом используется статическое значение для идентификатора ресурса.

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

## Ссылка на секретный код с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секретного кода хранилища ключей. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. В этом случае вы не можете жестко задать идентификатор ресурса в файле параметров. К сожалению, динамически создать идентификатор ресурса в файле параметров нельзя, так как выражения шаблонов в файле параметров не разрешены.

Чтобы динамически создать идентификатор ресурса для секретного кода хранилища ключей, необходимо переместить ресурс, для которого требуется секретный код, во вложенный шаблон. Добавьте вложенный шаблон в главный шаблон и передайте параметр, содержащий динамически созданный идентификатор ресурса.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## Дальнейшие действия

- Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).
- Сведения об использовании хранилища ключей с виртуальной машиной см. в разделе [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).
- Полные примеры ссылок на секретные коды ключей см. в статье [Примеры хранилища ключей](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0629_2016-->
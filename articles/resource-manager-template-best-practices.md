<properties
	pageTitle="Рекомендации по созданию шаблона Azure Resource Manager | Microsoft Azure"
	description="Рекомендации по упрощению шаблонов Azure Resource Manager."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="tomfitz"/>

# Рекомендации по созданию шаблонов Azure Resource Manager

Приведенные рекомендации помогут создавать шаблоны Resource Manager, которые надежны и удобны в использовании. Указания в данной статье носят рекомендательный характер, то есть не являются обязательными. Для вашего сценария может потребоваться отклониться от этих указаний.

## Имена ресурсов

Обычно вы работаете с именами ресурсов трех типов:

1. имена ресурсов, которые должны быть уникальными;
2. имена ресурсов, которые могут не быть уникальными, но должны помогать в определении контекста;
3. имена ресурсов, которые могут быть универсальными.

Для получения справки по установлению соглашения об именовании ознакомьтесь с [руководством по именованию инфраструктуры](./virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md). Сведения об ограничениях имен ресурсов см. в разделе [Рекомендуемые соглашения об именовании для ресурсов Azure](./guidance/guidance-naming-conventions.md).

### Уникальные имена ресурсов

Необходимо указать уникальное имя для любого типа ресурса, который имеет конечную точку доступа к данным. Ниже приведено несколько распространенных типов, для которых требуются уникальные имена:

- Учетная запись хранения
- Веб-сайт
- SQL Server
- Хранилище ключей
- Кэш Redis
- Учетная запись Пакетной службы
- Диспетчер трафика
- Служба поиска
- Кластер HDInsight

Более того, имена учетных записей хранения должны содержать до 24 знаков в нижнем регистре без дефисов.

Вместо того, чтобы предоставить параметр для этих имен ресурсов и во время развертывания пытаться угадать, уникально ли имя, можно создать переменную, которая использует функцию [uniqueString()](resource-group-template-functions.md#uniquestring) для создания имен. Нередко вам также нужно добавлять префикс или постфикс к результату **uniqueString**, чтобы было проще определить тип ресурса по его имени. Например, можно создать уникальное имя учетной записи хранения с помощью следующей переменной.

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

Учетные записи хранения с префиксом uniqueString не будут распределены в кластеры, находящиеся в одной и той же стойке.

### Имена ресурсов для идентификации

Для типов ресурсов, которым не обязательно нужны уникальные имена, достаточно просто указать имя, которое определяет контекст и тип ресурса. Целесообразно предоставить описательное имя, которое поможет узнать его в списке имен ресурсов. Если требуется изменить имя ресурса во время развертывания, то используйте для имени параметр.

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

Если необходимо передать имя во время развертывания, то используйте переменную.

    "variables": {
        "vmName": "demoLinuxVM"
    }

Или используйте жестко заданное значение.

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### Универсальные имена ресурсов

Для типов ресурсов, к которым часто обращаются через другой ресурс, можно использовать универсальное имя, жестко заданное в шаблоне. Например, вряд ли вы захотите предоставить настраиваемое имя для правил брандмауэра на сервере SQL Server.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## Параметры

1. Используйте как можно меньше параметров. Если можно, используйте переменную или литерал. Используйте параметры только для:
 - параметров, которые должны отличаться в зависимости от среды (например, sku, размер или емкость);
 - имен ресурсов, которые вы хотите задать, чтобы упростить идентификацию;
 - значений, которые часто используются для выполнения других задач (например, имя пользователя администратора);
 - секретов (например, паролей);
 - числа или массива значений, используемых при создании нескольких экземпляров типа ресурса.

1. Имена параметров должны указываться по шаблону **camelCasing**.

1. Укажите описание в метаданных для каждого параметра.

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Определите значения по умолчанию для параметров (за исключением паролей и ключей SSH).

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Используйте **securestring** для всех паролей и секретов.

        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
 
1. По возможности следует избегать указания **расположения** с помощью параметра. Вместо этого используйте свойство location группы ресурсов. Ресурсы в шаблоне будут развернуты в том же расположении, что и группа ресурсов, с помощью выражения **resourceGroup().location** для всех ресурсов.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
  
     Если тип ресурса поддерживается в ограниченном наборе расположений, то допустимое расположение можно указать непосредственно в шаблоне. Если вам необходимо использовать параметр расположения, то указывайте его значение для ресурсов, которые могут находиться в одном расположении, насколько это возможно. Так пользователям придется реже указывать расположения для каждого типа ресурса.

1. Избегайте использовать параметр или переменную для версии API для типа ресурса. Свойства и значения ресурса могут отличаться для разных версий. Функции IntelliSense в редакторах кода не удастся определить правильную схему, если версия API будет указана как значение параметра или переменной. Вместо этого жестко задайте версию API в шаблоне.

## Переменные 

1. Применяйте переменные для значений, которые необходимо использовать в шаблоне более одного раза. Если значение используется только один раз, жестко задайте его. Это облегчит чтение шаблона.

1. Нельзя использовать функцию [reference](resource-group-template-functions.md#reference) в разделе переменных. Функция reference получает свое значение из состояния среды выполнения ресурса, а переменные разрешаются при начальной обработке шаблона. Вместо этого сформируйте значения, которым требуется функция **reference**, непосредственно в разделе **resources** или **outputs** шаблона.

1. Добавьте переменные для имен ресурсов, которые должны быть уникальными, как показано в разделе [Имена ресурсов](#resource-names).

1. Переменные можно группировать в составные объекты. Можно использовать ссылку на значение из составного объекта в формате **переменная.дополнительный\_элемент**. Группирование переменных помогает следить за связанными переменными и улучшает читаемость шаблона.

        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
 
     > [AZURE.NOTE] Составной объект не может содержать выражение, которое ссылается на значение из составного объекта. Для этого определите отдельную переменную.

     Более сложные примеры использования составных объектов в качестве переменных доступны в разделе [Совместное использование состояния в шаблонах диспетчера ресурсов Azure](best-practices-resource-manager-state.md).

## Ресурсы

1. Укажите **комментарии** для каждого ресурса в шаблоне, чтобы другим участникам было проще понять назначение этого ресурса.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]

1. Используйте теги для добавления метаданных ресурсов. С их помощью можно указать дополнительные сведения о ресурсах. Например, можно добавить метаданные к ресурсу для пояснения выставления счетов. Дополнительные сведения см. в статье [Использование тегов для организации ресурсов в Azure](resource-group-using-tags.md).

1. Если вы используете в шаблоне **общедоступную конечную точку** (например, общедоступную конечную точку хранилища BLOB-объектов), то **не следует жестко задавать** пространство имен. Используйте функцию **reference** для динамического извлечения пространства имен. Это позволит развернуть шаблон в другом общедоступном пространстве имен, не изменяя конечную точку в шаблоне вручную. Задайте в apiVersion ту же версию, которая указана в storageAccount в шаблоне.

        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Если учетная запись хранения развертывается в том же шаблоне, то при указании ссылки на ресурс нет необходимости указывать пространство имен поставщика. Ниже описывается упрощенный синтаксис.
     
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Если в шаблоне имеются другие значения, для которых настроено общедоступное пространство имен, измените их, указав одну и ту же функцию reference. Пример: свойство storageUri виртуальной машины diagnosticsProfile.

        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
 
     Можно также использовать функцию **reference** для ссылки на учетную запись хранения в другой группе ресурсов.


        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. Назначайте publicIPAddresses виртуальной машине только в том случае, если это требуется для работы приложения. Для подключения с целью отладки, управления или администрирования используйте inboundNatRules, virtualNetworkGateways или jumpbox.

     Дополнительные сведения о подключении к виртуальным машинам можно получить в приведенных ниже статьях.
     - [Running VMs for an N-tier architecture on Azure](./guidance/guidance-compute-3-tier-vm.md) (Выполнение виртуальных машин в N-уровневой архитектуре в Azure)
     - [Настройка доступа WinRM для виртуальных машин в Azure Resource Manager](./virtual-machines/virtual-machines-windows-winrm.md)
     - [Разрешение внешнего доступа к виртуальной машине с помощью портала Azure](./virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
     - [Разрешение внешнего доступа к виртуальной машине с помощью PowerShell](./virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
     - [Открытие портов и конечных точек](./virtual-machines/virtual-machines-linux-nsg-quickstart.md)

1. Свойство **DomainNameLabel** для publicIPAddresses должно быть уникальным. Свойство domainNameLabel должно содержать то 3 до 63 знаков и соответствовать правилам, определенным этим регулярным выражением: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Так как функция uniqueString создаст строку из 13 знаков, то в приведенном ниже примере предполагается, что строка префикса dnsPrefixString уже будет проверена на наличие до 50 знаков и соответствие этим правилам.

        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }

1. При добавлении пароля в **customScriptExtension** используйте свойство **commandToExecute** в protectedSettings.

        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }

     > [AZURE.NOTE] Чтобы обеспечить шифрование секретов, которые передаются как параметры в виртуальные машины или расширения, необходимо использовать свойство protectedSettings соответствующих расширений.

## Выходные данные

Если шаблон создает какое-либо новое значение **publicIPAddresses**, то в нем должен быть раздел **output**, который предоставляет сведения об IP-адресе и полном доменном имени, чтобы их было легко получить после развертывания. При указании ссылки на ресурс используйте версию API, которая была использована для его создания.

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## Отдельный шаблон или вложенные шаблоны

Для развертывания решения можно использовать отдельный шаблон или основной шаблон с несколькими вложенными шаблонами. Вложенные шаблоны обычно используются для более сложных сценариев. Вложенные шаблоны имеют следующие преимущества:

1. можно разложить решение на целевые компоненты;
2. вложенные шаблоны можно повторно использовать в разных основных шаблонах.

Если вы решили разбить структуру шаблона на несколько вложенных шаблонов, то приведенные ниже рекомендации помогут вам стандартизировать ее. Эти рекомендации основаны на документе [Рекомендации по разработке шаблонов диспетчера ресурсов Azure](best-practices-resource-manager-design-templates.md). Рекомендуемая структура состоит из следующих шаблонов.

+ **Основной шаблон** (azuredeploy.json). Используется для входных параметров.
+ **Шаблон общих ресурсов**. Используется для развертывания общих ресурсов, используемых всеми прочими ресурсами (например, виртуальная сеть, группы доступности). Выражение DependsOn обеспечивает развертывание этого шаблона раньше остальных шаблонов.
+ **Шаблон дополнительных ресурсов**. Используется для условного развертывания ресурсов на основе параметра (например, jumpbox).
+ **Шаблон элемента ресурсов**. У каждого типа экземпляра в пределах уровня приложения есть собственная конфигурация. В пределах уровня можно определить разные типы экземпляров (например, первый экземпляр создает новый кластер, а дополнительные экземпляры добавляются в существующий кластер). У каждого типа экземпляра будет свой собственный шаблон развертывания.
+ **Сценарии**. Повторно используемые сценарии, которые могут быть применены к каждому типу экземпляра (например, для инициализации и форматирования дополнительных дисков). Пользовательские сценарии, которые создаются для специальной настройки, отличаются для каждого типа экземпляра.

![вложенный шаблон](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Дополнительные сведения см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

## Условная связь с вложенным шаблоном

Можно установить условную связь с вложенными шаблонами с помощью параметра, который становится частью универсального кода ресурса (URI) для шаблона.

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## Формат шаблона

1. Рекомендуется обработать шаблон проверяющим элементом управления JSON, чтобы удалить лишние запятые, скобки и квадратные скобки, которые могут привести к ошибке во время развертывания. Попробуйте [JSONlint](http://jsonlint.com/) или пакет linter для предпочитаемой среды редактирования (Visual Studio Code, Atom, Sublime Text, Visual Studio и т. д.).
1. Кроме того, рекомендуется отформатировать код JSON для повышения удобочитаемости. Вы можете использовать пакет модуля форматирования данных JSON для своего локального редактора. В Visual Studio документ можно отформатировать, используя клавиши **CTRL+K и CTRL+D**. В Visual Studio Code можно использовать клавиши **ALT+SHIFT+F**. Если ваш локальный редактор не может отформатировать документ, то можно использовать [модуль форматирования данных в Интернете](https://www.bing.com/search?q=json+formatter).

## Дальнейшие действия

1. Указания по разработке архитектуры решения для виртуальных машин см. в разделах [Running a Windows VM on Azure](./guidance/guidance-compute-single-vm.md) (Запуск виртуальной машины Windows в Azure) и [Running a Linux VM on Azure](./guidance/guidance-compute-single-vm-linux.md) (Запуск виртуальной машины Linux в Azure).
2. Указания по настройке учетной записи хранения см. в разделе [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](./storage/storage-performance-checklist.md).
3. Справку по виртуальным сетям можно найти в разделе [Рекомендации по сетевой инфраструктуре](./virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md).

<!---HONumber=AcomDC_0720_2016-->
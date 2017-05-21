---
title: "Рекомендации по созданию шаблонов Resource Manager | Документация Майкрософт"
description: "Рекомендации по упрощению шаблонов Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 24c2b27948e9373bc3957f706ed802cc36c04148
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Рекомендации по созданию шаблонов Azure Resource Manager
Эти рекомендации помогут создавать надежные и удобные в использовании шаблоны Azure Resource Manager. Приведенные здесь указания носят рекомендательный характер и не являются обязательными (если это явно не обозначено). Для вашего сценария может потребоваться использовать один из описанных ниже подходов или примеров.

## <a name="resource-names"></a>Имена ресурсов
Обычно в Resource Manager вы работаете с именами ресурсов трех типов:

* имена ресурсов, которые должны быть уникальными;
* имена ресурсов, которые могут не быть уникальными, но должны помогать в определении ресурса на основе контекста;
* имена ресурсов, которые могут быть универсальными.

Для получения справки по установлению соглашения об именовании ознакомьтесь со статьей [Рекомендации по именованию для инфраструктуры Azure для виртуальных машин Windows](../virtual-machines/windows/infrastructure-naming-guidelines.md). Сведения об ограничениях имен ресурсов см. в статье [Рекомендуемые соглашения об именовании для ресурсов Azure](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Уникальные имена ресурсов
Необходимо указать уникальное имя для любого типа ресурса, который имеет конечную точку доступа к данным. Ниже приведено несколько распространенных типов ресурсов, для которых требуются уникальные имена:

* служба хранилища Azure<sup>1</sup>; 
* веб-приложения службы приложений Azure;
* SQL Server
* Хранилище ключей Azure
* кэш Azure Redis
* Пакетная служба Azure
* Azure Traffic Manager
* поиск Azure;
* Azure HDInsight

<sup>1</sup> Имена учетных записей хранения также должны содержать до 24 знаков в нижнем регистре без дефисов.

Если указать параметр для имени ресурса, при развертывании необходимо указать уникальное имя. При необходимости вы можете создать переменную, которая использует функцию [uniqueString()](resource-group-template-functions-string.md#uniquestring) для создания имени. 

Вам также может потребоваться добавить префикс или суффикс к результату **uniqueString**. Измените уникальное имя, чтобы было проще определить тип ресурса по его имени. Например, можно создать уникальное имя учетной записи хранения с помощью следующей переменной:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Имена ресурсов для идентификации
Для некоторых типов ресурсов необязательно указывать уникальные имена. Вы можете просто указать имя, которое определяет контекст и тип ресурса. Укажите описательное имя, которое поможет определить ресурс в списке ресурсов. Если для различных развертываний требуется использовать разные имена ресурсов, то используйте для имени следующий параметр:

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

Если необходимо передать имя во время развертывания, используйте следующую переменную: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

Вы также можете использовать жестко заданное значение:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>Универсальные имена ресурсов
Для типов ресурсов, к которым часто обращаются через другой ресурс, можно использовать универсальное имя, жестко заданное в шаблоне. Например, можно задать стандартное универсальное имя для правил брандмауэра на SQL Server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>Параметры
Ниже приведены некоторые рекомендации по работе с параметрами.

* Используйте как можно меньше параметров. По возможности используйте переменную или литеральное значение. Используйте параметры только для следующих сценариев:
   
   * параметры, значения которых должны отличаться в зависимости от среды (SKU, размер или емкость);
   * имена ресурсов, которые вы хотите задать, чтобы упростить идентификацию;
   * значения, которые часто используются для выполнения других задач (например, имя пользователя администратора);
   * секреты (например, пароли);
   * числа или массивы значений, используемые при создании нескольких экземпляров типа ресурса.
* Имена для параметров необходимо указывать в нижнем регистре.
* Укажите описание для каждого параметра в метаданных.

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Определите значения по умолчанию для параметров (за исключением паролей и ключей SSH).
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Используйте **securestring** для всех паролей и секретов. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* По возможности не используйте параметр, чтобы указать расположение. Вместо этого используйте свойство **location** группы ресурсов. Чтобы развернуть ресурсы в шаблоне в том же расположении, что и группа ресурсов, используйте выражение **resourceGroup().location** для всех ресурсов.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Если тип ресурса поддерживается в ограниченном наборе расположений, то допустимое расположение необходимо указать непосредственно в шаблоне. Когда вам необходимо использовать параметр **location**, то указывайте его значение для ресурсов, которые могут находиться в одном расположении, насколько это возможно. Так пользователям придется реже указывать информацию о расположении.
* Избегайте использовать параметр или переменную для версии API для типа ресурса. Свойства и значения ресурса могут отличаться для разных версий. Функции IntelliSense в редакторах кода не могут определить правильную схему, если версия API указана в качестве значения параметра или переменной. Вместо этого жестко задайте версию API в шаблоне.

## <a name="variables"></a>Переменные
Ниже приведены некоторые рекомендации по работе с переменными.

* Применяйте переменные для значений, которые необходимо использовать в шаблоне более одного раза. Если значение используется только один раз, жестко задайте его. Это облегчит чтение шаблона.
* Нельзя использовать функцию [reference](resource-group-template-functions-resource.md#reference) в разделе **переменных** шаблона. Функция **reference** получает свое значение из состояния среды выполнения ресурса, а переменные разрешаются при начальной обработке шаблона. Сформируйте значения, которым требуется функция **reference**, непосредственно в разделе **resources** или **outputs** шаблона.
* Добавьте переменные для имен ресурсов, которые должны быть уникальными, как описано в разделе [Имена ресурсов](#resource-names).
* Переменные можно группировать в составные объекты. Используйте формат **переменная.дополнительный_элемент** для ссылки на значение из составного объекта. Группирование переменных помогает следить за связанными переменными и улучшает читаемость шаблона. Ниже приведен пример:
   
   ```json
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
   ```
   
   > [!NOTE]
   > Составной объект не может содержать выражение, которое ссылается на значение из составного объекта. Для этого определите отдельную переменную.
   > 
   > 
   
     Расширенные примеры использования составных объектов в качестве переменных доступны в статье [Передача состояния в шаблоны Azure Resource Manager и из них](best-practices-resource-manager-state.md).

## <a name="resources"></a>Ресурсы
Ниже приведены некоторые рекомендации по работе с ресурсами.

* Чтобы другим участникам было проще понять назначение этого ресурса, укажите **комментарии** для каждого ресурса в шаблоне:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Используйте теги для добавления метаданных в ресурсы. С помощью метаданных можно указать дополнительную информацию о ресурсах. Например, можно добавить метаданные для записи сведений о выставлении счетов ресурса. Дополнительные сведения см. в статье [Использование тегов для организации ресурсов в Azure](resource-group-using-tags.md).
* Если вы используете в шаблоне *общедоступную конечную точку* (например, общедоступную конечную точку хранилища BLOB-объектов Azure), то *не следует жестко задавать* пространство имен. Используйте функцию **reference** для динамического извлечения пространства имен. Вы можете использовать этот подход, чтобы развернуть шаблон в другом общедоступном пространстве имен, не изменяя конечную точку в шаблоне вручную. Задайте для версии API ту же версию, которая указана в учетной записи хранения в вашем шаблоне.
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Если учетная запись хранения развертывается в том же создаваемом шаблоне, то при указании ссылки на ресурс нет необходимости указывать пространство имен поставщика. Вот как выглядит упрощенный синтаксис:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Если в шаблоне имеются другие значения, настроенные для использования общедоступного пространства имен, измените их, указав одну и ту же функцию **reference**. Например, можно задать свойство **storageUri** диагностического профиля виртуальной машины:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Вы также можете использовать функцию reference для ссылки на учетную запись хранения в другой группе ресурсов:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Назначайте общедоступные IP-адреса виртуальной машине только в том случае, если это требуется для работы приложения. Чтобы подключиться к виртуальной машине с целью отладки, управления или администрирования, используйте правила преобразования сетевых адресов для входящих подключений, шлюз виртуальной машины или jumpbox.
   
     Дополнительные сведения о подключении к виртуальным машинам можно получить в приведенных ниже статьях.
   
   * [Run Windows VMs for an N-tier application](../guidance/guidance-compute-n-tier-vm.md) (Запуск виртуальных машин Windows в n-уровневом приложении)
   * [Настройка доступа WinRM для виртуальных машин в Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Открытие портов для виртуальной машины в Azure с помощью портала Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Открытие портов и конечных точек для виртуальной машины в Azure с помощью PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Открытие портов и конечных точек для виртуальной машины Linux с помощью интерфейса командной строки Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* Свойство **DomainNameLabel** для общедоступных IP-адресов должно быть уникальным. Свойство **domainNameLabel** должно содержать то 3 до 63 знаков и соответствовать правилам, определенным этим регулярным выражением: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Так как функция **uniqueString** создает строку длиной 13 знаков, в параметре **dnsPrefixString** можно использовать не более 50 знаков:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* При добавлении пароля в расширение пользовательских скриптов используйте свойство **commandToExecute** в **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Чтобы обеспечить шифрование секретов, которые передаются как параметры в виртуальные машины и расширения, необходимо использовать свойство **protectedSettings** соответствующих расширений.
   > 
   > 

## <a name="outputs"></a>outputs
Если вы используете шаблон, чтобы создать общедоступные IP-адреса, включая раздел **outputs**, который возвращает сведения об IP-адресах и полное доменное имя. С помощью этих выходных данных можно легко получить сведения об общедоступных IP-адресах и полных доменных именах после развертывания. При указании ссылки на ресурс используйте версию API, которая была использована для его создания: 

```json
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

## <a name="single-template-vs-nested-templates"></a>Отдельный шаблон и вложенные шаблоны
Для развертывания решения можно использовать отдельный шаблон или основной шаблон с несколькими вложенными шаблонами. Вложенные шаблоны обычно используются для более сложных сценариев. Использование вложенных шаблонов обеспечивает следующие преимущества:

* вы можете разбить решение на целевые компоненты;
* вложенные шаблоны можно повторно использовать в разных основных шаблонах.

Если вы решили использовать вложенные шаблоны, воспользуйтесь приведенными ниже рекомендациями, чтобы стандартизировать структуру шаблона. Эти рекомендации основаны на статье [Приемы разработки шаблонов Azure Resource Manager при развертывании сложных решений](best-practices-resource-manager-design-templates.md). Мы советуем структуру, которая состоит из следующих шаблонов:

* **Основной шаблон** (azuredeploy.json). Используется для входных параметров.
* **Шаблон общих ресурсов**. Используется для развертывания общих ресурсов, используемых всеми прочими ресурсами (например, виртуальная сеть и группы доступности). Используйте выражение **dependsOn**, чтобы обеспечить развертывание этого шаблона раньше остальных шаблонов.
* **Шаблон дополнительных ресурсов**. Используется для условного развертывания ресурсов на основе параметра (например, jumpbox).
* **Шаблон элемента ресурсов.** У каждого типа экземпляра в пределах уровня приложения есть собственная конфигурация. В пределах уровня можно определить разные типы экземпляров. (Например, первый экземпляр создает кластер, а дополнительные экземпляры добавляются в существующий кластер.) У каждого типа экземпляра есть собственный шаблон развертывания.
* **Сценарии**. Повторно используемые сценарии, которые можно применить к каждому типу экземпляра (например, для инициализации и форматирования дополнительных дисков). Пользовательские скрипты, которые создаются для специальной настройки, отличаются для каждого типа экземпляра.

![Вложенный шаблон](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Дополнительные сведения см. в статье [Использование связанных шаблонов при развертывании ресурсов Azure](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Условная связь с вложенным шаблоном
Вы можете использовать параметр, чтобы установить условную связь с вложенными шаблонами. Параметр становится частью универсального кода ресурса (URI) для шаблона:

```json
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
```

## <a name="template-format"></a>Формат шаблона
Рекомендуется обработать шаблон проверяющим элементом управления JSON, чтобы удалить лишние запятые, скобки и квадратные скобки, которые могут привести к ошибке во время развертывания. Попробуйте [JSONlint](http://jsonlint.com/) или пакет linter для предпочитаемой среды редактирования (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Кроме того, рекомендуется отформатировать код JSON для повышения удобочитаемости. Вы можете использовать пакет модуля форматирования данных JSON для своего локального редактора. В Visual Studio документ можно отформатировать, нажав клавиши **CTRL+K и CTRL+D**. В Visual Studio Code нажмите клавиши **ALT+SHIFT+F**. Если ваш локальный редактор не может отформатировать документ, то можно использовать [модуль форматирования данных в Интернете](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Дальнейшие действия
* Рекомендации по разработке архитектуры решения для виртуальных машин см. в статьях [Run a Windows VM on Azure](../guidance/guidance-compute-single-vm.md) (Запуск виртуальной машины Windows в Azure) и [Run a Linux VM on Azure](../guidance/guidance-compute-single-vm-linux.md) (Запуск виртуальной машины Linux в Azure).
* Рекомендации по настройке учетной записи хранения см. в статье [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](../storage/storage-performance-checklist.md).
* Справку по виртуальным сетям можно найти в статье [Рекомендации по сетевой инфраструктуре Azure для виртуальных машин Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md).
* Сведения об использовании Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md).



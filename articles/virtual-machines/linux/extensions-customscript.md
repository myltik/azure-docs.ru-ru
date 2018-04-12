---
title: Выполнение пользовательских сценариев на виртуальных машинах Linux в Azure | Документация Майкрософт
description: Автоматизируйте задачи настройки виртуальных машин Linux с помощью расширения пользовательских сценариев
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux
Расширение пользовательских сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или другого расположения, доступного из Интернета, или передать в среду выполнения расширения. 

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager. Его также можно запустить с помощью Azure CLI, PowerShell, портала Azure или виртуальных машин Azure REST API.

В этой статье показано, как применять пользовательское расширение сценариев из Azure CLI и как выполнять расширения с помощью шаблона Azure Resource Manager. В этой статье также показаны шаги по устранению неполадок в системах Linux.

## <a name="extension-configuration"></a>Конфигурация расширения
В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager. 

Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

### <a name="public-configuration"></a>Открытая конфигурация
Схема для открытой конфигурации выглядит следующим образом.

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

* **commandToExecute** — (обязательный, строка) выполняемый сценарий точки входа.
* **fileUris** — (необязательный, строковый массив) URL-адреса файлов для скачивания.
* **timestamp** — (необязательный, целое число) метка времени сценария. Измените значение этого поля, только если вы хотите активировать повторное выполнение сценария.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Защищенная конфигурация
Схема защищенной конфигурации выглядит следующим образом.

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

* **commandToExecute** — (необязательный, строка) выполняемый сценарий точки входа. Используйте это поле, если команда содержит секретные данные, например пароли.
* **storageAccountName** — (необязательный, строка) имя учетной записи хранения. Если указаны учетные данные, все значения URI файлов должны иметь формат URL-адресов для больших двоичных объектов Azure.
* **storageAccountKey** — (необязательный, строка) ключ доступа для учетной записи хранения.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure
При использовании Azure CLI для выполнения расширения пользовательских сценариев создайте один или несколько файлов конфигурации. Файлы конфигурации содержат как минимум URI файла и команду выполнения сценария.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

При необходимости можно указать параметры в команде в виде форматированной строки JSON. Это позволяет задать конфигурацию прямо во время выполнения, не используя отдельный файл конфигурации.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Примеры использования интерфейса командной строки Azure

#### <a name="public-configuration-with-script-file"></a>Открытая конфигурация с файлом сценария

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Команда интерфейса командной строки Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Открытая конфигурация без файла сценария

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Команда интерфейса командной строки Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Открытый защищенный файл конфигурации

Используйте открытый файл конфигурации, чтобы задать URI файла сценария. Чтобы задать команду для выполнения, используйте защищенный файл конфигурации.

Файл открытой конфигурации:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Файл защищенной конфигурации:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Команда интерфейса командной строки Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Шаблон Resource Manager
Расширение пользовательских сценариев Azure может выполняться во время развертывания виртуальной машины с использованием шаблона Resource Manager. Для этого добавьте правильно составленный объект JSON в шаблон развертывания.

### <a name="resource-manager-examples"></a>Примеры использования Resource Manager

#### <a name="public-configuration"></a>Открытая конфигурация

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Команда выполнения в защищенной конфигурации

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Полный пример см. в [демонстрации музыкального магазина .NET](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Устранение неполадок
Расширение пользовательских сценариев при выполнении создает или загружает сценарий в каталог, как показано в примере ниже. Выходные данные команды также сохраняются в этот каталог в файлах `stdout` и `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Расширение сценариев Azure создает журнал, который можно найти здесь:

```bash
/var/log/azure/custom-script/handler.log
```

Состояние выполнения для расширения пользовательских сценариев также можно получить с помощью интерфейса командной строки Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Выходные данные выглядят так:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Дополнительная информация
Сведения о других расширениях сценариев для виртуальной машины см. в статье [Обзор расширений и компонентов виртуальных машин под управлением Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


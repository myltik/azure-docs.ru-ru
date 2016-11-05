---
title: Пользовательские сценарии на виртуальных машинах Linux | Microsoft Docs
description: Автоматизируйте задачи настройки виртуальных машин Linux с помощью расширения пользовательских сценариев
services: virtual-machines-linux
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: nepeters

---
# Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux
Расширение пользовательских сценариев загружает и запускает сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно загрузить из службы хранилища Azure или другого расположения, доступного из Интернета, или передать во время выполнения расширения. Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure.

В этом документе объясняется, как использовать расширение пользовательских сценариев с помощью командной строки Azure и шаблона Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Linux.

## Конфигурация расширения
В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager. Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

### Открытая конфигурация
Схема.

* **commandToExecute** — (обязательный, строка) сценарий точки входа
* **fileUris** — (необязательный, строковый массив) URL-адреса файлов для загрузки.
* **timestamp** — (необязательный, целое число) используется только для повторного запуска скрипта при изменении значения этого поля.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### Защищенная конфигурация
Схема.

* **commandToExecute** — (необязательный, строка) сценарий точки входа. Используйте это поле, если команда содержит секретные данные, например пароли.
* **storageAccountName** — (необязательный, строка) имя учетной записи хранения. Если указаны учетные данные, все значения fileUris должны иметь формат URL-адресов для BLOB-объектов Azure.
* **storageAccountKey** — (необязательный, строка) ключ доступа для учетной записи хранения.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## Инфраструктура CLI Azure
Если вы запускаете расширение пользовательских сценариев из интерфейса командной строки Azure, создайте файл или файлы конфигурации, содержащие по меньшей мере универсальный код ресурса (URI) файла и команду выполнения сценария.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Также команду можно выполнить с помощью параметров `--public-config` и `--private-config`, что позволяет задать конфигурацию прямо во время выполнения, не используя отдельный файл конфигурации.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### Примеры использования интерфейса командной строки Azure
**Пример 1** — открытая конфигурация с файлом сценария.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Команда интерфейса командной строки Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Пример 2** — открытая конфигурация без файла сценария.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Команда интерфейса командной строки Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Пример 3** — используется файл открытой конфигурации для указания универсального кода ресурса (URI) файла сценария и файл защищенной конфигурации для указания выполняемой команды.

Файл открытой конфигурации:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Файл защищенной конфигурации:

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Команда интерфейса командной строки Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## Шаблон Resource Manager
Расширение пользовательских сценариев Azure может выполняться во время развертывания виртуальной машины с использованием шаблона Resource Manager. Для этого добавьте правильно составленный объект JSON в шаблон развертывания.

### Примеры использования Resource Manager
**Пример 1** — открытая конфигурация.

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

**Пример 2** — команда выполнения в защищенной конфигурации.

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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Готовый пример демонстрационной версии музыкального магазина .NET Core см. [здесь](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## Устранение неполадок
Расширение пользовательских сценариев при выполнении создает или загружает сценарий в каталог, как показано в примере ниже. Выходные данные команды также сохраняются в этот каталог в файлах `stdout` и `stderr`.

```none
/var/lib/azure/custom-script/download/0/
```

Расширение сценариев Azure создает журнал, который можно найти здесь.

```none
/var/log/azure/customscript/handler.log
```

Состояние выполнения для расширения пользовательских сценариев также можно получить с помощью интерфейса командной строки Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

Выходные данные выглядят так:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## Дальнейшие действия
Сведения о других расширениях сценариев для виртуальной машины см. в статье [Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md).

<!---HONumber=AcomDC_0928_2016-->
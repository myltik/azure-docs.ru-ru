---
title: Выполнение пользовательских сценариев на виртуальных машинах Linux в Azure | Документация Майкрософт
description: Автоматизируйте задачи настройки виртуальных машин Linux с помощью расширения настраиваемых скриптов версии 1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: eac64a5b456eb040bcb1ac01c3c86dfde0847e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944926"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Использование расширения настраиваемых скриптов Azure версии 1 на виртуальных машинах Linux
Расширение настраиваемых скриптов версии 1 скачивает и выполняет скрипты на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или другого расположения, доступного из Интернета, или передать в среду выполнения расширения. 

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager. Его также можно запустить с помощью Azure CLI, PowerShell, портала Azure или виртуальных машин Azure REST API.

В этой статье показано, как применять пользовательское расширение сценариев из Azure CLI и как выполнять расширения с помощью шаблона Azure Resource Manager. В этой статье также показаны шаги по устранению неполадок в системах Linux.


Существует два расширения настраиваемых скриптов Linux:
* Версия 1 — Microsoft.OSTCExtensions.CustomScriptForLinux
* Версия 2 — Microsoft.Azure.Extensions.CustomScript

Переводите новые и существующие развертывания на использование новой версии ([Microsoft.Azure.Extensions.CustomScript](\custom-script-linux.md)). Новая версия рассчитана на то, чтобы стать готовой заменой. Таким образом, миграция выполняется так же легко, как и изменение имени и версии, при этом изменять конфигурацию расширения не требуется.

 

### <a name="operating-system"></a>Операционная система
Поддерживаемые дистрибутивы Linux:

- CentOS 6.5 и более поздние версии
- Debian 8 и более поздние версии
    - Debian 8.7 не поставляется с Python2 в последних образах, что приводит к нарушению CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13.1 и более поздние версии
- Oracle Linux 6.4 и более поздние версии
- SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3) и более поздние версии
- Ubuntu 12.04 и более поздние версии

### <a name="script-location"></a>Расположение сценария

Вы можете использовать расширение, чтобы получить доступ к хранилищу BLOB-объектов Azure с помощью учетных данных службы хранилища BLOB-объектов Azure. Кроме того, скрипт может находиться в любом расположении при условии, что виртуальная машина может осуществлять маршрутизацию в эту конечную точку, такую как GitHub, внутренний файловый сервер и т. д.

### <a name="internet-connectivity"></a>Подключение к Интернету
Чтобы скачать скрипт извне, например с сайта GitHub или хранилища Azure, необходимо открыть дополнительные порты брандмауэра или группы безопасности сети. Например, если скрипт находится в хранилище Azure, вы можете разрешить доступ с помощью тегов службы Azure NSG для [хранилища](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Если скрипт расположен на локальном сервере, вам по-прежнему может потребоваться открыть дополнительные порты брандмауэра или группы безопасности сети.

### <a name="tips-and-tricks"></a>Советы и рекомендации
* Высокий процент сбоев данного расширения связан с синтаксическими ошибками в скрипте. Чтобы упростить поиск места отказа, протестируйте запуски скрипта без ошибок и включите в скрипт дополнительные возможности ведения журнала.
* Пишите скрипты, которые являются идемпотентными, чтобы их случайные повторные запуски не приводили к изменениям системы.
* Выполняемые скрипты не должны запрашивать ввод данных пользователем.
* На выполнение скрипта отводится 90 минут. Более продолжительное действие вызовет сбой подготовки расширения.
* Не следует помещать перезагрузки в скрипт, так как будут возникать проблемы с другими устанавливаемыми расширениями, и после перезагрузки расширение прекратит работать. 
* Если у вас есть скрипт, приводящий к перезагрузке, установите приложения и запустите скрипты и т. д. Необходимо запланировать перезагрузку с помощью задания Cron или таких средств, как расширения DSC, Chef, Puppet.
* Расширение будет запускать скрипт только один раз. Чтобы запускать скрипт при каждой загрузке, можно воспользоваться [образом cloud-init](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) и модулем [скриптов при загрузке](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Кроме того, можно использовать скрипт для создания единицы службы Systemd.
* Чтобы запланировать время выполнения скрипта, необходимо создать задание Cron с помощью расширения. 
* Во время выполнения скрипта вы увидите на портале Microsoft Azure или в CLI только переходное состояние расширения. Если требуются более частые обновления состояния выполняющегося скрипта, следует создать собственное решение.
* Расширение настраиваемых скриптов не имеет собственной поддержки прокси-серверов, однако можно использовать средство передачи файлов, которое поддерживает прокси-серверы в скрипте, например *Curl*. 
* Следует учитывать настраиваемые расположения каталогов, которые могут использоваться скриптами или командами, и иметь в распоряжении логику для обработки таких случаев.



## <a name="extension-schema"></a>Схема расширения

В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager. 

Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

Эти элементы следует рассматривать в качестве конфиденциальных данных. Их нужно задать в защищенной конфигурации параметров расширений. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине.

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных | 
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | дата |
| publisher | Microsoft.OSTCExtensions | строка |
| Тип | CustomScriptForLinux | строка |
| typeHandlerVersion | 1.5 | int |
| fileUris (пример) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (пример) | python MyPythonScript.py <my-param1> | строка |
| enableInternalDNSCheck | Да | Логическое |
| storageAccountName (пример) | examplestorageacct | строка |
| storageAccountKey (пример) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | строка |

### <a name="property-value-details"></a>Сведения о значении свойства
* `fileUris`: (необязательное, массив строк) список URI скриптов.
* `enableInternalDNSCheck`: (необязательное, логическое) по умолчанию имеет значение True; установите значение False, чтобы отключить проверку DNS.
* `commandToExecute`: (необязательное, строка) выполняемый скрипт точки входа.
* `storageAccountName`: (необязательное, строка) имя учетной записи хранения.
* `storageAccountKey`: (необязательное, строка) ключ доступа для учетной записи хранения.

Следующие значения можно задавать либо в открытых, либо в защищенных параметрах. Их нельзя указывать в параметрах этих типов одновременно.
* `commandToExecute`

Открытые параметры могут быть полезны для отладки, однако настоятельно рекомендуется использовать защищенные параметры.

Открытые параметры отправляются в виде открытого текста в виртуальную машину, где будет выполняться скрипт.  Защищенные параметры шифруются с помощью ключа, который известен только Azure и виртуальной машине. Параметры сохраняются на виртуальной машине в том виде, в каком они были отправлены, т. е. если параметры были зашифрованы, они сохраняются на виртуальной машине зашифрованными. Сертификат, используемый для расшифровки зашифрованных значений, хранится на виртуальной машине и применяется для расшифровки параметров (при необходимости) во время выполнения.


## <a name="template-deployment"></a>Развертывание шаблона
Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения пользовательских сценариев во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. 


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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

## <a name="azure-cli"></a>Инфраструктура CLI Azure
При использовании Azure CLI для выполнения расширения пользовательских сценариев создайте один или несколько файлов конфигурации. Как минимум, требуется "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

При необходимости можно указать параметры в команде в виде форматированной строки JSON. Это позволяет задать конфигурацию прямо во время выполнения, не используя отдельный файл конфигурации.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Примеры использования интерфейса командной строки Azure

#### <a name="public-configuration-with-no-script-file"></a>Открытая конфигурация без файла сценария

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Команда интерфейса командной строки Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Открытый защищенный файл конфигурации

Используйте открытый файл конфигурации, чтобы задать URI файла сценария. Чтобы задать команду для выполнения, используйте защищенный файл конфигурации.

Файл открытой конфигурации:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Файл защищенной конфигурации:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Команда интерфейса командной строки Azure:

```azurecli
az vm extension set 
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Устранение неполадок
Расширение пользовательских сценариев при выполнении создает или загружает сценарий в каталог, как показано в примере ниже. Выходные данные команды также сохраняются в этот каталог в файлах `stdout` и `stderr`. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Чтобы приступить к устранению неполадок, сначала просмотрите журнал агента Linux, убедитесь, что расширение запускалось, проверьте:

```bash
/var/log/waagent.log 
```

Необходимо обратить внимание на выполнение расширения. Оно будет выглядеть примерно так:
```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```
Некоторые замечания
1. Enable — означает момент запуска команды.
2. Download — относится к скачиванию пакета расширения CustomScript из Azure, а не файлов скрипта, указанных в fileUris.
3. Вы также видите, в какой файл журнала осуществляется запись, — /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log.

Далее нужно проверить файл журнала, имеющий следующий формат:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Необходимо обратить внимание на отдельное выполнение. Оно будет выглядеть примерно так:
```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
```
Здесь отображаются следующие компоненты:
* команда Enable, запускающая этот журнал;
* параметры, переданные в расширение;
* расширение, скачивающее файл, и результат скачивания;
* выполняемая команда и результат выполнения.

Состояние выполнения для расширения пользовательских сценариев также можно получить с помощью интерфейса командной строки Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Выходные данные выглядят так:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Дополнительная информация
Код, текущие проблемы и версии доступны в [репозитории расширения CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).


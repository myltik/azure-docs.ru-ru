---
title: Расширения и компоненты виртуальных машин под управлением Linux | Документация Майкрософт
description: Узнайте о расширениях, доступных для виртуальных машин Azure. Расширения сгруппированы по предоставляемым функциям или улучшениям.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 47cc812f9dac606cf4f69df9eff6d48095eb6ef8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Обзор расширений и компонентов виртуальных машин под управлением Linux

Расширения виртуальных машин Azure — это небольшие приложения, которые выполняют задачи настройки и автоматизации после развертывания виртуальных машин Azure. Например, если на виртуальной машине нужно установить программное обеспечение, настроить антивирусную защиту или конфигурацию Docker, это можно сделать с помощью расширения виртуальной машины (далее — расширение ВМ). Расширения ВМ Azure можно запускать с помощью интерфейса командной строки Azure, PowerShell, шаблонов Azure Resource Manager или портала Azure. Расширения могут предоставляться в рамках нового развертывания виртуальной машины и работать в любой из существующих систем.

Этот документ содержит обзор расширений ВМ Azure, предварительные требования для их использования, рекомендации по их обнаружению и удалению, а также советы по управлению ими. Этот документ содержит только обобщенные сведения, так как существует множество расширений виртуальной машины с разными параметрами настройки. Информацию о нужном расширении можно найти в соответствующей документации.

## <a name="use-cases-and-samples"></a>Варианты использования и примеры

Существует несколько разных расширений ВМ Azure, которые используются в определенных сценариях. Ниже приведены некоторые примеры.

- Применение к виртуальной машине конфигураций требуемого состояния PowerShell с помощью расширения DSC для Linux. Подробнее см. [Общие сведения об обработчике расширения Desired State Configuration в Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Настройка мониторинга виртуальной машины с помощью расширения ВМ Microsoft Monitoring Agent. Дополнительные сведения см. в статье [Мониторинг виртуальных машин Linux в Azure](tutorial-monitoring.md).
- настройка мониторинга инфраструктуры Azure с помощью расширения Datadog. Дополнительную информацию см. в [блоге Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Настройка узла Docker на виртуальной машине Azure с помощью расширения Docker. Дополнительные сведения см. в статье [Использование расширения виртуальной машины Docker для развертывания среды](dockerextension.md).

Помимо расширений, созданных для конкретных процессов, существует расширение пользовательских сценариев для виртуальных машин под управлением Windows и Linux. Расширение пользовательских сценариев для Linux позволяет запустить на виртуальной машине любой сценарий Bash. Пользовательские сценарии могут пригодиться при проектировании развертывания Azure, для которого требуется дополнительная настройка, ее невозможно выполнить собственными средствами Azure. Дополнительные сведения см. в статье [Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux](extensions-customscript.md).


## <a name="prerequisites"></a>предварительным требованиям

Для каждого расширения ВМ могут существовать свои требования. Например, для расширения Docker требуется поддерживаемый дистрибутив Linux. Индивидуальные требования для каждого расширения подробно описаны в соответствующей документации.

### <a name="azure-vm-agent"></a>Агент виртуальной машины Azure

Агент виртуальной машины Azure управляет взаимодействием виртуальной машины с контроллером структуры Azure. Агент отвечает за многие функциональные аспекты развертывания виртуальных машин Azure и управления ими, включая запуск расширений ВМ. Агент виртуальной машины Azure предварительно установлен во всех образах Azure Marketplace. Также его можно установить вручную в поддерживаемых операционных системах.

Сведения о поддерживаемых операционных системах и инструкции по установке см. в статье [Обзор агента и расширений виртуальной машины](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Поиск расширений ВМ

Существует множество различных расширений ВМ, которые можно использовать с виртуальными машинами Azure. Чтобы просмотреть полный список, выполните в интерфейсе командной строки Azure приведенную ниже команду, указав собственное расположение.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Запуск расширений ВМ

Расширения ВМ Azure можно запускать на существующих виртуальных машинах. Это можно использовать для изменения конфигурации или восстановления подключения на уже развернутой виртуальной машине. Также расширения можно использовать при развертывании с помощью шаблонов Azure Resource Manager. Используя расширения вместе с шаблонами Resource Manager, можно так развернуть и настроить виртуальные машины Azure, чтобы не после развертывания не требовались дополнительные действия.

Запустить расширение на существующей виртуальной машине можно несколькими способами.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Расширения ВМ Azure можно запускать на существующих виртуальных машинах с помощью команды `az vm extension set`. В этом примере на виртуальной машине выполняется расширение пользовательского сценария.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Выходные данные должны быть похожи на приведенные ниже.

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Портал Azure

Расширения ВМ можно устанавливать на существующие виртуальные машины с помощью портала Azure. Для этого выберите виртуальную машину, выберите **Расширения** и щелкните **Добавить**. Выберите нужное расширение из списка доступных расширений и следуйте инструкциям мастера.

На рисунке ниже показана установка расширения пользовательских сценариев для Linux с помощью портала Azure.

![Установка расширения пользовательских сценариев](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Расширения ВМ можно добавлять в шаблоны Azure Resource Manager. Так они смогут выполняться при развертывании соответствующего шаблона. Развертывая расширение вместе с шаблоном, можно создать полностью настроенное развертывание Azure. Например, ниже представлен код JSON из шаблона Resource Manager. Шаблон развертывает набор виртуальных машин с балансировкой нагрузки и базу данных SQL Azure, а затем устанавливает на каждой виртуальной машине приложение .NET Core. Расширение ВМ отвечает за установку программного обеспечения.

Дополнительные сведения вы можете получить, ознакомившись с полным [шаблоном Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Дополнительные сведения см. в статье [Создание шаблонов Azure Resource Manager](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Защита данных в расширениях ВМ

При работе с расширением ВМ может использоваться конфиденциальная информация, например учетные данные, имена учетных записей хранения или ключи доступа к учетной записи хранения. Многие расширения ВМ поддерживают защищенную конфигурацию, при использовании которой данные хранятся в зашифрованном виде и расшифровываются только на целевой виртуальной машине. Каждое расширение имеет собственную схему защищенной конфигурации, которая описывается в документации по этим расширениям.

В следующем примере показан экземпляр расширения пользовательских сценариев для Linux. Обратите внимание, что команда для выполнения расширения содержит набор учетных данных. В этом примере выполняемая команда не шифруется.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Переместив свойство **commandToExecute** в конфигурацию **protected**, вы защитите данные из строки выполнения.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Устранение неполадок с расширениями ВМ

Для каждого расширения ВМ предусмотрена своя процедура устранения неполадок. Например, если вы используете расширение пользовательских сценариев, сведения о выполнении сценария можно найти на той виртуальной машине, на которой запускалось расширение. Действия по устранению неполадок для конкретных расширений описаны в документации по соответствующим расширениям.

Описанные ниже действия по устранению неполадок относятся ко всем расширения ВМ.

### <a name="view-extension-status"></a>Просмотр состояния расширения

После того, как расширение ВМ будет выполнено на виртуальной машине, с помощью следующей команды Azure CLI можно получить состояние расширения. Замените в примере имена-параметры собственными значениями.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выглядят так:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Кроме того, состояние выполнения расширения можно найти на портале Azure. Чтобы просмотреть состояние расширения, выберите виртуальную машину, затем щелкните **Расширения** и найдите нужное расширение.

### <a name="rerun-a-vm-extension"></a>Повторный запуск расширения ВМ

В некоторых случаях может потребоваться повторный запуск расширения ВМ. Можно сначала удалить расширение, затем повторно запустить его любым удобным способом. Чтобы удалить расширение, выполните в Azure CLI следующую команду: Замените в примере имена-параметры собственными значениями.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Кроме того, расширение можно удалить через портал Azure. Для этого выполните на портале следующие действия:

1. Выберите виртуальную машину.
2. Выберите **Расширения**.
3. Выберите нужное расширение.
4. Выберите **Удалить**.

## <a name="common-vm-extension-reference"></a>Справочные материалы для всех расширений ВМ
| Имя расширения | ОПИСАНИЕ | Дополнительные сведения |
| --- | --- | --- |
| Расширение пользовательских сценариев для Linux |Выполняет сценарии на виртуальных машинах Azure. |[Расширение пользовательских сценариев для Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Расширение Docker |Устанавливает управляющую программу Docker для поддержки удаленных команд Docker. |[Расширение виртуальной машины Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Расширение для доступа к виртуальной машине |Восстанавливает доступ к виртуальной машине Azure. |[Расширение для доступа к виртуальной машине](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Расширение системы диагностики Azure |Управляет системой диагностики Azure. |[Расширение системы диагностики Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Расширение Azure VM Access |Управляет пользователями и учетными данными. |[Расширение VM Access для Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

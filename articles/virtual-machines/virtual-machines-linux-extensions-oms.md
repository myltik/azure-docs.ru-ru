---
title: "Расширение виртуальной машины OMS Azure для Linux | Документация Майкрософт"
description: "Разверните агент OMS на виртуальной машине Linux, используя расширение виртуальной машины."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины OMS для Linux

## <a name="overview"></a>Обзор

Operations Management Suite (OMS) предоставляет возможности мониторинга, оповещений и внесения исправлений в соответствии с оповещениями для облачных и локальных ресурсов. Расширение виртуальной машины агента OMS для Linux опубликовано и поддерживается корпорацией Майкрософт. Это расширение устанавливает агент OMS на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области OMS. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины OMS для Linux.

Общие сведения о расширениях виртуальной машины Azure см. в статье [Обзор расширений и компонентов виртуальной машины](./virtual-machines-linux-extensions-features.md).

Дополнительные сведения об Operations Management Suite см. на странице с [обзором Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Расширение агента OMS можно выполнять с использованием следующих дистрибутивов Linux.

| Дистрибутив | Version (версия) |
|---|---|
| CentOS Linux | 5,6 и 7 |
| Oracle Linux | 5,6 и 7 |
| Сервер Red Hat Enterprise Linux | 5,6 и 7 |
| Debian GNU/Linux | 6, 7 и 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04 |
| SUSE Linux Enterprise Server | 11 и 12 |

### <a name="connectivity"></a>Соединение

Расширение агента OMS для Linux требует, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-configuration"></a>Конфигурация расширения

Расширение виртуальной машины агента OMS для Linux требует идентификатор и ключ из целевой рабочей области OMS. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, он хранится в защищенной конфигурации. Данные защищенной конфигурации расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Открытые и закрытые конфигурации задаются во время развертывания, что описывается в следующих разделах этого документа.

### <a name="public-configuration"></a>Открытая конфигурация

Схема для открытой конфигурации:

- workspaceId: (требуется, строка) идентификатор рабочей области OMS, в которую внедряется виртуальная машина.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Закрытая конфигурация

Схема для открытой конфигурации:

- workspaceKey: (требуется, строка) общий первичный или вторичный ключ рабочей области.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих настройки после развертывания, например внедрения в OMS. Пример шаблона Resource Manager, включающего в себя расширение виртуальной машины агента OMS, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Этот пример можно развернуть из документа, используя эту кнопку:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

JSON-файл, используемый для развертывания расширения виртуальной машины агента OMS, выглядит следующим образом:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Azure CLI можно использовать для развертывания расширения виртуальной машины агента OMS на существующей виртуальной машине. Перед этим создайте файл public.json и protected.json. Схема для этих файлов описана ранее в этом документе.

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
azure vm extension get myResourceGroup myVM
```

Выходные данные выполнения расширения регистрируются в следующем файле:

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/en-us/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->



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
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 138fc8c98ea6f409b28407b20851c96ecc618b09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины OMS для Linux

## <a name="overview"></a>Обзор

Operations Management Suite (OMS) предоставляет возможности мониторинга, оповещений и внесения исправлений в соответствии с оповещениями для облачных и локальных ресурсов. Расширение виртуальной машины агента OMS для Linux опубликовано и поддерживается корпорацией Майкрософт. Это расширение устанавливает агент OMS на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области OMS. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины OMS для Linux.

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>операционная система

Расширение агента OMS можно выполнять с использованием следующих дистрибутивов Linux.

| Дистрибутив | Version (версия) |
|---|---|
| CentOS Linux | 5, 6 и 7 |
| Oracle Linux | 5, 6 и 7 |
| Сервер Red Hat Enterprise Linux | 5, 6 и 7 |
| Debian GNU/Linux | 6, 7 и 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 и 12 |

### <a name="internet-connectivity"></a>Подключение к Интернету

Расширение агента OMS для Linux требует, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем объекте JSON показана схема для расширения агента OMS. Расширение требует идентификатор и ключ из целевой рабочей области OMS, которые находятся на портале OMS. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, его следует хранить в защищенной конфигурации параметров. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Обратите внимание, что в **workspaceId** и **workspaceKey** учитывается регистр знаков.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Значения свойств

| Имя | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceID (пример) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (пример) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих настройки после развертывания, например внедрения в OMS. Пример шаблона Resource Manager, включающего в себя расширение виртуальной машины агента OMS, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Конфигурацию JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение конфигурации JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/resource-manager-template-child-resource.md). 

В следующем примере предполагается, что расширение OMS вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в объект `"resources": []` виртуальной машины.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

При размещении JSON расширения в корне шаблона имя ресурса содержит ссылку на родительскую виртуальную машину, а тип отражает вложенную конфигурацию.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
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

Azure CLI можно использовать для развертывания расширения виртуальной машины агента OMS на существующей виртуальной машине. Замените ключ OMS и идентификатор OMS значениями из рабочей области OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Коды ошибок и их описание

| Код ошибки | Значение | Возможное действие |
| :---: | --- | --- |
| 10 | Виртуальная машина уже подключена к рабочей области OMS | Для подключения виртуальной машины к рабочей области, указанной в схеме расширения, задайте для stopOnMultipleConnections значение false в общих параметрах или удалите это свойство. Счет для этой виртуальной машины выставляется за каждую рабочую область, к которой она подключена. |
| 11 | Для расширения предоставлена недопустимая конфигурация | Изучите приведенные выше примеры, чтобы задать все значения свойств, необходимые для развертывания. |
| 12 | Диспетчер пакетов dpkg заблокирован | Убедитесь, что все операции обновления dpkg на компьютере завершились и повторите попытку. |
| 20 | Преждевременный вызов операции включения | [Обновите агент Azure Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) до новейшей версии. |
| 51 | Это расширение не поддерживается в операционной системе виртуальной машины | |
| 55 | Не удается подключиться к службе Microsoft Operations Management Suite | Убедитесь, что система имеет доступ к Интернету или что предоставлен допустимый прокси-сервер HTTP. Кроме того, проверьте правильность идентификатора рабочей области. |

Дополнительные сведения об устранении неполадок см. в [руководстве по устранению неполадок агента OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/en-us/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).

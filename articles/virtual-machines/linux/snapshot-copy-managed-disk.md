---
title: "Создание моментального снимка виртуального жесткого диска в Azure | Документация Майкрософт"
description: "Узнайте, как создать копию виртуального жесткого диска (VHD) в Azure в качестве резервной копии или для устранения неполадок."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: da00c48f7da5a9be146f4fdb626c93db746c0f9b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Создание моментального снимка 

Создание моментального снимка операционной системы или виртуального жесткого диска данных для резервного копирования или устранения неполадок с виртуальной машиной. Моментальный снимок — это полная копия виртуального жесткого диска, доступная только для чтения. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Создание моментального снимка с помощью Azure CLI 2.0

В следующем примере требуется, чтобы вы установили Azure CLI 2.0 и выполнили вход в систему с учетной записью Azure. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Ниже показано, как создать моментальный снимок, выполнив команду `az snapshot create` с параметром `--source-disk`. В приведенном примере предполагается, что существует виртуальная машина `myVM` с управляемым диском ОС, созданная в группе ресурсов `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

Результат должен выглядеть следующим образом.

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Создание моментального снимка с помощью портала Azure 

1. Войдите на [портал Azure](https://portal.azure.com).
2. В левом верхнем углу щелкните **Создать** и найдите **моментальный снимок**.
3. В колонке "Моментальный снимок" щелкните **Создать**.
4. Заполните поле **Имя** для моментального снимка.
5. Выберите существующую [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#resource-groups) или укажите имя, чтобы создать новую. 
6. Выберите расположение центра обработки данных Azure.  
7. В поле **Исходный диск** выберите управляемый диск, моментальный снимок которого необходимо создать.
8. Выберите **тип учетной записи**, которая будет использоваться для хранения моментального снимка. Мы рекомендуем тип **Standard_LRS**, если вам не требуется хранить моментальный снимок на высокопроизводительном диске.
9. Щелкните **Создать**.

Если вы планируете использовать моментальный снимок, чтобы создать управляемый диск и подключить его к виртуальной машине, которая должна быть высокопроизводительной, используйте параметр `--sku Premium_LRS` в команде `az snapshot create`. Это позволит создать моментальный снимок таким образом, чтобы он хранился в качестве управляемого диска уровня "Премиум". Управляемые диски уровня "Премиум" работают быстрее, так как это твердотельные накопители (SSD), однако их использование обойдется дороже, чем диски уровня "Стандартный" (жесткие диски).


## <a name="next-steps"></a>Дальнейшие действия

 Создайте виртуальную машину из моментального снимка, создав из него управляемый диск, а затем подключив этот диск как диск ОС. См. дополнительные сведения о скрипте для [создания виртуальной машины из моментального снимка](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).


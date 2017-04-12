---
title: "Копирование управляемого диска Azure для архивации | Документация Майкрософт"
description: "Узнайте, как создать копию управляемого диска Azure, используемого для архивации или устранения неполадок с дисками."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 22013ec8e5531a2f61d811300bce016fcde5ab86
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Создание копии виртуального жесткого диска, хранящегося в виде управляемого диска Azure, с помощью управляемых моментальных снимков
Создайте моментальный снимок управляемого диска для архивации или создайте управляемый диск на основе моментального снимка и подключите его к тестовой виртуальной машине для устранения неполадок. Управляемый моментальный снимок — это полная копия управляемого диска виртуальной машины на определенный момент времени. Он создает копию виртуального жесткого диска только для чтения и по умолчанию сохраняет ее в качестве управляемого диска уровня "Стандартный". 

Дополнительные сведения о ценах см. на странице [с ценами на службу хранилища Azure](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Чтобы создать моментальный снимок управляемого диска, используйте портал Azure или Azure CLI 2.0.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Создание моментального снимка с помощью Azure CLI 2.0

> [!NOTE] 
> В следующем примере требуется, чтобы вы установили Azure CLI 2.0 и выполнили вход в систему с учетной записью Azure.

Ниже показано, как получить и создать моментальный снимок управляемого диска ОС с помощью команды `az snapshot create` с параметром `--source-disk`. В приведенном примере предполагается, что существует виртуальная машина `myVM` с управляемым диском ОС, созданная в группе ресурсов `myResourceGroup`.

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




---
title: Создание моментального снимка виртуального жесткого диска в Azure | Документация Майкрософт
description: Узнайте, как создать копию виртуального жесткого диска (VHD) в Azure в качестве резервной копии или для устранения неполадок.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318907"
---
# <a name="create-a-snapshot"></a>Создание моментального снимка 

Создайте моментальный снимок операционной системы или диска данных для резервного копирования или устранения неполадок с виртуальной машиной. Моментальный снимок — это полная копия виртуального жесткого диска, доступная только для чтения. 

## <a name="use-azure-cli"></a>Использование интерфейса командной строки Azure 

В следующем примере требуется, чтобы вы установили Azure CLI 2.0 и выполнили вход в систему с учетной записью Azure. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Ниже показано, как создать моментальный снимок, выполнив команду `az snapshot create` с параметром `--source-disk`. В приведенном примере предполагается, что в группе ресурсов `myResourceGroup` существует виртуальная машина `myVM`.

Получите идентификатор диска.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Создайте моментальный снимок с именем *osDisk-backup*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Перед сохранением моментального снимка в хранилище, избыточном в пределах зоны, его необходимо создать в регионе, который поддерживает [зоны доступности](../../availability-zones/az-overview.md) и в котором включен параметр `--sku Standard_ZRS`.

## <a name="use-azure-portal"></a>Использование портала Azure 

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левом верхнем углу щелкните **Создать ресурс** и выполните поиск фразы **моментальный снимок**.
3. В колонке "Моментальный снимок" щелкните **Создать**.
4. Заполните поле **Имя** для моментального снимка.
5. Выберите существующую [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#resource-groups) или укажите имя, чтобы создать новую. 
6. Выберите расположение центра обработки данных Azure.  
7. В поле **Исходный диск** выберите управляемый диск, моментальный снимок которого необходимо создать.
8. Выберите **тип учетной записи**, которая будет использоваться для хранения моментального снимка. Мы рекомендуем тип **Standard_LRS**, если вам не требуется хранить моментальный снимок на высокопроизводительном диске.
9. Нажмите кнопку **Создать**.


## <a name="next-steps"></a>Дополнительная информация

 Создайте виртуальную машину из моментального снимка, создав из него управляемый диск, а затем подключив этот диск как диск ОС. См. дополнительные сведения о скрипте для [создания виртуальной машины из моментального снимка](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).


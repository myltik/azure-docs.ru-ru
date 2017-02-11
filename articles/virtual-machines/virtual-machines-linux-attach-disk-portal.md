---
title: "Подключение диска данных к виртуальной машине Linux | Документация Майкрософт"
description: "Подключение нового или существующего диска данных к виртуальной машине Linux на портале Azure с помощью модели развертывания на основе диспетчера ресурсов."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Подключение диска данных к виртуальной машине Linux на портале Azure
В этой статье показано, как подключить новый и существующий диски к виртуальной машине Linux на портале Azure. Вы также можете [подключить диск данных к виртуальной машине Windows на портале Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Перед этим ознакомьтесь со следующими советами:

* Размер виртуальной машины определяет, сколько дисков данных к ней можно подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Для использования хранилища Premium вам необходимо будет использовать виртуальную машину серии DS или GS. Эти виртуальные машины позволяют использовать диски из учетных записей хранения Premium и Standard. Хранилище Premium доступно в определенных регионах. Дополнительные сведения см. в разделе [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../storage/storage-premium-storage.md).
* На самом деле диски, подключенные к виртуальным машинам, — это VHD-файлы в учетной записи хранения Azure. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Если вы подключаете новый диск, его не надо предварительно создавать. Azure создаст его при подключении.
* Если вы подключаете существующий диск, его VHD-файл должен быть доступен в учетной записи хранения Azure. Можно использовать VHD, уже находящийся там, если он не подключен к другой виртуальной машине, или передать свой VHD-файл в учетную запись хранения.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия
После добавления диска его необходимо подготовить для использования. См. раздел "Практическое руководство. Инициализация нового диска данных в Linux" для операционной системы виртуальной машины в этой [статье](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO3-->



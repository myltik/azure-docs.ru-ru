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
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7c481cfae5f97b71c0ab184419ceaa46ab3f5a5b
ms.openlocfilehash: 394d82e444bdbc8b07243d92743ceb660f142509


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Подключение диска данных к виртуальной машине Linux на портале Azure
В этой статье показано, как подключить новый и существующий диски к виртуальной машине Linux на портале Azure. Вы также можете [подключить диск данных к виртуальной машине Windows на портале Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Перед этим ознакомьтесь со следующими советами:

* Размер виртуальной машины определяет, сколько дисков данных к ней можно подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Для использования хранилища Premium вам необходимо будет использовать виртуальную машину серии DS или GS. Эти виртуальные машины позволяют использовать диски из учетных записей хранения Premium и Standard. Хранилище Premium доступно в определенных регионах. Дополнительные сведения см. в разделе [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* На самом деле диски, подключенные к виртуальным машинам, — это VHD-файлы в учетной записи хранения Azure. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Если вы подключаете новый диск, его не надо предварительно создавать. Azure создаст его при подключении.
* Если вы подключаете существующий диск, его VHD-файл должен быть доступен в учетной записи хранения Azure. Можно использовать VHD, уже находящийся там, если он не подключен к другой виртуальной машине, или передать свой VHD-файл в учетную запись хранения.


## <a name="find-the-virtual-machine"></a>Поиск виртуальной машины
1. Войдите на [портал Azure](https://portal.azure.com/).
2. В главном меню щелкните **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. В колонке "Виртуальные машины" в разделе **Основные компоненты** щелкните **Диски**.
   
    ![Открытие параметров диска](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Продолжайте, следуя указаниям по подключению [нового](#option-1-attach-a-new-disk) или [существующего диска](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-a-new-disk"></a>Вариант 1. Подключение нового диска
1. В колонке **Диски** щелкните **Подключить новый**.
2. Просмотрите параметры по умолчанию, при необходимости измените их, затем нажмите кнопку **ОК**.
   
   ![Просмотр параметров диска](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. После того как Azure создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.

## <a name="option-2-attach-an-existing-disk"></a>Вариант 2. Подключение существующего диска
1. В колонке **Диски** щелкните **Подключить существующий**.
2. В разделе **Подключение существующего диска** щелкните **VHD-файл**.
   
   ![Подключение существующего диска](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. В разделе **Учетные записи хранения**выберите учетную запись и контейнер, который содержит VHD-файл.
   
   ![Поиск расположения VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Выберите VHD-файл.
5. В разделе **Подключение существующего диска** выбранный вами файл отображается в списке, расположенном в разделе **VHD-файл**. Нажмите кнопку **ОК**.
6. После того как Azure подключит диск к виртуальной машине, он будет отображаться в параметрах дисков виртуальной машины в разделе **Диски данных**.



## <a name="next-steps"></a>Дальнейшие действия
После добавления диска его необходимо подготовить для использования. Дополнительные сведения см. в разделе [Инициализация нового диска данных в Linux](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO5-->



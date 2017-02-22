---
title: "Подключение диска к классической виртуальной машине Azure | Документация Майкрософт"
description: "Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания, и его инициализация."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 26c58ae4c509cb768807875ecdf96e9a24d6a472
ms.openlocfilehash: 8393b8ce2b373e8ff33454a61c944a5f8f7a8168


---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Если вы собираетесь использовать новый портал, см. статью [Подключение диска данных к виртуальной машине Windows на портале Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Если требуется дополнительный диск с данными, вы можете подключить к виртуальной машине пустой диск или существующий диск с данными. В обоих случаях диски — это VHD-файлы, которые размещаются в вашей учетной записи хранения Azure. Если применяется новый диск, после подключения диска также необходимо инициализировать его, чтобы он был готов для использования в виртуальной машине Windows.

Дополнительную информацию о дисках см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Инициализировать диск
1. Подключитесь к виртуальной машине. Указания см. в статье [Вход в виртуальную машину под управлением Windows с помощью классического портала Azure][logon].
2. После входа в систему на виртуальной машине откройте **Диспетчер сервера**. В области слева выберите **Файловые службы и службы хранилища**.
   
    ![Откройте диспетчер сервера.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Разверните меню и выберите **Диски**.
4. В разделе **Диски** перечислены все диски. В большинстве случаев присутствует диск 0, диск 1 и диск 2. Диск 0 — это диск операционной системы, диск 1 — временный диск, а диск 2 — диск данных, который вы только что подключили к виртуальной машине. Новый диск данных будет отображаться в качестве раздела **Неизвестный**. Щелкните диск правой кнопкой мыши и выберите пункт **Инициализировать**.
5. Появится уведомление о том, что при инициализации диска все данные будут очищены. Щелкните **Да** , чтобы подтвердить ознакомление с предупреждением и инициализировать диск. После завершения операции раздел будет иметь значение **GPT**. Щелкните диск правой кнопкой мыши еще раз и выберите пункт **Новый том**.
6. Завершите мастер, используя значения по умолчанию. После этого в разделе **Тома** появится новый том. Теперь диск в сети и готов к хранению данных.
   
   ![Том успешно инициализирован](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> Размер виртуальной машины определяет количество дисков, которые можно к ней подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="additional-resources"></a>Дополнительные ресурсы
[Отключение диска от виртуальной машины Windows](virtual-machines-windows-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[logon]: virtual-machines-windows-classic-connect-logon.md



<!--HONumber=Feb17_HO3-->



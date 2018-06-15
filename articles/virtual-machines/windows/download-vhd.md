---
title: Скачивание виртуального жесткого диска Windows из Azure | Документация Майкрософт
description: Скачайте виртуальный жесткий диск Windows с помощью портала Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f62c1b815180e39468a39b8bc2a220a6bfb9ea5a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726301"
---
# <a name="download-a-windows-vhd-from-azure"></a>Скачивание виртуального жесткого диска Windows из Azure

В этой статье описано, как скачать файл [виртуального жесткого диска (VHD) Windows](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) из Azure, используя портал Azure. 

## <a name="stop-the-vm"></a>Остановка виртуальной машины

VHD невозможно скачать из Azure, если он подключен к запущенной виртуальной машине. Для скачивания VHD необходимо остановить виртуальную машину. Чтобы использовать VHD в качестве [образа](tutorial-custom-images.md) для создания других виртуальных машин с помощью новых дисков, необходимо воспользоваться [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) для подготовки к использованию операционной системы, которая содержится в файле, а затем остановить виртуальную машину. Для использования VHD в качестве диска для нового экземпляра существующей виртуальной машины или диска данных необходимо просто остановить виртуальную машину и отменить ее выделение.

Чтобы использовать VHD как образ для создания других виртуальных машин, выполните следующие действия:

1.  Перейдите на [портал Azure](https://portal.azure.com/), если вы еще этого не сделали.
2.  [Подключитесь к виртуальной машине](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  На виртуальной машине откройте окно командной строки с правами администратора.
4.  Измените каталог на *%windir%\system32\sysprep* и запустите файл sysprep.exe.
5.  В диалоговом окне "Программа подготовки системы" выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
6.  В разделе "Параметры завершения работы" выберите **Завершение работы** и нажмите кнопку **ОК**. 

Для использования VHD в качестве диска для нового экземпляра существующей виртуальной машины или диска данных выполните следующие действия:

1.  В главном меню на портале Azure и щелкните **Виртуальные машины**.
2.  Выберите виртуальную машину из списка.
3.  В колонке виртуальной машины нажмите кнопку **Остановить**.

    ![Остановка виртуальной машины](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Создание URL-адреса SAS

Чтобы скачать VHD-файл, необходимо создать [подписанный URL-адрес (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Когда этот URL-адрес создан, ему назначается срок действия.

1.  В меню колонки виртуальной машины щелкните **Диски**.
2.  Выберите диск операционной системы для виртуальной машины и щелкните **Экспорт**.
3.  Задайте срок действия URL-адреса *36000*.
4.  Нажмите кнопку **Создать URL-адрес**.

    ![Создание URL-адреса](./media/download-vhd/export-generate.png)

> [!NOTE]
> Заданное по умолчанию значение срока действия увеличивается, чтобы предоставить достаточно времени на скачивание большого VHD-файл для операционной системы Windows Server. Скачивание VHD-файл, содержащего операционную систему Windows Server, может занять несколько часов в зависимости от качества подключения. Если вы скачиваете VHD для диска данных, то время, заданное по умолчанию, является достаточным. 
> 
> 

## <a name="download-vhd"></a>Скачивание VHD

1.  Под созданным URL-адресом щелкните ссылку "Скачать VHD-файл".

    ![Скачивание VHD](./media/download-vhd/export-download.png)

2.  Чтобы начать скачивание, может потребоваться нажать кнопку **Сохранить** в браузере. По умолчанию VHD-файлу присваивается имя *abcd*.

    ![Нажатие кнопки "Сохранить" в браузере](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [передать VHD-файл в Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Создание управляемых дисков из неуправляемых дисков в учетной записи хранения](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Управление дисками Azure с помощью PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


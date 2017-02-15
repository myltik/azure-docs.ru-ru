---
title: "Подготовка к использованию виртуального жесткого диска Windows | Документация Майкрософт"
description: "Узнайте, как с помощью Sysprep обобщить виртуальную машину Windows для использования в модели развертывания Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9e9d973ef36cde4f0a1ada2ba7bf7d01a8d8f687


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Обобщение виртуальной машины Windows с помощью Sysprep
В этом разделе содержатся сведения о том, как обобщить виртуальную машину Windows для ее дальнейшего использования в качестве образа. Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запуск Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. 

## <a name="next-steps"></a>Дальнейшие действия
* Если виртуальная машина находится в локальной среде, [отправьте виртуальный жесткий диск в Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Если виртуальная машина уже находится в Azure, [создайте образ из обобщенной виртуальной машины](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->



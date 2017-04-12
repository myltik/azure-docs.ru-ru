---
title: "Подготовка виртуальной машины Windows к использованию в Azure | Документация Майкрософт"
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
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b360a6cf0f8eb37099b02ffba8721ab3ec1a5f10
ms.lasthandoff: 03/31/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Обобщение виртуальной машины Windows с помощью Sysprep
В этом разделе содержатся сведения о том, как обобщить виртуальную машину Windows для ее дальнейшего использования в качестве образа. Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. 

> [!IMPORTANT]
> Не перезапускайте виртуальную машину до завершения передачи VHD в Azure или создания образа виртуальной машины. Если виртуальная машина случайно будет перезапущена, запустите инструмент Sysprep, чтобы повторно подготовить ее.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
* Если виртуальная машина находится в локальной среде, [отправьте виртуальный жесткий диск в Azure](upload-image.md).
* Если виртуальная машина уже находится в Azure, [создайте образ из обобщенной виртуальной машины](capture-image.md).



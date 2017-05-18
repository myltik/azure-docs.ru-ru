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
ms.date: 05/11/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 3be9805bfd1a71df1ff761531406adcbb7bbe612
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Обобщение виртуальной машины Windows с помощью Sysprep
В этом разделе содержатся сведения о том, как обобщить виртуальную машину Windows для ее дальнейшего использования в качестве образа. Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md). 
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



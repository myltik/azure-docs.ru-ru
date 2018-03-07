---
title: "Добавление образов Linux в Azure Stack"
description: "Сведения о том, как добавлять образы Linux в Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Добавление образов Linux в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные машины Linux можно развернуть в Azure Stack, добавив образ на базе Linux в Azure Stack Marketplace. Проще всего добавить образ Linux в Azure Stack с помощью управления Marketplace. Эти образы были подготовлены и протестированы на совместимость с Azure Stack.

## <a name="marketplace-management"></a>Управление Marketplace

Для загрузки образов Linux из Azure Marketplace используйте процедуры, приведенные в следующей статье. Выберите образы Linux, которые вы хотите предложить пользователям в Azure Stack.

[Скачайте элементы Marketplace из Azure в Azure Stack](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>Подготовка собственного образа

Можно подготовить свой собственный образ Linux с помощью одной из следующих инструкций:

   * [Подготовка виртуальной машины на основе CentOS для Azure](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Подготовка виртуального жесткого диска Debian для Azure](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Подготовка виртуальной машины SLES или openSUSE для Azure](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Скачайте и установите [агент Linux для Azure](https://github.com/Azure/WALinuxAgent/).

    Агент Linux для Azure версии 2.2.2 или выше необходим для подготовки виртуальной машины Linux в Azure Stack. Некоторые версии агента не работают (например, 2.2.12 и 2.2.13). Многие из перечисленных ранее дистрибутивов уже содержат эту или более позднюю версию агента (обычно под именем `WALinuxAgent` или `walinuxagent`). При создании настраиваемого образа необходимо установить агент вручную. Установленную версию можно определить по имени пакета или с помощью запуска `/usr/sbin/waagent -version` на виртуальной машине.

    Выполните приведенные ниже инструкции, чтобы установить агент Linux для Azure вручную.

   a. Сначала скачайте последнюю версию агента Linux для Azure с сайта [GitHub](https://github.com/Azure/WALinuxAgent/releases), например:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   Б. Распакуйте агент для Azure:

            # tar -vzxf v2.2.21.tar.gz
   c. Установите python-setuptools.

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Установите агент для Azure:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     В системах с параллельно установленными Python 2.x и Python 3.x может потребоваться выполнить следующую команду:

         sudo python3 setup.py install --register-service
     Дополнительные сведения см. в файле [сведений](https://github.com/Azure/WALinuxAgent/blob/master/README.md) агента Linux для Azure.
2. [Добавьте образ в Marketplace](azure-stack-add-vm-image.md). Убедитесь, что для параметра `OSType` задано значение `Linux`.
3. После добавления образа в Marketplace создается элемент Marketplace, и пользователи могут развернуть виртуальную машину Linux.

## <a name="next-steps"></a>Дополнительная информация
[Общие сведения о предложении служб в Azure Stack](azure-stack-offer-services-overview.md)

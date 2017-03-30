---
title: "Часто задаваемые вопросы о виртуальных машинах Windows в Azure | Документация Майкрософт"
description: "В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Windows, созданных с помощью модели Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6e84a567e8875a8db3f66bc19340557cff75cf1d
ms.lasthandoff: 03/17/2017


---

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Часто задаваемые вопросы по виртуальным машинам Windows
В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Windows, созданных в Azure с помощью модели развертывания Resource Manager. Версия этой статьи для Linux — [Часто задаваемые вопросы по виртуальным машинам Linux](virtual-machines-linux-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Что можно запускать на виртуальной машине Azure?
Все подписчики могут запускать на виртуальной машине Azure серверное программное обеспечение. Сведения о политике поддержки серверного ПО Майкрософт в Azure см. в статье [Поддержка серверного программного обеспечения Майкрософт для виртуальных машин Azure](https://support.microsoft.com/kb/2721672).

Подписчикам MSDN Azure, а также подписчикам MSDN для разработки и тестирования с оплатой по мере использования доступны определенные версии Windows 7, Windows 8.1 и Windows 10, которые можно использовать для разработки или тестирования. Дополнительные сведения, включая указания и ограничения, см. в статье [Образы клиента Windows для подписчиков MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Какой объем памяти можно использовать с виртуальной машиной?
Каждый диск данных может иметь объем до 1 ТБ. Количество дисков данных, которое можно использовать, зависит от размера виртуальной машины. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Учетная запись хранения Azure предоставляет хранилище для диска операционной системы и любых дисков данных. Каждый из этих дисков представляет собой VHD-файл, хранящийся как страничный BLOB-объект. Информацию о ценах см. в статье [Информация о ценах на хранилища](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Как получить доступ к своей виртуальной машине?
Установите удаленное подключение с использованием протокола удаленного рабочего стола (RDP) к виртуальной машине Windows. Указания см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти в систему](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Если сервер не настроен как узел сеансов служб удаленных рабочих столов, то поддерживается не более двух параллельных подключений.  

Если возникли проблемы с удаленным подключением, ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Если вы знакомы с Hyper-V, то, возможно, ищете инструмент, аналогичный VMConnect. В Azure такой инструмент не предусмотрен, так как не поддерживается консольный доступ к виртуальной машине.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Можно ли использовать временный диск (по умолчанию это диск D) для хранения данных?
Не используйте для хранения данных временный диск. Он обеспечивает лишь временное хранение, поэтому вы рискуете потерять данные, которые невозможно восстановить. Потеря данных может происходить, когда виртуальная машина перемещается на другой узел. Виртуальная машина может перемещаться, когда возникает необходимость в изменении ее размера, при обновлении узла, при сбое оборудования, а также по другим причинам.

При наличии приложения, для которого необходимо использовать букву диска D, можно переназначить буквы дисков, чтобы для временного диска использовался диск, отличный от D. Указания см. в статье [Изменение буквы диска для временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Как изменить букву временного диска?
Можно изменить букву диска, переместив файл подкачки и переназначив буквы дисков, но эти действия нужно выполнять в строго определенном порядке. Указания см. в статье [Изменение буквы диска для временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Можно ли добавить существующую виртуальную машину в группу доступности?
Нет. Чтобы виртуальная машина была частью группы доступности, ее необходимо создать в группе. Добавлять виртуальные машины в группу доступности после создания пока невозможно.
## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Можно ли передать виртуальную машину в Azure?
Да. Указания см. в статье [Отправка образа виртуальной машины Windows в Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-resize-the-os-disk"></a>Можно ли изменить размер диска ОС?
Да. Указания см. в статье [Как расширить диск ОС виртуальной машины в группе ресурсов Azure](virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Можно ли копировать или клонировать существующую виртуальную машину Azure?
Да. Указания см. в статье [Как создать копию виртуальной машины Windows в модели развертывания диспетчера ресурсов](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Почему в Azure Resource Manager не отображаются регионы центральной и восточной Канады?

При создании виртуальных машин в существующих подписках Azure эти два новых региона не регистрируются автоматически. Регистрация осуществляется автоматически при развертывании виртуальной машины на портале Azure в любом другом регионе с помощью Azure Resource Manager. После развертывания виртуальной машины в любом другом регионе Azure новые регионы станут доступными для последующих виртуальных машин.

## <a name="does-azure-support-linux-vms"></a>Поддерживает ли Azure виртуальные машины Linux?
Да. Чтобы быстро создать пробную виртуальную машину Linux, см. статью [Создание виртуальной машины Linux в Azure с помощью портала](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Создав виртуальную машину, могу я добавить к ней сетевой адаптер?
Да, теперь это возможно. Сначала виртуальную машину нужно остановить и отменить ее распределение. Затем можно добавить или удалить сетевую карту (за исключением последней сетевой карты на виртуальной машине). 

## <a name="are-there-any-computer-name-requirements"></a>Есть ли какие-либо требования к имени компьютера?
Да. Длина имени компьютера не должна превышать 15 знаков. Дополнительные сведения об именовании ресурсов см. в статье [Рекомендации по именованию для инфраструктуры](virtual-machines-windows-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Какие требования к имени пользователя при создании виртуальной машины?

Длина имени пользователя не должна превышать 20 знаков. Также оно не должно заканчиваться точкой ("."). 


Не допускаются следующие имена пользователей:
<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> владелец </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Какие требования к паролю при создании виртуальной машины?
Длина пароля должна быть от 12 до 123 знаков. Также должны выполняться 3 из следующих 4 требований сложности:

* используются строчные знаки;
* используются прописные знаки;
* используется цифра;
* используется специальный знак (регулярное выражение [\W_]).

Не допускаются следующие пароли:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$$word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Password! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>iloveyou! </td>
    </tr>
</table>


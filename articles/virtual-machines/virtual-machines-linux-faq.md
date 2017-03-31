---
title: "Часто задаваемые вопросы о виртуальных машинах Linux в Azure | Документация Майкрософт"
description: "В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Linux, созданных с помощью модели Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bfbd08b40e7d16efd5784497512f9063eb17d808
ms.lasthandoff: 03/22/2017


---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Часто задаваемые вопросы по виртуальным машинам Linux
В этой статье содержатся ответы на некоторые распространенные вопросы о виртуальных машинах Linux, созданных в Azure посредством модели развертывания с помощью Resource Manager. Версия этой статьи для Windows — [Часто задаваемые вопросы по виртуальным машинам Windows](virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Что можно запускать на виртуальной машине Azure?
Все подписчики могут запускать на виртуальной машине Azure серверное программное обеспечение. Дополнительные сведения см. в статье [Linux в Azure — рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Какой объем памяти можно использовать с виртуальной машиной?
Каждый диск данных может иметь объем до 1 ТБ. Количество дисков данных, которое можно использовать, зависит от размера виртуальной машины. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Учетная запись хранения Azure предоставляет хранилище для диска операционной системы и любых дисков данных. Каждый из этих дисков представляет собой VHD-файл, хранящийся как страничный BLOB-объект. Информацию о ценах см. в статье [Информация о ценах на хранилища](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Как получить доступ к своей виртуальной машине?
Установите удаленное подключение для входа на виртуальную машину с помощью Secure Shell (SSH). Ознакомьтесь с инструкциями по подключению [из Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [Linux и Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). По умолчанию SSH поддерживает не более 10 параллельных подключений. Число доступных параллельных подключений можно увеличить, изменив файл конфигурации.

Если возникают проблемы, ознакомьтесь со статьей об [устранении неполадок с подключением Secure Shell (SSH)](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Можно ли использовать временный диск (/dev/sdb1) для хранения данных?
Не используйте временный диск (/dev/sdb1) для хранения данных. Он обеспечивает лишь временное хранение. Вы рискуете потерять данные, которые невозможно будет восстановить.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Можно ли копировать или клонировать существующую виртуальную машину Azure?
Да. Указания доступны в статье [Создание копии виртуальной машины Linux, работающей в Azure](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Почему в Azure Resource Manager не отображаются регионы центральной и восточной Канады?
При создании виртуальных машин в существующих подписках Azure эти два новых региона не регистрируются автоматически. Регистрация осуществляется автоматически при развертывании виртуальной машины на портале Azure в любом другом регионе с помощью Azure Resource Manager. После развертывания виртуальной машины в любом другом регионе Azure новые регионы станут доступными для последующих виртуальных машин.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Создав виртуальную машину, могу я добавить к ней сетевой адаптер?
Да, теперь это возможно. Сначала виртуальную машину нужно остановить и отменить ее распределение. Затем можно добавить или удалить сетевую карту (за исключением последней сетевой карты на виртуальной машине). 

## <a name="are-there-any-computer-name-requirements"></a>Есть ли какие-либо требования к имени компьютера?
Да. Длина имени компьютера не должна превышать 64 знака. Дополнительные сведения об именовании ресурсов см. в статье [Рекомендации по именованию для инфраструктуры](virtual-machines-linux-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Какие требования к имени пользователя при создании виртуальной машины?
Длина имени пользователя должно быть от 1 до 64 знаков.

Не допускаются следующие имена пользователей:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
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
Длина пароля должна быть от 6 до 72 знаков. Также должны удовлетворяться 3 из следующих 4 требований сложности:

* используются строчные знаки;
* используются прописные знаки;
* используется цифра;
* используется специальный знак (регулярное выражение [\W_]).

Не допускаются следующие пароли:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>


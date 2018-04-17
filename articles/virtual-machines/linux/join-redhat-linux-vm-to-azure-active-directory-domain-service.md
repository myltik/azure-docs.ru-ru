---
title: Присоединение виртуальной машины RedHat Linux к доменным службам Azure Active Directory | Документация Майкрософт
description: Как присоединить существующую виртуальную машину RedHat Enterprise Linux 7 к доменным службам Azure Active Directory.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 68971372a3c9d9671ccad43db8d7b543d286fc7a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Присоединение виртуальной машины RedHat Linux к доменным службам Azure Active Directory

В этой статье показано, как присоединить виртуальную машину RedHat Enterprise Linux (RHEL) 7 к управляемому домену доменных служб Azure Active Directory (AADDS).  Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);

- [файлы открытого и закрытого ключа SSH](mac-create-ssh-keys.md).

- [доменные службы Azure Active Directory](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Быстрые команды

_Замените все примеры параметров своими значениями_.

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Переключение Azure CLI в режим классического развертывания

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Поиск версии и образа RHEL

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Создание виртуальной машины Red Hat Linux

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Подключение к виртуальной машине по протоколу SSH

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Обновление пакетов YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Установка требуемых пакетов

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Теперь, когда все требуемые пакеты установлены на виртуальной машине Linux, мы готовы присоединить виртуальную машину к управляемому домену.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Поиск управляемого домена доменных служб AAD

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Инициализация Kerberos

Обязательно укажите пользователя, который принадлежит к группе "Администраторы AAD AD". Только эти пользователи могут присоединять компьютеры к управляемому домену.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Присоединение компьютера к домену

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Проверка присоединения компьютера к домену

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Дальнейшие действия

* [Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Настройка хранилища ключей для виртуальных машин в Azure Resource Manager](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

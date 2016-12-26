---
title: "Присоединение виртуальной машины RedHat Linux к доменным службам Azure Active Directory | Документация Майкрософт"
description: "Как присоединить существующую виртуальную машину RedHat Enterprise Linux 7 к доменным службам Azure Active Directory."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 4e764e0d115c787f78593116f29c89e7c1e0e527
ms.openlocfilehash: 0c596b630c44ff68a01c7dbd3173c192fafac125


---

# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Присоединение виртуальной машины RedHat Linux к доменным службам Azure Active Directory

В этой статье показано, как присоединить виртуальную машину RedHat Enterprise Linux (RHEL) 7 к управляемому домену доменных служб Azure Active Directory (AADDS).  Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);

- [файлы открытого и закрытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md).

- [доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Быстрые команды

Замените все примеры параметров своими параметрами.

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
-p P@ssw0rd! \
-z "Small" \
-e \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Подключение к виртуальной машине по протоколу SSH

```bash
ssh -i ~/.ssh/id_rsa ahmet@
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

* [Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure](virtual-machines-linux-update-infrastructure-redhat?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Настройка хранилища ключей для виртуальных машин в Azure Resource Manager](virtual-machines-linux-key-vault-setup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Nov16_HO5-->



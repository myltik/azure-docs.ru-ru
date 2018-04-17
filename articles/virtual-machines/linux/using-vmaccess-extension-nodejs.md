---
title: Сброс настроек доступа на виртуальных машинах Linux в Azure с помощью расширения VMAccess | Документация Майкрософт
description: Узнайте, как сбросить доступ в виртуальных машинах Linux в Azure с помощью расширения VMAccess.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Linux в Azure с помощью расширения VMAccess и Azure CLI 1.0
В этой статье показано, как использовать расширение Azure VMAcesss для проверки или восстановления диска, сброса разрешений пользователей на доступ, управления учетными записями пользователей или сброса конфигурации SSHD в Linux. Для работы с этой статьей потребуется:

* Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).
* [Интерфейс командной строки Azure](../../cli-install-nodejs.md) с выполненным входом (с помощью команды `azure login`).
* Интерфейс командной строки Azure *нужно* переключить в режим Azure Resource Manager `azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания с помощью Resource Manager (в этой статье).
- [Azure CLI 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Использовать расширение VMAccess для виртуальных машин Linux можно двумя способами.

* С помощью Azure CLI 1.0 и необходимых параметров.
* С помощью необработанных файлов JSON, которые расширение VMAccess обрабатывает для выполнения операций с ними.

В разделе с кратким описанием команд будет использоваться команда Azure CLI 1.0 `azure vm reset-access`. В примерах команд ниже замените значения, содержащие example, значениями, соответствующими вашей среде.

## <a name="create-a-resource-group-and-linux-vm"></a>Создание группы ресурсов и виртуальной машины Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Создание виртуальной машины Debian
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Сброс пароля пользователя root
Чтобы сбросить пароль пользователя root, выполните следующую команду:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Сброс ключа SSH
Чтобы сбросить ключ SSH для пользователя, отличного от root, выполните следующую команду:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Создание пользователя
Чтобы создать пользователя, выполните следующую команду:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Удаление пользователя
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Сброс SSHD
Чтобы сбросить конфигурацию SSHD, выполните следующую команду:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство
### <a name="vmaccess-defined"></a>Определение VMAccess
На диске в виртуальной машине Linux имеются ошибки. Вы каким-то образом сбросили пароль пользователя root для виртуальной машины Linux или случайно удалили закрытый ключ SSH. Если бы такое случилось раньше, вам пришлось бы ехать в центр данных и открывать KVM-консоль для доступа к серверу. Расширение Azure VMAccess можно представить как KVM-коммутатор, который позволяет открывать консоль для сброса разрешений на доступ к Linux или обслуживания дисков.

В подробном пошаговом руководстве мы будем использовать полную форму VMAccess, которая использует необработанные файлы JSON.  Эти файлы JSON VMAccess также можно вызывать из шаблонов Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Использование VMAccess для проверки или восстановления диска виртуальной машины Linux
С помощью VMAccess можно выполнить проверку fsck для диска в виртуальной машине Linux.  Также можно выполнить проверку и восстановление диска с помощью VMAccess.

Для проверки и последующего восстановления диска используйте следующий сценарий VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Использование VMAccess для сброса доступа пользователя к Linux
Если вы потеряли доступ к учетной записи root на виртуальной машине Linux, то можете выполнить сценарий VMAccess, чтобы сбросить пароль пользователя root.

Чтобы сбросить пароль пользователя root, используйте следующий сценарий VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Чтобы сбросить ключ SSH пользователя, отличного от root, используйте следующий сценарий VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Использование VMAccess для управления учетными записями пользователей в Linux
VMAccess — это сценарий Python, который можно применять для управления пользователями в виртуальной машине Linux, не выполняя вход и не используя учетную запись sudo или root.

Чтобы создать пользователя, используйте следующий сценарий VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Чтобы удалить пользователя, используйте следующий сценарий VMAccess:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Использование VMAccess для сброса конфигурации SSHD
Если вы внесли изменения в конфигурацию SSHD для виртуальных машин Linux и закрыли подключение SSH, не проверив изменения, то последующий вход по SSH может оказаться невозможным.  VMAccess можно использовать для сброса конфигурации SSHD в известное исправное состояние, не выполняя вход с помощью SSH.

Чтобы сбросить конфигурацию SSHD, используйте следующий сценарий VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Дополнительная информация
Обновление Linux с помощью расширения Azure VMAccess — это один из методов внесения изменений на работающей виртуальной машине Linux.  Для изменения виртуальной машины Linux при загрузке также можно использовать инструменты, такие как cloud-init или шаблоны Azure.

[Обзор расширений и компонентов виртуальной машины](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Разработка шаблонов Azure Resource Manager с расширениями виртуальной машины Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Настройка виртуальной машины Linux во время создания с помощь cloud-init](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


---
title: "Сброс настроек доступа с помощью расширения VMAccess и Azure CLI 2.0 | Документация Майкрософт"
description: "В этой статье показано, как управлять пользователями и сбросить параметры доступа на виртуальных машинах Linux, используя расширение VMAccess и Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: debdb8a16c8cfd6a137bd2a7c3b82cfdbedb0d8c
ms.openlocfilehash: 4fac98d37dde195af69d8bd03fd796c6eeae3734
ms.lasthandoff: 02/27/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Linux с помощью расширения VMAccess и Azure CLI 2.0
На диске в виртуальной машине Linux имеются ошибки. Вы каким-то образом сбросили пароль пользователя root для виртуальной машины Linux или случайно удалили закрытый ключ SSH. Если бы такое случилось раньше, вам пришлось бы ехать в центр данных и открывать KVM-консоль для доступа к серверу. Расширение Azure VMAccess можно представить как KVM-коммутатор, который позволяет открывать консоль для сброса разрешений на доступ к Linux или обслуживания дисков.

В этой статье показано, как использовать расширение Azure VMAcesss для проверки или восстановления диска, сброса разрешений пользователей на доступ, управления учетными записями пользователей или сброса конфигурации SSHD в Linux. Эти действия можно также выполнить с помощью [Azure CLI 1.0](virtual-machines-linux-using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Использование расширения VMAccess
Расширение VMAccess для виртуальных машин Linux можно использовать двумя способами:

* С помощью Azure CLI 2.0 и необходимых параметров.
* [с помощью необработанных файлов JSON, которые расширение VMAccess обрабатывает](#use-json-files-and-the-vmaccess-extension) для выполнения операций.

В следующих примерах используется команда [az vm access](/cli/azure/vm/access) с соответствующими параметрами. Чтобы выполнить эти действия, нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Сброс ключа SSH
В следующем примере сбрасывается ключ SSH для пользователя `azureuser` на виртуальной машине `myVM`.

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Сброс пароля
В следующем примере сбрасывается пароль для пользователя `azureuser` на виртуальной машине `myVM`.

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>Сброс SSHD
В следующем примере сбрасывается конфигурация SSHD на виртуальной машине `myVM`.

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>Создание пользователя
В следующем примере создается пользователь `myNewUser`, использующий ключ SSH для аутентификации на виртуальной машине `myVM`.

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>Удаление пользователя
В следующем примере удаляется пользователь `myNewUser` на виртуальной машине `myVM`.

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Использование JSON-файлов и расширения VMAccess
В следующих примерах используется необработанные JSON-файлы. Используйте команду [az vm extension set](/cli/azure/vm/extension#set) для последующего вызова своих JSON-файлов. Эти JSON-файлы также можно вызывать из шаблонов Azure. 

### <a name="reset-user-access"></a>Сброс доступа пользователей
Если вы потеряли доступ к учетной записи root на виртуальной машине Linux, то можете выполнить сценарий VMAccess, чтобы сбросить пароль пользователя.

Чтобы сбросить ключ SSH, создайте файл `reset_ssh_key.json` и добавьте в него следующее содержимое.

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Чтобы сбросить пароль доступа, создайте файл `reset_user_password.json` и добавьте в него следующее содержимое.

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="reset-ssh"></a>Сброс конфигурации SSH
Если вы внесли изменения в конфигурацию SSHD для виртуальных машин Linux и закрыли подключение SSH, не проверив изменения, то последующий вход по SSH может оказаться невозможным.  VMAccess можно использовать для сброса конфигурации SSHD в известное исправное состояние, не выполняя вход с помощью SSH.

Чтобы сбросить конфигурацию SSHD, создайте файл `reset_sshd.json` и добавьте в него следующее содержимое.

```json
{
  "reset_ssh": true
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>Управление пользователями
VMAccess — это сценарий Python, который можно применять для управления пользователями в виртуальной машине Linux, не выполняя вход и не используя учетную запись sudo или root.

Чтобы создать пользователя, создайте файл `create_new_user.json` и добавьте в него следующее содержимое.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Чтобы удалить пользователя, создайте файл `delete_user.json` и добавьте в него следующее содержимое.

```json
{
  "remove_user":"myDeleteUser"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Проверка или исправление диска
С помощью VMAccess можно выполнить проверку fsck для диска в виртуальной машине Linux. Также можно выполнить проверку и восстановление диска с помощью VMAccess.

Чтобы проверить, а затем восстановить диск, используйте этот сценарий VMAccess, создайте файл `disk_check_repair.json` и добавьте в него следующее содержимое.

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Дальнейшие действия
Обновление Linux с помощью расширения Azure VMAccess — это один из методов внесения изменений на работающей виртуальной машине Linux. Для изменения виртуальной машины Linux при загрузке также можно использовать инструменты, такие как cloud-init или шаблоны Azure Resource Manager.

[Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Разработка шаблонов Azure Resource Manager с расширениями виртуальной машины Linux](virtual-machines-linux-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Настройка виртуальной машины Linux во время создания с помощь cloud-init](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



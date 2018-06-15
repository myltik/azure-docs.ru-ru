---
title: Сброс параметров доступа к виртуальным машинам Linux в Azure | Документация Майкрософт
description: В этой статье показано, как управлять пользователями с правами администратора и сбрасывать параметры доступа на виртуальных машинах Linux, используя расширение VMAccess и Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 15fab2b4f83b86227a3a2327669861b5adc8d3c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915149"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Управление пользователями с правами администратора, SSH и проверка или восстановление дисков на виртуальных машинах Linux с помощью расширения VMAccess и Azure CLI 2.0
На диске в виртуальной машине Linux имеются ошибки. Вы каким-то образом сбросили пароль пользователя root для виртуальной машины Linux или случайно удалили закрытый ключ SSH. Если бы такое случилось раньше, вам пришлось бы ехать в центр данных и открывать KVM-консоль для доступа к серверу. Расширение Azure VMAccess можно представить как KVM-коммутатор, который позволяет открывать консоль для сброса разрешений на доступ к Linux или обслуживания дисков.

В этой статье показано, как использовать расширение Azure VMAccess для проверки или восстановления диска, сброса разрешений пользователей на доступ, управления учетными записями пользователей с правами администратора или обновления конфигурации SSH в Linux. Эти действия можно также выполнить с помощью [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Использование расширения VMAccess
Расширение VMAccess для виртуальных машин Linux можно использовать двумя способами:

* С помощью Azure CLI 2.0 и необходимых параметров.
* [с помощью необработанных файлов JSON, которые расширение VMAccess обрабатывает](#use-json-files-and-the-vmaccess-extension) для выполнения операций.

В следующих примерах используются команды [az vm user](/cli/azure/vm/user). Чтобы выполнить эти действия, нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

## <a name="update-ssh-key"></a>Обновление ключа SSH
В следующем примере обновляется ключ SSH для пользователя `azureuser` на виртуальной машине `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Примечание.** При использовании команды `az vm user update` в файл `~/.ssh/authorized_keys` на виртуальной машине добавляется новый текст открытого ключа для администратора. Существующие ключи SSH не заменяются и не удаляются. Предыдущий набор ключей не будет удален во время развертывания или последующих обновлений при помощи расширения VMAccess.

## <a name="reset-password"></a>Сброс пароля
В следующем примере сбрасывается пароль для пользователя `azureuser` на виртуальной машине `myVM`.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Перезапуск SSH
В следующем примере показан перезапуск управляющей программы SSH и сброс настроек SSH до значений по умолчанию на виртуальной машине `myVM`.

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Создание пользователя с правами администратора (с правами sudo)
В следующем примере создается пользователь с именем `myNewUser` и правами **sudo**. Учетная запись использует ключ SSH для проверки подлинности на виртуальной машине с именем `myVM`. Этот метод предназначен для восстановления доступа к виртуальной машине в случае, если пользователь забудет или потеряет текущие учетные данные. Количество учетных записей с правами **sudo** рекомендуется ограничить.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Удаление пользователя
В следующем примере удаляется пользователь `myNewUser` на виртуальной машине `myVM`.

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Использование JSON-файлов и расширения VMAccess
В следующих примерах используется необработанные JSON-файлы. Используйте команду [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) для последующего вызова своих JSON-файлов. Эти JSON-файлы также можно вызывать из шаблонов Azure. 

### <a name="reset-user-access"></a>Сброс доступа пользователей
Если вы потеряли доступ к учетной записи root на виртуальной машине Linux, можете выполнить скрипт VMAccess, чтобы обновить ключ SSH или пароль пользователя.

Чтобы обновить открытый ключ SSH, создайте файл `update_ssh_key.json` и добавьте параметры в указанном ниже формате. Для параметров `username` и `ssh_key` задайте свои значения.

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
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
  --protected-settings update_ssh_key.json
```

Чтобы сбросить пароль пользователя, создайте файл `reset_user_password.json` и добавьте параметры в следующем формате. Для параметров `username` и `password` задайте свои значения.

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

### <a name="restart-ssh"></a>Перезапуск SSH
Чтобы перезапустить управляющую программу SSH и сбросить настройки SSH до значений по умолчанию, создайте файл `reset_sshd.json`. Добавьте следующее содержимое:

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

### <a name="manage-administrative-users"></a>Управление пользователями с правами администратора

Чтобы создать пользователя с правами **sudo**, использующего ключ SSH для проверки подлинности, создайте файл `create_new_user.json` и добавьте параметры в следующем формате. Для параметров `username` и `ssh_key` задайте свои значения. Этот метод предназначен для восстановления доступа к виртуальной машине в случае, если пользователь забудет или потеряет текущие учетные данные. Количество учетных записей с правами **sudo** рекомендуется ограничить.

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

Чтобы удалить пользователя, создайте файл `delete_user.json` и добавьте в него следующее содержимое. Для параметра `remove_user` задайте свое значение.

```json
{
  "remove_user":"myNewUser"
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
С помощью VMAccess можно также проверить и восстановить диск, добавленный к виртуальной машине Linux.

Чтобы проверить, а затем восстановить диск, создайте файл `disk_check_repair.json` и добавьте параметры в следующем формате. Для параметра `repair_disk` введите имя своего диска.

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
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

## <a name="next-steps"></a>Дополнительная информация
Обновление Linux с помощью расширения Azure VMAccess — это один из методов внесения изменений на работающей виртуальной машине Linux. Для изменения виртуальной машины Linux при загрузке также можно использовать инструменты, такие как cloud-init или шаблоны Azure Resource Manager.

[Обзор расширений и компонентов виртуальных машин под управлением Linux](extensions-features.md)

[Разработка шаблонов Azure Resource Manager с расширениями виртуальной машины Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Настройка виртуальной машины Linux во время создания с помощь cloud-init](using-cloud-init.md)


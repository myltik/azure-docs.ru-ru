<properties
    pageTitle="Сброс доступа в виртуальных машинах Linux в Azure с помощью расширения VMAccess | Microsoft Azure"
    description="Узнайте, как сбросить доступ в виртуальных машинах Linux в Azure с помощью расширения VMAccess."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Azure с помощью расширения VMAccess

В этой статье показано, как использовать расширение VMAcesss для виртуальных машин [(Github)](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) для проверки или восстановления диска, сброса разрешений пользователей на доступ, управления учетными записями пользователей или сброса конфигурации SSHD в Linux. Для выполнения действий, описанных в этой статье, требуется [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/), [ключи SSH](virtual-machines-linux-mac-create-ssh-keys.md), виртуальная машина Linux в Azure и интерфейс командной строки Azure, установленный и переведенный в режим ARM с помощью `azure config mode arm`.

## Быстрые команды

Использовать расширение VMAccess для виртуальных машин Linux можно двумя способами. Первый способ — использование интерфейса командной строки Azure с `azure vm reset-access` с соответствующим флагом. Второй способ — использование VMAccess с необработанными файлами JSON, которые расширение VMAccess будет обрабатывать а затем выполнять с ними операции. В разделе с кратким описанием команд будет использоваться метод `azure vm reset-access`.

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

## Сброс пароля пользователя root

Чтобы сбросить пароль пользователя root, выполните следующую команду:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u root -p <examplePassword>
```

## Сброс ключа SSH

Чтобы сбросить ключ SSH для пользователя, отличного от root, выполните следующую команду:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleUser> -M <~/.ssh/azure_id_rsa.pub>
```

## Создание пользователя

Чтобы создать пользователя, выполните следующую команду:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleNewUserName> -p <examplePassword>
```

## Удаление пользователя

```bash
azure vm reset-access -g <resource group> -n <vm name> -R <exampleNewUserName>
```

## Сброс SSHD

Чтобы сбросить конфигурацию SSHD, выполните следующую команду:

```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```


## Подробное пошаговое руководство

### Определение VMAccess

На диске в виртуальной машине Linux имеются ошибки. Вы каким-то образом сбросили пароль пользователя root для виртуальной машины Linux или случайно удалили закрытый ключ SSH. Если бы такое случилось раньше, вам пришлось бы ехать в центр данных, разблокировать дверь с помощью отпечатка пальца, а затем открывать KVM-консоль для доступа к серверу. Расширение Azure VMAccess можно представить как такой KVM-коммутатор, который позволяет открывать консоль для сброса разрешений на доступ к Linux или для обслуживания дисков.

В подробном пошаговом руководстве мы будем использовать полную форму VMAccess, которая использует необработанные файлы JSON. Эти файлы JSON VMAccess также можно вызывать из шаблонов Azure.

### Использование VMAccess для проверки или восстановления диска виртуальной машины Linux

С помощью VMAccess можно выполнить проверку fsck для диска в виртуальной машине Linux. Сначала можно выполнить проверку диска, а затем его восстановление, если были найдены ошибки.

Для проверки и последующего восстановления диска используйте следующий сценарий VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Использование VMAccess для сброса доступа пользователя к Linux

Если вы утратили доступ к учетной записи root в виртуальной машине Linux, то вы можете запустить сценарий VMAccess, чтобы сбросить пароль пользователя root и разблокировать Linux.

Чтобы сбросить пароль пользователя root, используйте следующий сценарий VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Чтобы сбросить ключ SSH пользователя, отличного от root, используйте следующий сценарий VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### Использование VMAccess для управления учетными записями пользователей в Linux

VMAccess — это сценарий Python, который можно применять для управления пользователями в виртуальной машине Linux, не выполняя вход и не используя учетную запись sudo или root.

Чтобы создать пользователя, используйте следующий сценарий VMAccess:

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Чтобы создать пользователя, используйте следующий сценарий VMAccess:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Использование VMAccess для сброса конфигурации SSHD в Linux

Если вы внесли изменения в конфигурацию SSHD для виртуальных машин Linux и закрыли подключение SSH, не проверив изменения, то последующий вход по SSH может оказаться невозможным. VMAccess можно использовать для сброса конфигурации SSHD в известное исправное состояние.

Чтобы сбросить конфигурацию SSHD, используйте следующий сценарий VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

<!---HONumber=AcomDC_0504_2016-->
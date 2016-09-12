.<properties
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
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Управление пользователями, SSH и проверка или восстановление дисков в виртуальных машинах Azure с помощью расширения VMAccess

В этой статье показано, как использовать расширение Azure VMAcesss для проверки или восстановления диска, сброса разрешений пользователей на доступ, управления учетными записями пользователей или сброса конфигурации SSHD в Linux.

Необходимые компоненты: [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/), [открытый и закрытый ключи SSH](virtual-machines-linux-mac-create-ssh-keys.md) и интерфейс командной строки Azure (Azure CLI), установленный и переведенный в режим Resource Manager с помощью `azure config mode arm`.

## Быстрые команды

Использовать расширение VMAccess для виртуальных машин Linux можно двумя способами.

- С помощью командной строки Azure и необходимых параметров.
- С помощью необработанных файлов JSON, которые расширение VMAccess обрабатывает для выполнения операций с ними.

В разделе с кратким описанием команд будет использоваться метод `azure vm reset-access` Azure CLI. В примерах команд ниже замените значения, содержащие example, значениями, соответствующими вашей среде.

## Создание группы ресурсов и виртуальной машины Linux

```bash
azure group create resourcegroupexample westus
```

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u userexample \
-g resourcegroupexample \
-l westus \
-y Linux \
-n debianexamplevm \
-Q Debian
```

## Сброс пароля пользователя root

Чтобы сбросить пароль пользователя root, выполните следующую команду:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u root -p examplenewPassword
```

## Сброс ключа SSH

Чтобы сбросить ключ SSH для пользователя, отличного от root, выполните следующую команду:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -M ~/.ssh/id_rsa.pub
```

## Создание пользователя

Чтобы создать пользователя, выполните следующую команду:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -p examplePassword
```

## Удаление пользователя

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -R userexample
```

## Сброс SSHD

Чтобы сбросить конфигурацию SSHD, выполните следующую команду:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -r
```


## Подробное пошаговое руководство

### Определение VMAccess

На диске в виртуальной машине Linux имеются ошибки. Вы каким-то образом сбросили пароль пользователя root для виртуальной машины Linux или случайно удалили закрытый ключ SSH. Если бы такое случилось раньше, вам пришлось бы ехать в центр данных и открывать KVM-консоль для доступа к серверу. Расширение Azure VMAccess можно представить как KVM-коммутатор, который позволяет открывать консоль для сброса разрешений на доступ к Linux или обслуживания дисков.

В подробном пошаговом руководстве мы будем использовать полную форму VMAccess, которая использует необработанные файлы JSON. Эти файлы JSON VMAccess также можно вызывать из шаблонов Azure.

### Использование VMAccess для проверки или восстановления диска виртуальной машины Linux

С помощью VMAccess можно выполнить проверку fsck для диска в виртуальной машине Linux. Также можно выполнить проверку и восстановление диска с помощью VMAccess.

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
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Использование VMAccess для сброса доступа пользователя к Linux

Если вы потеряли доступ к учетной записи root на виртуальной машине Linux, то можете выполнить сценарий VMAccess, чтобы сбросить пароль пользователя root.

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
azure vm extension set exampleResourceGroup exampleVM \
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
azure vm extension set exampleResourceGroup exampleVM \
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
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Чтобы создать пользователя, выполните следующую команду:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Использование VMAccess для сброса конфигурации SSHD

Если вы внесли изменения в конфигурацию SSHD для виртуальных машин Linux и закрыли подключение SSH, не проверив изменения, то последующий вход по SSH может оказаться невозможным. VMAccess можно использовать для сброса конфигурации SSHD в известное исправное состояние, не выполняя вход с помощью SSH.

Чтобы сбросить конфигурацию SSHD, используйте следующий сценарий VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Выполните сценарий VMAccess с помощью следующей команды:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## Дальнейшие действия

Обновление Linux с помощью расширения Azure VMAccess — это один из методов внесения изменений на работающей виртуальной машине Linux. Для изменения виртуальной машины Linux при загрузке также можно использовать инструменты, такие как cloud-init или шаблоны Azure.

[Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md)

[Разработка шаблонов Azure Resource Manager с расширениями виртуальной машины Linux](virtual-machines-linux-extensions-authoring-templates.md)

[Настройка виртуальной машины Linux во время создания с помощь cloud-init](virtual-machines-linux-using-cloud-init.md)

<!---HONumber=AcomDC_0831_2016-->
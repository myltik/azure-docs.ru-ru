---
title: "Подключение к виртуальной машине под управлением Linux в Azure с помощью удаленного рабочего стола | Документация Майкрософт"
description: "Узнайте, как установить и настроить удаленный рабочий стол (xrdp) для подключения к виртуальной машине Linux в Azure с помощью графических средств."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1b8ad526ee59bd1547cbd7f4a208720fb6557fc2
ms.lasthandoff: 03/27/2017


---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Установка и настройка удаленного рабочего стола для подключения к виртуальной машине Linux в Azure
Управление виртуальными машинами Linux в Azure обычно осуществляется из командной строки с помощью подключения Secure Shell (SSH). Если вы только начинаете работу с Linux или хотите быстро устранить неполадки, проще всего использовать удаленный рабочий стол. В этой статье описывается установка и настройка среды рабочего стола ([xfce](https://www.xfce.org)) и удаленного рабочего стола ([xrdp](http://www.xrdp)) для виртуальной машины Linux с помощью модели развертывания Resource Manager. Вы также можете [выполнить эти действия для виртуальных машин с помощью классической модели развертывания](linux/classic/remote-desktop.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


## <a name="prerequisites"></a>Предварительные требования
Для работы с этой статьей требуется существующая виртуальная машина Linux в Azure. Если требуется создать виртуальную машину, используйте один из следующих методов:

- [Azure CLI 2.0](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json);
- [портал Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Также необходимо выполнить вход в [активную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/).


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для установки и настройки удаленного рабочего стола на виртуальной машине. Дополнительные сведения и контекст для каждого этапа можно найти в остальной части документа, [начиная отсюда](#install-graphical-environment-on-linux-vm).

С помощью приведенного ниже примера кода можно установить упрощенную среду рабочего стола [xfce4](https://www.xfce.org/) на виртуальной машине Ubuntu. Коды для других дистрибутивов и сред рабочих столов незначительно отличаются. Например, для установки на виртуальной машине Red Hat Enterprise Linux используйте **yum** и настройте соответствующие правила **selinux**, а для установки на виртуальной машине SUSE используйте **zypper**.

Установите SSH-подключение к виртуальной машине. Установите среду рабочего стола xfce с помощью следующего кода:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

Установите xrdp с помощью следующего кода:

```bash
sudo apt-get install xrdp
```

Настройте xrdp для использования xfce в качестве среды рабочего стола с помощью следующего кода:

```bash
echo xfce4-session >~/.xsession
```

Перезапустите службу xrdp:

```bash
sudo service xrdp restart
```

Настройте пароль для своей учетной записи, если сейчас для проверки подлинности используется только ключ SSH:

```bash
sudo passwd ops
```

Выйдите из сеанса SSH и вернитесь к виртуальной машине Linux. Используйте Azure CLI на локальном компьютере, чтобы создать правило группы безопасности сети, чтобы разрешить обмен данными с удаленным рабочим столом. Используйте команду [az network nsg rule create](/cli/azure/network/nsg/rule#create) в Azure CLI 2.0. В следующем примере создается правило `myNetworkSecurityGroupRule` в `myNetworkSecurityGroup`. Это правило разрешает трафик через TCP-порт 3389.
    
```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 3389 \
    --access allow
```

Или используйте Azure CLI 1.0.

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --destination-port-range 3389 --access allow
```

Подключитесь к виртуальной машине Linux с помощью клиента удаленного рабочего стола, который вы обычно используете.

![Подключение к xrdp с помощью клиента удаленного рабочего стола](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Установка среды рабочего стола на виртуальной машине Linux
На большинстве виртуальных машин Linux в Azure по умолчанию не установлена среда рабочего стола. Управление виртуальными машинами Linux обычно осуществляется через SSH-подключения, а не с помощью рабочего стола. В Linux можно использовать различные среды рабочего стола. Выбранная среда рабочего стола может занимать 1–2 ГБ места на диске, а для установки и настройки всех необходимых пакетов может потребоваться 5–10 минут.

С помощью приведенного ниже примера кода можно установить упрощенную среду рабочего стола [xfce4](https://www.xfce.org/) на виртуальной машине Ubuntu. Команды для других дистрибутивов незначительно отличаются. Например, для установки на виртуальной машине Red Hat Enterprise Linux используйте **yum** и настройте соответствующие правила **selinux**, а для установки на виртуальной машине SUSE используйте **zypper**.

Сначала установите SSH-подключение к виртуальной машине. В следующем примере устанавливается подключение к виртуальной машине `myvm.westus.cloudapp.azure.com` с помощью имени пользователя `ops`:

```bash
ssh ops@myvm.westus.cloudapp.azure.com ~/.ssh/id_rsa.pub
```

Если вы используете Windows и нуждаетесь в дополнительных сведениях об использовании SSH, см. статью [Использование SSH с Windows в Azure](virtual-machines-linux-ssh-from-windows.md).

Затем установите xfce с помощью `apt` следующим образом:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Установка и настройка сервера удаленных рабочих столов
После установки среды рабочего стола настройте службу удаленного рабочего стола для прослушивания входящих подключений. [xrdp](http://xrdp.org) — это сервер RDP с открытым исходным кодом, доступный в большинстве дистрибутивов Linux и совместимый с xfce. Установите xrdp на виртуальной машине Ubuntu следующим образом:

```bash
sudo apt-get install xrdp
```

Укажите, какую среду рабочего стола должен использовать сервер xrdp при запуске сеанса. Настройте xrdp для использования xfce в качестве среды рабочего стола с помощью следующего кода:

```bash
echo xfce4-session >~/.xsession
```

Перезапустите службу xrdp, чтобы изменения вступили в силу:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Настройка пароля локальной учетной записи пользователя
Если вы создали пароль для учетной записи пользователя при создании виртуальной машины, пропустите этот шаг. Если вы используете только проверку подлинности с помощью ключа SSH и пароль локальной учетной записи не задан, укажите пароль, прежде чем использовать xrdp для входа на виртуальную машину. xrdp не принимает ключи SSH для проверки подлинности. В следующем примере задается пароль для учетной записи пользователя `ops`:

```bash
sudo passwd ops
```

> [!NOTE]
> После указания пароля конфигурация sshd не обновится таким образом, чтобы разрешить вход с помощью пароля, если такая возможность в настоящее время отсутствует. С точки зрения безопасности лучше подключиться к виртуальной машине через туннель SSH с помощью проверки подлинности на основе ключа, а затем подключиться к xrdp. В таком случае можно пропустить следующий шаг по созданию правила группы безопасности сети, разрешающего трафик с удаленного рабочего стола.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Создание правила группы безопасности сети, разрешающего трафик с удаленного рабочего стола
Чтобы трафик с удаленного рабочего стола мог поступать на виртуальную машину Linux, необходимо создать правило группы безопасности сети, разрешающее использовать протокол TCP на порту 3389 для доступа к виртуальной машине. Дополнительные сведения о правилах групп безопасности сети см. в статье [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Вы также можете [создать правило группы безопасности сети с помощью портала Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В следующих примерах создается правило группы безопасности сети с именем `myNetworkSecurityGroupRule`, разрешающее (`allow`) трафик по протоколу `tcp` на порту `3389`.

- Используйте команду [az network nsg rule create](/cli/azure/network/nsg/rule#create) в Azure CLI 2.0.
    
    ```azurecli
    az network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 3389 \
        --access allow
    ```

- Или используйте Azure CLI 1.0.

    ```azurecli
    azure network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --destination-port-range 3389 --access allow
    ```

## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Подключение к виртуальной машине Linux с помощью клиента удаленного рабочего стола
Откройте локальный клиент удаленного рабочего стола и подключитесь с помощью IP-адреса или DNS-имени виртуальной машины Linux. Введите имя пользователя и пароль для учетной записи пользователя на виртуальной машине:

![Подключение к xrdp с помощью клиента удаленного рабочего стола](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

После проверки подлинности загрузится среда рабочего стола xfce. Она будет выглядеть примерно так:

![Среда рабочего стола xfce при подключении через xrdp](./media/virtual-machines-linux-use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Устранение неполадок
Если не удается подключиться к виртуальной машине Linux с помощью клиента удаленного рабочего стола, используйте `netstat` на виртуальной машине Linux, чтобы убедиться, что она прослушивает RDP-подключения:

```bash
sudo netstat -plnt | grep rdp
```

В следующем примере показана виртуальная машина, прослушивающая TCP-порт 3389, как и ожидалось:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Если служба xrdp не выполняет прослушивание, перезапустите службу на виртуальной машине Ubuntu следующим образом:

```bash
sudo service xrdp restart
```

Просмотрите журналы `/var/log` на виртуальной машине Ubuntu, чтобы узнать, почему служба не отвечает. Вы также можете проверять системный журнал на предмет ошибок во время попыток подключения к удаленному рабочему столу:

```bash
tail -f /var/log/syslog
```

В других дистрибутивах Linux, например Red Hat Enterprise Linux и SUSE, способы перезапуска служб и расположение файла журнала могут отличаться.

Если ответ не поступает в клиент удаленного рабочего стола, а события не отображаются в системном журнале, это означает, что трафик с удаленного рабочего стола не достигает виртуальной машины. Проверьте правила групп безопасности сети и убедитесь, что в их числе есть правило, разрешающее трафик по протоколу TCP через порт 3389. Дополнительные сведения см. в статье [Устранение проблем с подключением к приложениям на виртуальных машинах Linux в Azure](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании и использовании ключей SSH на виртуальных машинах Linux см. в статье [Создание пары из открытого и закрытого ключей SSH для виртуальных машин Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Сведения об использовании SSH в Windows см. в статье [Использование SSH с Windows в Azure](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



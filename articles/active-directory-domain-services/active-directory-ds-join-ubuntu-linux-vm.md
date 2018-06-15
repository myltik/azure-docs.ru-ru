---
title: 'Доменные службы Azure Active Directory: присоединение виртуальной машины Ubuntu к управляемому домену | Документация Майкрософт'
description: Присоединение виртуальной машины Ubuntu Linux к управляемому домену Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: maheshu
ms.openlocfilehash: 51d32a1009e5fe2625bda80e4ab5ee7f5bf2a38d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587663"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Присоединение виртуальной машины Ubuntu к управляемому домену в Azure
Из этой статьи вы узнаете, как присоединить виртуальную машину Ubuntu Linux к управляемому домену доменных служб Azure AD.


## <a name="before-you-begin"></a>Перед началом работы
Чтобы выполнить задачи, описанные в этой статье, вам потребуется следующее:  
1. Действующая **подписка Azure**.
2. **Каталог Azure AD** — синхронизированный с локальным каталогом или каталогом только для облака.
3. **Доменные службы Azure AD** должны быть включены для каталога Azure AD. Если это еще не сделано, выполните все задачи, описанные в [руководстве по началу работы](active-directory-ds-getting-started.md).
4. Обязательно укажите IP-адреса управляемого домена в качестве DNS-серверов для виртуальной сети. Дополнительные сведения см. в статье об [изменении настроек DNS виртуальной сети Azure](active-directory-ds-getting-started-dns.md).
5. Выполните шаги, необходимые для [синхронизации паролей с управляемым доменом доменных служб Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Подготовка виртуальной машины Ubuntu Linux
Подготовьте виртуальную машину Ubuntu Linux в Azure любым из указанных ниже методов.
* [портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [интерфейс командной строки Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Разверните виртуальную машину в **виртуальной сети, для которой включены доменные службы Azure AD**.
> * Выберите **другую подсеть** (не ту, для которой включены доменные службы Azure AD).
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Удаленное подключение к виртуальной машине Ubuntu Linux
Вы подготовили виртуальную машину Ubuntu Linux в Azure. Следующая задача — установить удаленное подключение к виртуальной машине, используя учетную запись локального администратора, созданную при подготовке виртуальной машины.

Выполните инструкции из статьи о[входе в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Настройка файла hosts на виртуальной машине Linux
В окне терминала SSH откройте файл /etc/hosts для редактирования, чтобы изменить в нем IP-адрес и имя узла вашего компьютера.

```
sudo vi /etc/hosts
```

Добавьте следующее значение в файл hosts:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
В этом примере contoso100.com — это DNS-имя управляемого домена, а contoso-ubuntu — это имя узла виртуальной машины Ubuntu, присоединяемой к управляемому домену.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Установка требуемых пакетов на виртуальную машину Linux
После этого установите на виртуальную машину пакеты, необходимые для присоединения к домену. Выполните следующие действия:

1.  В терминале SSH введите команду ниже, чтобы загрузить списки пакетов из репозиториев. Эта команда обновляет списки пакетов для получения сведений о последних версиях пакетов и их зависимостях.

    ```
    sudo apt-get update
    ```

2. Введите следующую команду, чтобы установить необходимые пакеты.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Во время установки Kerberos экран будет розовым. При установке пакета krb5-user поступает запрос на имя области (ВСЕ символы названия должны быть в ВЕРХНЕМ РЕГИСТРЕ). В процессе установки разделы [realm] и [domain_realm] сохраняются в файл /etc/krb5.conf.

    > [!TIP]
    > Например, если управляемый домен имеет имя contoso100.com, введите название области CONTOSO100.COM. Не забывайте, что имя должно быть в ВЕРХНЕМ РЕГИСТРЕ.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Настройка параметров протокола NTP на виртуальной машине Linux
Дата и время на виртуальной машине Ubuntu должны синхронизироваться с управляемым доменом. Добавьте имя узла NTP из управляемого домена в файл /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

Добавьте следующее значение в файл ntp.conf и сохраните этот файл:

```
server contoso100.com
```
В этом примере contoso100.com — это DNS-имя управляемого домена.

Теперь на виртуальной машине Ubuntu выполните синхронизацию даты и времени с NTP-сервером, а затем запустите службу NTP.

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Присоединение виртуальной машины Linux к управляемому домену
Теперь, когда все требуемые пакеты установлены на виртуальной машине Linux, мы готовы присоединить виртуальную машину к управляемому домену.

1. Выполните поиск управляемого домена доменных служб AAD. В окне терминала SSH введите следующую команду:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE] 
   > **Устранение неполадок.** Если команда *realm discover* не может найти управляемый домен, сделайте следующее:
     * Проверьте подключение между доменом и виртуальной машиной (с помощью команды ping).
     * Убедитесь, что виртуальная машина развернута в одной виртуальной сети с управляемым доменом.
     * Проверьте, обновлены ли параметры DNS-сервера для виртуальной сети — должны быть указаны контроллеры управляемого домена.
   >

2. Инициализируйте Kerberos. В окне терминала SSH введите следующую команду: 

    > [!TIP] 
    > * Обязательно укажите пользователя, который принадлежит к группе "Администраторы AAD AD". 
    > * Введите доменное имя заглавными буквами, иначе операция с использованием kinit завершится ошибкой.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Присоедините компьютер к домену. В окне терминала SSH введите следующую команду: 

    > [!TIP] 
    > Используйте ту же учетную запись пользователя, которую вы указали на предыдущем шаге (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Когда компьютер присоединится к управляемому домену, вы получите сообщение "Компьютер успешно зарегистрирован в realm" (Successfully enrolled machine in realm).


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Обновление конфигурации SSSD и перезапуск службы
1. В окне терминала SSH введите команду ниже. Откройте файл sssd.conf и внесите в него следующее изменение:
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Закомментируйте строку **use_fully_qualified_names = True** и сохраните файл.
    ```
    # use_fully_qualified_names = True
    ```

3. Перезапустите службу SSHD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Настройка автоматического создания домашнего каталога
Чтобы включить автоматическое создание домашнего каталога при первом входе пользователя, введите следующие команды в окне терминала PuTTY:
```
sudo vi /etc/pam.d/common-session
```
    
В этом файле под строкой session optional pam_sss.so добавьте новую строку с указанными ниже данными и сохраните файл.
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Проверка присоединения к домену
Проверьте, присоединена ли виртуальная машина к управляемому домену. Подключитесь к виртуальной машине Ubuntu, присоединенной к домену, используя другое SSH-подключение. Используйте учетную запись пользователя домена и проверьте, правильно ли разрешится эта учетная запись.

1. В окне терминала SSH введите команду ниже, чтобы подключиться по протоколу SSH к виртуальной машине Ubuntu, присоединенной к домену. Используйте учетную запись домена, которая принадлежит к управляемому домену (в нашем примере — bob@CONTOSO100.COM).
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. Чтобы проверить, правильно ли инициализирован корневой каталог, в окне терминала SSH введите следующую команду:
    ```
    pwd
    ```

3. Чтобы проверить, правильно ли определено членство в группе, в окне терминала SSH введите следующую команду:
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"
Вы можете предоставить всем членам группы "Администраторы контроллера домена AAD" административные привилегии на виртуальной машине Ubuntu. Файл sudo хранится в файле /etc/sudoers. Члены групп AD, добавленных в этот файл, имеют право выполнять команду sudo.

1. Убедитесь, что в терминале SSH вы имеете права суперпользователя. Для этого можно использовать учетную запись локального администратора, которую вы указали при создании виртуальной машины. Выполните следующую команду.
    ```
    sudo vi /etc/sudoers
    ```

2. Добавьте следующую запись в файл /etc/sudoers и сохраните его:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Теперь при входе от имени любого члена группы "Администраторы контроллера домена AAD" вы получите права администратора на этой виртуальной машине.


## <a name="troubleshooting-domain-join"></a>Устранение неполадок при присоединении к домену
См. статью, посвященную [устранению неполадок при присоединении к домену](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain).


## <a name="related-content"></a>Похожий контент
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

---
title: 'Доменные службы Azure Active Directory: присоединение виртуальной машины RHEL к управляемому домену | Документация Майкрософт'
description: Присоединение виртуальной машины Red Hat Enterprise Linux к доменным службам Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: maheshu
ms.openlocfilehash: b91ffb0965516a8e3f6678a6607ab0b26a0c2a67
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586854"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену
Эта статья покажет, как присоединить виртуальную машину Red Hat Enterprise Linux (RHEL) 7 к управляемому домену доменных служб Azure AD.

## <a name="before-you-begin"></a>Перед началом работы
Чтобы выполнить задачи, описанные в этой статье, вам потребуется следующее:  
1. Действующая **подписка Azure**.
2. **Каталог Azure AD** — синхронизированный с локальным каталогом или каталогом только для облака.
3. **Доменные службы Azure AD** должны быть включены для каталога Azure AD. Если это еще не сделано, выполните все задачи, описанные в [руководстве по началу работы](active-directory-ds-getting-started.md).
4. Обязательно укажите IP-адреса управляемого домена в качестве DNS-серверов для виртуальной сети. Дополнительные сведения см. в статье об [изменении настроек DNS виртуальной сети Azure](active-directory-ds-getting-started-dns.md).
5. Выполните шаги, необходимые для [синхронизации паролей с управляемым доменом доменных служб Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Подготовка виртуальной машины Red Hat Enterprise Linux
Подготовьте виртуальную машину RHEL 7 в Azure любым из следующих способов:
* [портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [интерфейс командной строки Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Разверните виртуальную машину в **виртуальной сети, для которой включены доменные службы Azure AD**.
> * Выберите **другую подсеть** (не ту, для которой включены доменные службы Azure AD).
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Удаленное подключение к только что подготовленной виртуальной машине Linux
Вы подготовили виртуальную машину RHEL 7.2 в Azure. Следующая задача — установить удаленное подключение к виртуальной машине, используя учетную запись локального администратора, созданную при подготовке виртуальной машины.

Выполните инструкции из статьи о[входе в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Настройка файла hosts на виртуальной машине Linux
В окне терминала SSH откройте файл /etc/hosts для редактирования, чтобы изменить в нем IP-адрес и имя узла вашего компьютера.

```
sudo vi /etc/hosts
```

Добавьте следующее значение в файл hosts:

```
127.0.0.1 contoso-rhel.contoso100.com contoso-rhel
```
В этом случае contoso100.com — это DNS-имя управляемого домена. contoso-rhel — это имя узла виртуальной машины RHEL, присоединяемой к управляемому домену.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Установка требуемых пакетов на виртуальную машину Linux
После этого установите на виртуальную машину пакеты, необходимые для присоединения к домену. В окне терминала SSH введите следующую команду, чтобы установить необходимые пакеты:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Когда компьютер присоединится к управляемому домену, вы получите сообщение "Компьютер успешно зарегистрирован в realm" (Successfully enrolled machine in realm).


## <a name="verify-domain-join"></a>Проверка присоединения к домену
Проверьте, присоединена ли виртуальная машина к управляемому домену. Подключитесь к виртуальной машине RHEL, присоединенной к домену, используя другое SSH-подключение. Используйте учетную запись пользователя домена и проверьте, правильно ли разрешена учетная запись.

1. В окне терминала SSH введите следующую команду, чтобы подключиться к виртуальной машине, присоединенной к домену, с помощью SSH. Используйте учетную запись домена, которая принадлежит к управляемому домену (в нашем примере — bob@CONTOSO100.COM).
    ```
    ssh -l bob@CONTOSO100.COM contoso-rhel.contoso100.com
    ```

2. Чтобы проверить, правильно ли инициализирован корневой каталог, в окне терминала SSH введите следующую команду:
    ```
    pwd
    ```

3. Чтобы проверить, правильно ли определено членство в группе, в окне терминала SSH введите следующую команду:
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Устранение неполадок при присоединении к домену
См. статью, посвященную [устранению неполадок при присоединении к домену](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain).

## <a name="related-content"></a>Похожий контент
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Установка Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7: Windows Integration Guide (Red Hat Enterprise Linux 7: руководство по интеграции Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

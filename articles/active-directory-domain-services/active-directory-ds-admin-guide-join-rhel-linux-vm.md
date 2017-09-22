---
title: "Доменные службы Azure Active Directory: присоединение виртуальной машины RHEL к управляемому домену | Документация Майкрософт"
description: "Присоединение виртуальной машины Red Hat Enterprise Linux к доменным службам Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 435d01f14978ea72b088691747175c56f31b8bc3
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену
Эта статья покажет, как присоединить виртуальную машину Red Hat Enterprise Linux (RHEL) 7 к управляемому домену доменных служб Azure AD.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Подготовка виртуальной машины Red Hat Enterprise Linux
Вот как подготовить виртуальную машину RHEL 7 на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).

    ![Панель мониторинга портала Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. Нажмите кнопку **Создать** в области слева и введите в поле поиска **Red Hat**, как показано ниже на снимке экрана. В результатах поиска вы увидите все записи для Red Hat Enterprise Linux. Щелкните **Red Hat Enterprise Linux 7.2**.

    ![Выбор RHEL в результатах](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. Результаты поиска в области **Все** должны содержать образ Red Hat Enterprise Linux 7.2. Щелкните **Red Hat Enterprise Linux 7.2** , чтобы получить сведения об образе виртуальной машины.

    ![Выбор RHEL в результатах](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. В области **Red Hat Enterprise Linux 7.2** вы увидите подробную информацию об образе виртуальной машины. В раскрывающемся списке **Выберите модель развертывания** выберите вариант **Классический**. Нажмите кнопку **Создать** .

    ![Просмотр сведений об образе](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. На странице **Основные сведения** мастера **создания виртуальной машины** введите **имя узла** для новой виртуальной машины. Также следует указать учетные данные локального администратора: **имя пользователя** и **пароль**. Вы также можете использовать ключ SSH для проверки подлинности локального администратора. Кроме того, выберите **ценовую категорию** для виртуальной машины.

    ![Мастер создания виртуальной машины: страница основных сведений](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. На странице **Размер** мастера **создания виртуальной машины** выберите размер виртуальной машины.

    ![Мастер создания виртуальной машины: выбор размера](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-vm-size.png)

7. На странице **Параметры** мастера **создания виртуальной машины** выберите учетную запись хранения для виртуальной машины. Щелкните **Виртуальная сеть**, чтобы выбрать виртуальную сеть, в которой нужно развернуть виртуальную машину Linux. В колонке **Виртуальная сеть** выберите виртуальную сеть, в которой доступны доменные службы Azure AD. В этом примере мы выбрали виртуальную сеть MyPreviewVNet.

    ![Создание виртуальной машины — выбор виртуальной сети](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. На странице **Сводка** мастера **создания виртуальной машины** проверьте сведения и нажмите кнопку **ОК**.

    ![Создание виртуальной машины — виртуальная сеть выбрана](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. Начнется развертывание новой виртуальной машины на основе образа RHEL 7.2.

    ![Создание виртуальной машины — развертывание начато](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
10. Через несколько минут виртуальная машина должна быть развернута и готова к использованию.

    ![Создание виртуальной машины — развернута](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Удаленное подключение к только что подготовленной виртуальной машине Linux
Вы подготовили виртуальную машину RHEL 7.2 в Azure. Следующий шаг — установка удаленного подключения к этой виртуальной машине.

**Подключение к виртуальной машине RHEL 7.2**. Выполните инструкции из статьи [Как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Во всех последующих действиях для подключения к виртуальной машине RHEL мы используем SSH-клиент PuTTY. Дополнительные сведения см. на [странице скачивания PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Откройте программу PuTTY.
2. Введите **имя узла** для новой виртуальной машины RHEL. В нашем примере новая виртуальная машина имеет имя узла contoso-rhel.cloudapp.net. Если вы не уверены, какое имя узла у вашей виртуальной машины, откройте панель мониторинга виртуальной машины на портале Azure.

    ![Подключение PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. Войдите на виртуальную машину, используя учетные данные локального администратора, которые вы указали при создании виртуальной машины. В этом примере для учетной записи локального администратора используется имя mahesh.

    ![Окно входа PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Установка требуемых пакетов на виртуальную машину Linux
Когда вы подключитесь к виртуальной машине, вам следует установить все пакеты, необходимые для присоединения виртуальной машины к домену. Выполните следующие действия:

1. **Установите realmd:** пакет realmd нужен для присоединения к домену. В окне терминала PuTTY введите следующую команду:

    sudo yum install realmd

    ![Установка realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Через несколько минут пакет realmd будет установлен на виртуальной машине.

    ![realmd установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **Установите sssd:** пакету realmd требуется sssd, чтобы выполнять операции присоединения к домену. В окне терминала PuTTY введите следующую команду:

    sudo yum install sssd

    ![Установка sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Через несколько минут пакет sssd будет установлен на виртуальной машине.

    ![realmd установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **Установите kerberos:** в окне терминала PuTTY введите следующую команду:

    sudo yum install krb5-workstation krb5-libs

    ![Установка kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Через несколько минут пакет realmd будет установлен на виртуальной машине.

    ![Kerberos установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Присоединение виртуальной машины Linux к управляемому домену
Теперь, когда все требуемые пакеты установлены на виртуальной машине Linux, мы готовы присоединить виртуальную машину к управляемому домену.

1. Выполните поиск управляемого домена доменных служб AAD. В окне терминала PuTTY введите следующую команду:

    sudo realm discover CONTOSO100.COM

    ![realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Если команде **realm discover** не удается найти управляемый домен, проверьте подключение между доменом и виртуальной машиной (с помощью команды ping). Также убедитесь, что виртуальная машина развернута в одной виртуальной сети с управляемым доменом.
2. Инициализируйте kerberos. В окне терминала PuTTY введите следующую команду. Обязательно укажите пользователя, который принадлежит к группе "Администраторы AAD AD". Только эти пользователи могут присоединять компьютеры к управляемому домену.

    kinit bob@CONTOSO100.COM

    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Доменное имя следует вводить заглавными буквами, иначе kinit завершится ошибкой.
3. Присоедините компьютер к домену. В окне терминала PuTTY введите следующую команду. Укажите того же пользователя, учетные данные которого вы использовали на предыдущем шаге (kinit).

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

    ![Присоединение к realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Когда компьютер присоединится к управляемому домену, вы получите сообщение "Компьютер успешно зарегистрирован в realm" (Successfully enrolled machine in realm).

## <a name="verify-domain-join"></a>Проверка присоединения к домену
Вы можете быстро проверить, присоединена ли виртуальная машина к управляемому домену. Подключитесь к виртуальной машине, присоединенной к домену, используя SSH и учетную запись пользователя домена, а затем проверьте, правильно ли разрешена учетная запись.

1. В окне терминала PuTTY введите следующую команду, чтобы подключиться к виртуальной машине, присоединенной к домену, с помощью SSH. Используйте учетную запись домена, которая принадлежит к управляемому домену (в нашем примере — bob@CONTOSO100.COM).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. Чтобы проверить, правильно ли инициализирована домашняя папка, в окне терминала PuTTY введите такую команду:

    pwd
3. Чтобы проверить, правильно ли определено членство в группе, в окне терминала PuTTY введите такую команду:

    id

Ниже приведен пример выходных данных этих команд:

![Проверка присоединения к домену](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>Устранение неполадок при присоединении к домену
См. статью, посвященную [устранению неполадок при присоединении к домену](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join).

## <a name="related-content"></a>Похожий контент
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Установка Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7: Windows Integration Guide (Red Hat Enterprise Linux 7: руководство по интеграции Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)


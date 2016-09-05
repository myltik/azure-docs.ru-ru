.<properties
	pageTitle="Предварительная версия доменных служб Azure Active Directory: руководство по администрированию | Microsoft Azure"
	description="Присоединение виртуальной машины Red Hat Enterprise Linux к доменным службам Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену
Эта статья покажет, как присоединить виртуальную машину Red Hat Enterprise Linux (RHEL) 7 к управляемому домену доменных служб Azure AD.

## Подготовка виртуальной машины Red Hat Enterprise Linux
Вот как подготовить виртуальную машину RHEL 7 с помощью портала Azure:

1. Войдите на [портал Azure](https://portal.azure.com).

    ![Панель мониторинга портала Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Нажмите кнопку **Создать** в области слева и введите в поле поиска **Red Hat**, как показано ниже на снимке экрана. В результатах поиска вы увидите все записи для Red Hat Enterprise Linux. Щелкните **Red Hat Enterprise Linux 7.2**.

    ![Выбор RHEL в результатах](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Результаты поиска в области **Все** должны содержать образ Red Hat Enterprise Linux 7.2. Щелкните **Red Hat Enterprise Linux 7.2**, чтобы получить сведения об образе виртуальной машины.

    ![Выбор RHEL в результатах](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. В области **Red Hat Enterprise Linux 7.2** вы увидите подробную информацию об образе виртуальной машины. В раскрывающемся списке **Выберите модель развертывания** выберите вариант **Классический**. Нажмите кнопку **Создать**.

    ![Просмотр сведений об образе](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. В области **Создать виртуальную машину** введите **Имя узла** для новой виртуальной машины. Также следует указать учетные данные локального администратора: **имя пользователя** и **пароль**. Вы также можете использовать ключ SSH для проверки подлинности локального администратора. Кроме того, выберите **ценовую категорию** для виртуальной машины.

    ![Создание виртуальной машины — основные сведения](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Затем щелкните **Дополнительная настройка**. Появится область с названием **Дополнительная настройка**. В этой области **Дополнительная настройка** щелкните элемент **Сети**, как показано на следующем снимке экрана.

    ![Создание виртуальной машины — настройка виртуальной сети](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Теперь появится панель с именем **Сеть**. В области **Сеть** щелкните **Виртуальная сеть**, чтобы выбрать виртуальную сеть, в которой нужно развернуть виртуальную машину Linux. Должна открыться область **Виртуальная сеть**. В области **Виртуальная сеть** выберите вариант **Использовать существующую виртуальную сеть**. Затем выберите виртуальную сеть, в которой доступны доменные службы Azure AD. В этом примере мы выбрали виртуальную сеть MyPreviewVNet.

    ![Создание виртуальной машины — выбор виртуальной сети](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. В области **Дополнительная настройка** щелкните кнопку **ОК**.

    ![Создание виртуальной машины — виртуальная сеть выбрана](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Теперь все готово к созданию виртуальной машины. В области **Создать виртуальную машину** нажмите кнопку **Создать**.

    ![Создание виртуальной машины — готово](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Начнется развертывание новой виртуальной машины на основе образа RHEL 7.2.

  ![Создание виртуальной машины — развертывание начато](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Через несколько минут виртуальная машина должна быть развернута и готова к использованию.

  ![Создание виртуальной машины — развернута](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## Удаленное подключение к только что подготовленной виртуальной машине Linux
Вы подготовили виртуальную машину RHEL 7.2 в Azure. Следующий шаг — установка удаленного подключения к этой виртуальной машине.

**Подключение к виртуальной машине RHEL 7.2**. Выполните инструкции из статьи [Как войти в виртуальную машину под управлением Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Во всех последующих действиях мы используем для подключения к виртуальной машине RHEL SSH-клиент PuTTY. Дополнительные сведения см. на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Откройте программу PuTTY.

2. Введите **имя узла** для новой виртуальной машины RHEL. В нашем примере новая виртуальная машина имеет имя узла contoso-rhel.cloudapp.net. Если вы не уверены, какое имя узла у вашей виртуальной машины, откройте панель мониторинга виртуальной машины на портале Azure.

    ![Подключение PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Войдите на виртуальную машину, используя учетные данные локального администратора, которые вы указали при создании виртуальной машины. В этом примере мы используем имя mahesh для учетной записи локального администратора.

    ![Окно входа PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## Установка требуемых пакетов на виртуальную машину Linux
Когда вы подключитесь к виртуальной машине, вам следует установить все пакеты, необходимые для присоединения виртуальной машины к домену. Выполните следующие действия:

1. **Установите realmd:** пакет realmd нужен для присоединения к домену. В окне терминала PuTTY введите следующую команду.

    sudo yum install realmd

    ![Установка realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Через несколько минут пакет realmd будет установлен на виртуальной машине.

    ![realmd установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Установите sssd:** пакет realmd зависит от sssd, когда выполняет операции присоединения к домену. В окне терминала PuTTY введите следующую команду.

    sudo yum install sssd

	![Установка sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Через несколько минут пакет sssd будет установлен на виртуальной машине.

    ![realmd установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Установите kerberos:** в окне терминала PuTTY введите следующую команду.

    sudo yum install krb5-workstation krb5-libs

	![Установка kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

	Через несколько минут пакет realmd будет установлен на виртуальной машине.

	![Kerberos установлен](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## Присоединение виртуальной машины Linux к управляемому домену
Теперь, когда все требуемые пакеты установлены на виртуальной машине Linux, мы готовы присоединить виртуальную машину к управляемому домену.

1. Выполните поиск управляемого домена доменных служб AAD. В окне терминала PuTTY введите следующую команду.

    sudo realm discover CONTOSO100.COM

	![Поиск realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

	Если команде **realm discover** не удается найти управляемый домен, проверьте подключение между доменом и виртуальной машиной (с помощью команды ping). Также убедитесь, что виртуальная машина развернута в одной виртуальной сети с управляемым доменом.

2. Инициализируйте kerberos. В окне терминала PuTTY введите следующую команду. Обязательно укажите пользователя, который принадлежит к группе "Администраторы AAD AD". Только эти пользователи могут присоединять компьютеры к управляемому домену.

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Доменное имя следует вводить заглавными буквами, иначе kinit завершится ошибкой.

3. Присоедините компьютер к домену. В окне терминала PuTTY введите следующую команду. Укажите того же пользователя, учетные данные которого использовали на предыдущем шаге (kinit).

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

	![Присоединение к realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Когда компьютер присоединится к управляемому домену, вы получите сообщение "Компьютер успешно зарегистрирован в realm" (Successfully enrolled machine in realm).


## Проверка присоединения к домену
Вы можете быстро проверить, присоединена ли виртуальная машина к управляемому домену. Для этого подключитесь к новой виртуальной машине, присоединенной к домену, используя ssh и учетную запись пользователя домена, а затем проверьте, правильно ли разрешается учетная запись.

1. В окне терминала PuTTY введите следующую команду, чтобы подключиться к виртуальной машине, присоединенной к домену, с помощью SSH. Используйте учетную запись домена, которая принадлежит к управляемой учетной записи (в нашем примере это bob@CONTOSO100.COM).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. В окне терминала PuTTY введите следующую команду, чтобы проверить, правильно ли инициализирована домашняя папка пользователя.

	pwd

3. В окне терминала PuTTY введите следующую команду, чтобы проверить, правильно ли определены группы для пользователя.

    id

Ниже приведен пример выходных данных этих команд.

![Проверка присоединения к домену](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## Устранение неполадок при присоединении к домену
См. раздел, посвященный [устранению неполадок при присоединении к домену](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join).


## Дополнительные сведения
- [Вход в виртуальную машину под управлением ОС Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Installing Kerberos](https://access.redhat.com/documentation/ru-RU/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html) (Установка Kerberos)
- [Red Hat Enterprise Linux 7: Windows Integration Guide](https://access.redhat.com/documentation/ru-RU/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html) (Red Hat Enterprise Linux 7: руководство по интеграции Windows)

<!---HONumber=AcomDC_0824_2016-->
<properties linkid="manage-services-networking-replica-domain-controller" urlDisplayName="Replica domain controller" pageTitle="Install a replica domain controller in Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Установка реплики контроллера домена Active Directory в виртуальных сетях Azure

В этом пошаговом учебнике перечислены действия, которые следует выполнить для установки дополнительного контроллера домена из леса Active Directory вашего предприятия на виртуальной машине в [виртуальной сети Azure][виртуальной сети Azure]. В рамках этого руководства считается, что виртуальная сеть виртуальной машины подключена к корпоративной сети вашей компании. Ориентирующее руководство по установке доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в разделе [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure][Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure].

## Оглавление

-   [Предварительные требования][Предварительные требования]
-   [Шаг 1: проверка статического IP-адреса для основного контроллера домена][Шаг 1: проверка статического IP-адреса для основного контроллера домена]
-   [Шаг 2: установка леса предприятия][Шаг 2: установка леса предприятия]
-   [Шаг 3: создание подсетей и сайтов][Шаг 3: создание подсетей и сайтов]
-   [Шаг 4: установка дополнительного контроллера домена на CloudSite][Шаг 4: установка дополнительного контроллера домена на CloudSite]
-   [Шаг 5: Проверка установки][Шаг 5: Проверка установки]
-   [Шаг 6: Подготовка виртуальной машины, присоединенной к домену при запуске][Шаг 6: Подготовка виртуальной машины, присоединенной к домену при запуске]
-   [Шаг 7: Выполните архивацию контроллера домена][Шаг 7: Выполните архивацию контроллера домена]
-   [Шаг 8: тестирование аутентификации и авторизации][Шаг 8: тестирование аутентификации и авторизации]

## <span id="Prerequisites"></span></a>Предварительные требования

-   [Настройте VPN «сеть-сеть» на портале управления][Настройте VPN «сеть-сеть» на портале управления], то есть между виртуальной сетью Azure и корпоративной сетью.
-   Создайте облачную службу в виртуальной сети.
-   Разверните две виртуальные машины в облачной службе, которые будут являться частью виртуальной сети (укажите подсеть, в которую следует поместить виртуальную машину). Дополнительные сведения см. в разделе [Добавление виртуальной машины к виртуальной сети][Добавление виртуальной машины к виртуальной сети]. Одна виртуальная машина должна иметь размер L и больше, чтобы к ней можно было подсоединить два диска данных. Диски данных должны хранить следующее:
    -   База данных и журналы Active Directory.
    -   Резервные копии состояния системы.
-   Корпоративная сеть с двумя виртуальными машинами (основной контроллер домена и файловый сервер).
-   Если необходимо дать внешним пользователям право разрешать имена учетных записей в Active Directory, необходимо развернуть инфраструктуру DNS. В этом случае следует создать делегирование зоны DNS, прежде чем устанавливать DNS-сервер на контроллере домена или разрешить мастеру установки доменных служб Active Directory создать подобное делегирование. Дополнительные сведения о создании делегирования зоны DNS см в разделе [Создание делегирования зоны][Создание делегирования зоны].
-   На контроллере домена, который был установлен на виртуальной машине Azure, настройте параметры средства разрешения клиента DNS, как показано ниже.
    -   Предпочитаемый DNS-сервер: локальный DNS-сервер.
    -   Дополнительный DNS-сервер: адрес замыкания на себя или, если возможно, другой DNS-сервер, работающий на контроллере домена в той же виртуальной сети.

<div class="dev-callout"> 
<b>Примечание.</b>
<p>Вам необходимо предоставить собственную инфраструктуру DNS для поддержки AD DS в виртуальной сети Azure. Инфраструктура DNS на базе Azure в рамках этого выпуска не поддерживает некоторые функции, необходимые для AD DS, такие как динамическая регистрация записей служб SRV. </p>
</div>

<div class="dev-callout"> 
<b>Примечание.</b>
<p>Если вы уже выполнили действия, приведенные в разделе <a href="/ru-ru/manage/services/networking/active-directory-forest/">Установка нового леса Active Directory в Azure</a>, вам может понадобиться удалить AD DS с контроллера домена в виртуальной сети Azure, прежде чем начать работу с этим учебником. Дополнительную информацию об удалении AD DS см. в разделе <a href="http://technet.microsoft.com/ru-ru/library/cc771844(v=WS.10).aspx">Удаление контроллера домена из домена</a>.</p>
</div>

## <span id="verifystaticip"></span></a>Шаг 1: проверка статического IP-адреса для основного контроллера домена

1.  Выполните вход на основной контроллер домена в корпоративной сети.

2.  В диспетчере сервера щелкните пункт "Просмотр сетевых подключений".

3.  Щелкните правой кнопкой мыши подключение к локальной сети и выберите пункт "Свойства".

4.  Выберите пункт "Протокол Интернета версии 4 (TCP/IPv4)" и нажмите кнопку "Свойства".

5.  Убедитесь, что серверу присвоен статический IP-адрес.

    ![VerifystaticIPaddressyourPrimaryDC1][VerifystaticIPaddressyourPrimaryDC1]

## <span id="installforest"></span></a>Шаг 2: установка леса предприятия

1.  Работая в сеансе удаленного рабочего стола на виртуальной машине, щелкните **Пуск**, введите **dcpromo** и нажмите клавишу ВВОД.

    ![InstallCorpForest1][InstallCorpForest1]

2.  На странице приветствия нажмите кнопку **Далее**.

    ![InstallCorpForest2][InstallCorpForest2]

3.  На странице совместимости операционной системы нажмите кнопку **Далее**.

    ![InstallCorpForest3][InstallCorpForest3]

4.  На странице "Выберите конфигурацию развертывания", выберите пункт **Создать новый домен в новом лесу** и нажмите кнопку **Далее**.

    ![InstallCorpForest4][InstallCorpForest4]

5.  На странице "Укажите имя корневого домена леса" введите **corp.contoso.com**, полное доменное имя (FQDN) корневого домена леса, затем нажмите кнопку **Далее** .

    ![InstallCorpForest5][InstallCorpForest5]

6.  На странице "Задайте режим работы леса", выберите **Windows Server 2008 R2** и нажмите кнопку **Далее**.

    ![InstallCorpForest6][InstallCorpForest6]

7.  На странице "Дополнительные параметры контроллера домена" убедитесь, что выбран пункт **DNS-сервер** и нажмите кнопку **Далее**.

    ![InstallCorpForest7][InstallCorpForest7]

8.  Если появится следующее предупреждение делегирования DNS, нажмите кнопку **Да**.

    ![InstallCorpForest8][InstallCorpForest8]

9.  В разделе "Расположение" для базы данных Active Directory, файлов журнала и страницы SYSVOL введите или выберите расположение файлов и нажмите кнопку **Далее**.

    ![InstallCorpForest9][InstallCorpForest9]

10. На странице "Администратор восстановления служб каталога" введите и подтвердите пароль DSRM и нажмите кнопку **Далее**.

    ![InstallCorpForest10][InstallCorpForest10]

11. На странице "Сводка" подтвердите свой выбор и нажмите кнопку **Далее**.

    ![InstallCorpForest11][InstallCorpForest11]

12. После завершения работы мастера установки Active Directory нажмите кнопку **Готово** и щелкните **Перезагрузить сейчас**, чтобы завершить установку.

    ![InstallCorpForest12][InstallCorpForest12]

## <span id="subnets"></span></a>Шаг 3: создание подсетей и сайтов

1.  На основном контроллере домена щелкните "Пуск", затем "Администрирование" и выберите "Active Directory – сайты и службы".
2.  Щелкните **Сайты**, щелкните правой кнопкой мыши пункт **Подсети** и выберите **Новая подсеть**.

    ![CreateSubnetsandSites1][CreateSubnetsandSites1]

3.  В поле **Префикс::** введите **10.1.0.0/24**, выберите объект сайта **Default-First-Site-Name** и нажмите кнопку **ОК**.

    ![CreateSubnetsandSites2][CreateSubnetsandSites2]

4.  Щелкните правой кнопкой мыши пункт **Сайты** и выберите пункт **Новый сайт**.

    ![CreateSubnetsandSites3][CreateSubnetsandSites3]

5.  В поле "Имя" введите **CloudSite**, выберите **DEFAULTIPSITELINK** и нажмите кнопку **ОК**.

    ![CreateSubnetsandSites4][CreateSubnetsandSites4]

6.  Нажмите кнопку **ОК** для подтверждения создания сайта.

    ![CreateSubnetsandSites5][CreateSubnetsandSites5]

7.  Щелкните правой кнопкой мыши пункт **Подсети** и выберите пункт **Новая подсеть**.

    ![CreateSubnetsandSites6][CreateSubnetsandSites6]

8.  В поле **Префикс::** введите **10.4.2.0/24**, выберите объект сайта **CloudSite** и нажмите кнопку **ОК**.

    ![CreateSubnetsandSites7][CreateSubnetsandSites7]

## <span id="cloudsite"></span></a>Шаг 4: установка дополнительного контроллера домена на CloudSite

1.  Выполните вход на свою виртуальную машину, нажмите кнопку **Пуск**, введите **dcpromo** и нажмите клавишу ВВОД.

    ![AddDC1][AddDC1]

2.  На странице приветствия нажмите кнопку **Далее**.

    ![AddDC2][AddDC2]

3.  На странице совместимости операционной системы нажмите кнопку **Далее**.

    ![AddDC3][AddDC3]

4.  На странице конфигурации развертывания щелкните **Существующий лес**, щелкните **Добавить контроллер домена к существующему домену** и нажмите кнопку **Далее**.

    ![AddDC4][AddDC4]

5.  На странице сетевых учетных данных убедитесь, что контроллер домена устанавливается в домене **corp.contoso.com**, после чего введите учетные данные члена группы «Администраторы домена» (или используйте учетные данные corp\\administrator).

    ![AddDC5][AddDC5]

6.  На странице "Выберите домен" нажмите кнопку **Далее**.

    ![AddDC6][AddDC6]

7.  На странице "Выберите сайт" убедитесь, что выбран объект CloudSite и нажмите кнопку **Далее**.

    ![AddDC7][AddDC7]

8.  На странице "Дополнительные параметры контроллера домена" нажмите кнопку **Далее**.

    ![AddDC8][AddDC8]

9.  В окне предупреждения о необходимости присвоения статического IP-адреса щелкните пункт **Да, компьютер будет использовать динамически назначаемый DHCP-сервером IP-адрес (не рекомендуется)**.

    **Важно!**

    Хотя IP-адрес в виртуальной сети Azure является динамическим, срок его аренды равен сроку действия виртуальной машины. Поэтому нет необходимости задавать статический IP-адрес контроллера домена, устанавливаемого в виртуальной сети. Установка статического IP-адреса виртуальной машины приведет к появлению проблем со связью.

    ![AddDC9][AddDC9]

10. По запросу в окне предупреждения о делегировании DNS нажмите кнопку **Да**.

    ![AddDC10][AddDC10]

11. На странице "Местоположение базы данных, файлов журналов Active Directory и SYSVOL" нажмите кнопку "Обзор" и введите местоположение папки NTDS, созданной ранее на дополнительном диске с данными, затем нажмите кнопку **Далее** .

    ![AddDC11][AddDC11]

12. На странице "Администратор восстановления служб каталога" введите и подтвердите пароль DSRM и нажмите кнопку **Далее**.

    ![AddDC12][AddDC12]

13. На странице сводки нажмите кнопку **Далее**.

    ![AddDC13][AddDC13]

14. После завершения работы мастера установки Active Directory нажмите кнопку **Готово** и щелкните **Перезагрузить сейчас**, чтобы завершить установку.

    ![AddDC14][AddDC14]

## <span id="validate"></span></a>Шаг 5: проверка установки

1.  Повторно подключитесь к виртуальной машине.

2.  Щелкните **Пуск**, щелкните правой кнопкой мыши пункт **Командная строка** и выберите **Запуск от имени администратора**.

3.  Введите следующую команду и нажмите клавишу ВВОД: Dcdiag /c /v

4.  Убедитесь, что тесты выполнены успешно.

После настройки контроллера домена запустите приведенный ниже командлет Windows PowerShell, чтобы подготовить дополнительные виртуальные машины для автоматического присоединения к домену во время подготовки. Настройки разрешения DNS-клиента для виртуальной машины должны быть настроены при подготовке виртуальных машин. Подставьте правильные имена для вашего домена, имени ВМ и т. п.

Дополнительную информацию об использовании Windows PowerShell см. в разделах [Начало работы с Azure PowerShell][Начало работы с Azure PowerShell] и [Командлеты для управления Azure][Командлеты для управления Azure].

## <span id="provisionvm"></span></a>Шаг 6: подготовка виртуальной машины, присоединенной к домену при запуске

1.  Чтобы создать дополнительную виртуальную машину, присоединенную домену при первом запуске, откройте ISE Azure PowerShell, вставьте в окно следующий скрипт, замените заполнители собственными значениями и выполните сценарий.

    Чтобы определить внутренний IP-адрес контроллера домена, щелкните имя виртуальной сети, где работает этот контроллер.

    В следующем примере внутренний IP-адрес контроллера домена задан как 10.4.3.1. Командлет Add-AzureProvisioningConfig также принимает параметр -MachineObjectOU (указывается в виде полного доменного имени в Active Directory), который позволяет настраивать параметры групповых политик на всех виртуальных машинах этого контейнера.

    После подготовки виртуальных машин выполните вход, указав доменную учетную запись в формате UPN, например: <administrator@corp.contoso.com>.

        #Deploy a new VM and join it to the domain
        #-------------------------------------------
        #Specify my DC's DNS IP (10.4.3.1)
        $myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'

        # OS Image to Use
        $image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-ru-ru-30GB.vhd'
        $service = 'myazuresvcindomainM1'
        $AG = 'YourAffinityGroup'
        $vnet = 'YourVirtualNetwork'
        $pwd = 'p@$$w0rd'
        $size = 'Small'

        #VM Configuration
        $vmname = 'MyTestVM1'
        $MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
            Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
            Set-AzureSubnet -SubnetNames 'BackEnd'

        New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet

## <span id="backup"></span></a>Шаг 7: архивация контроллера домена

1.  Подключитесь к своей виртуальной машине.

2.  Щелкните **Пуск**, затем **Диспетчер сервера**, затем **Добавить компоненты** и выберите **Возможности системы архивации данных Windows Server**. Следуйте инструкциям по установке системы архивации данных Windows Server.

3.  Щелкните **Пуск**, затем **Архивация данных Windows Server**, затем **Однократная архивация**.

4.  Щелкните **Другие параметры** и нажмите кнопку **Далее**.

5.  Щелкните **Весь сервер** и нажмите кнопку **Далее**.

6.  Щелкните **Локальные диски** и нажмите кнопку **Далее**.

7.  Выберите диск назначения, который не содержит файлы операционной системы или базы данных Active Directory и нажмите кнопку "Далее".

    ![BackupDC][BackupDC]

8.  Подтвердите выбранные параметры резервного копирования и нажмите кнопку **Архивация**.

## <span id="test"></span></a>Шаг 8: тестирование аутентификации и авторизации

1.  Чтобы протестировать аутентификацию и авторизацию, создайте учетную запись пользователя домена в Active Directory.
    Войдите в систему виртуальной машины на каждом сайте и создайте общую папку.

2.  Проверьте доступ к общей папке, используя разные учетные записи, группы и разрешения.

## См. также

-   [Виртуальная сеть Azure][виртуальной сети Azure]

-   [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной][Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной]

-   [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями][Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями]

-   [Azure PowerShell][Начало работы с Azure PowerShell]

-   [Командлеты управления Azure][Командлеты для управления Azure]

  [виртуальной сети Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx
  [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx
  [Предварительные требования]: #Prerequisites
  [Шаг 1: проверка статического IP-адреса для основного контроллера домена]: #verifystaticip
  [Шаг 2: установка леса предприятия]: #installforest
  [Шаг 3: создание подсетей и сайтов]: #subnets
  [Шаг 4: установка дополнительного контроллера домена на CloudSite]: #cloudsite
  [Шаг 5: Проверка установки]: #validate
  [Шаг 6: Подготовка виртуальной машины, присоединенной к домену при запуске]: #provisionvm
  [Шаг 7: Выполните архивацию контроллера домена]: #backup
  [Шаг 8: тестирование аутентификации и авторизации]: #test
  [Настройте VPN «сеть-сеть» на портале управления]: http://msdn.microsoft.com/ru-ru/library/dn133795.aspx
  [Добавление виртуальной машины к виртуальной сети]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-networks-add-virtual-machine/
  [Создание делегирования зоны]: http://technet.microsoft.com/library/cc753500.aspx
  [Установка нового леса Active Directory в Azure]: /ru-ru/manage/services/networking/active-directory-forest/
  [Удаление контроллера домена из домена]: http://technet.microsoft.com/ru-ru/library/cc771844(v=WS.10).aspx
  [VerifystaticIPaddressyourPrimaryDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png
  [InstallCorpForest1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png
  [InstallCorpForest2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png
  [InstallCorpForest3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png
  [InstallCorpForest4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png
  [InstallCorpForest5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png
  [InstallCorpForest6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png
  [InstallCorpForest7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png
  [InstallCorpForest8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png
  [InstallCorpForest9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png
  [InstallCorpForest10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png
  [InstallCorpForest11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png
  [InstallCorpForest12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png
  [CreateSubnetsandSites1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png
  [CreateSubnetsandSites2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png
  [CreateSubnetsandSites3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png
  [CreateSubnetsandSites4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png
  [CreateSubnetsandSites5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png
  [CreateSubnetsandSites6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png
  [CreateSubnetsandSites7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png
  [AddDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png
  [AddDC2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png
  [AddDC3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png
  [AddDC4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png
  [AddDC5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png
  [AddDC6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png
  [AddDC7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png
  [AddDC8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png
  [AddDC9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png
  [AddDC10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png
  [AddDC11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png
  [AddDC12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png
  [AddDC13]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png
  [AddDC14]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png
  [Начало работы с Azure PowerShell]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx
  [Командлеты для управления Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841
  [BackupDC]: ./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png
  [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05

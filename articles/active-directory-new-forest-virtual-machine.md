<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Установка нового леса Active Directory в виртуальной сети Azure

В этом разделе показано, как создать новую среду Windows Server Active Directory на виртуальной машине (ВМ) в [виртуальной сети Azure][виртуальной сети Azure]. В данном случае виртуальная сеть Azure не соединена с локальной сетью.

Вас также могут заинтересовать следующие связанные разделы:

-   При необходимости можно дополнительно [настроить подключение VPN типа «сеть-сеть» с помощью мастера портала управления][настроить подключение VPN типа «сеть-сеть» с помощью мастера портала управления], а затем установить новый лес или расширить локальный лес на виртуальную сеть Azure. Инструкции по выполнению этих действий см. в разделе [Установка реплики контроллера домена Active Directory в виртуальной сети Azure][Установка реплики контроллера домена Active Directory в виртуальной сети Azure].
-   Концептуальное руководство об установке доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в статье [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure][Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure].
-   Пошаговое руководство по созданию тестовой среды в Azure, включающей AD DS, см. в статье [Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure][Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure].

## Оглавление

-   [В чем отличие от локальной версии?][В чем отличие от локальной версии?]
-   [Шаг 1. Создание виртуальной сети Azure][Шаг 1. Создание виртуальной сети Azure]
-   [Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера][Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера]
-   [Шаг 3: Установка Windows Server Active Directory][Шаг 3: Установка Windows Server Active Directory]
-   [Шаг 4: Задание DNS-сервера для виртуальной сети Azure][Шаг 4: Задание DNS-сервера для виртуальной сети Azure]
-   [Шаг 5: Создание виртуальных машин для членов домена и присоединение к домену][Шаг 5: Создание виртуальных машин для членов домена и присоединение к домену]

## <span id="differ"></span></a>В чем отличие от локальной версии?

Этот процесс не сильно отличается от установки контроллера домена в локальной версии Azure. Основные различия показаны в приведенной ниже таблице.

| Чтобы настроить...                         | Локальная система                                                                                             | Виртуальная сеть Azure                                                                                              |
|--------------------------------------------|---------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| **IP-адрес для контроллера домена**        | Назначьте статический IP-адрес в свойствах сетевого адаптера                                                  | Получите IP-адрес через DHCP и при желании запустите командлет Set-AzureStaticVNetIP, чтобы сделать его статическим |
| **Сопоставитель DNS-клиента**              | Задайте адреса предпочитаемого и дополнительного DNS-серверов в свойствах сетевого адаптера для членов домена | Задайте адрес DNS-сервера в свойствах виртуальной сети                                                              |
| **Хранилище базы данных Active Directory** | При желании измените расположение хранилища по умолчанию, C:\\                                                | Вам нужно изменить расположение хранилища по умолчанию, C:\\                                                        |

## <span id="createvnet"></span></a>Шаг 1: Создание виртуальной сети Azure

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  Создайте виртуальную сеть. Щелкните **Сети** \> **Создать виртуальную сеть**. Для завершения работы мастера используйте значения из приведенной ниже таблицы.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">На странице мастера…</th>
    <th align="left">Укажите такие значения</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Информация о виртуальной сети</strong></td>
    <td align="left"><p>Имя: введите имя для виртуальной сети</p>
    <p>Регион: выберите ближайший регион</p>
    <p>Территориальная группа: <strong>Создать новую территориальную группу</strong></p>
    <p>Имя территориальной группы: введите имя для своей территориальной группы</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS и VPN</strong></td>
    <td align="left"><p>Оставьте поле DNS-сервера пустым</p>
    <p>Не выбирайте также и параметр VPN</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Адресное пространство виртуальной сети</strong></td>
    <td align="left"><p>Имя подсети: введите имя своей подсети</p>
    <p>Начальный IP-адрес: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера

1.  Щелкните последовательно **Создать** \> **Среда выполнения приложений** \> **Виртуальная машина** \> **Из коллекции**.
2.  Для завершения работы мастера используйте значения из приведенной ниже таблицы.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">На странице мастера…</th>
    <th align="left">Укажите такие значения</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>операционная система</strong></td>
    <td align="left">Выберите <strong>Windows Server 2012 R2 Datacenter</strong></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Конфигурация виртуальной машины</strong></td>
    <td align="left"><p>Дата выпуска: сегодняшняя дата</p>
    <p>Имя компьютера: укажите уникальное значение</p>
    <p>Уровень: Standard</p>
    <p>Размер: выберите любой размер</p>
    <p>Имя пользователя: введите имя. Эта учетная запись пользователя будет членом встроенной группы &quot;Администраторы&quot;.</p>
    <p>Пароль: Должен быть не менее 8 символов и включать 3 символа следующих типов:</p>
    <ul>
    <li>прописная буква;</li>
    <li>строчная буква;</li>
    <li>цифра;</li>
    <li>специальный символ.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Облачная служба</strong></td>
    <td align="left"><p>Облачная служба: <strong>Создать новую облачную службу</strong></p>
    <p>Имя облачной службы: примите значение по умолчанию</p>
    <p>Регион, территориальная группа и виртуальная сеть: выберите созданную вами виртуальную сеть</p>
    <p>Подсеть виртуальной сети: выберите созданную вами подсеть.</p>
    <p>Учетная запись хранения: <strong>Использовать автоматически созданную учетную запись хранения</strong></p>
    <p>Группа доступности: <strong>None</strong></p>
    <p>Конечные точки: принять значения по умолчанию</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Агент виртуальной машины</strong></td>
    <td align="left">Выберите <strong>Установка агента виртуальной машины</strong></td>
    </tr>
    </tbody>
    </table>

3.  Динамический IP-адрес, назначенный виртуальной машине по умолчанию, действителен во время работы с облачной службой. Но после завершения работы виртуальной машины он изменится. Можно назначить статический IP-адрес, [выполнив командлет Azure PowerShell Set-AzureStaticVNetIP][выполнив командлет Azure PowerShell Set-AzureStaticVNetIP]. При этом IP-адрес сохранится, даже если потребуется завершить работу виртуальной машины.
4.  Присоедините дополнительный диск к виртуальной машине для хранения базы данных Active Directory, журналов и SYSVOL.
  5.  Щелкните **ВМ** \> **Присоединить** \> **Присоединить пустой диск**.
  6.  Укажите размер (например, 10 ГБ) и примите все остальные значения по умолчанию.
7.  Войдите в систему виртуальной машины и отформатируйте дополнительный диск.
  8.  Щелкните **Подключиться**, чтобы войти в виртуальную машину, щелкните **Открыть** для создания сеанса RDP, затем еще раз щелкните **Подключиться**.
  9.  Измените учетные данные для указанного нового имени пользователя и пароля.
  10. В диспетчере серверов щелкните **Средства** \> **Управление компьютером**.
  11. Щелкните **Управление дисками** и нажмите кнопку **ОК** для инициализации нового диска.
  12. Щелкните правой кнопкой мыши имя диска и выберите **Создать простой том**. Завершите работу мастера, чтобы отформатировать новый диск.

## <span id="installad"></span></a>Шаг 3: установка Windows Server Active Directory

[Установите службы AD DS][Установите службы AD DS], используя ту же процедуру, которая используется при локальной установке (то есть можно использовать пользовательский интерфейс, файл ответов или Windows PowerShell). Для установки нового леса необходимо предоставить учетные данные администратора. Чтобы указать расположение для базы данных Active Directory, журналов и SYSVOL, измените расположение хранилища по умолчанию с диска операционной системы на дополнительный диск данных, присоединенный к виртуальной машине.

После завершения установки контроллера домена снова подключитесь к виртуальной машине и войдите в контроллер домена. Не забудьте указать учетные данные домена.

</p>
## <span id="dns"></span></a>Шаг 4: задание DNS-сервера для виртуальной сети Azure

1.  Щелкните **Виртуальные сети**, дважды щелкните созданную вами виртуальную сеть и выберите **Настройка**.
2.  В разделе **DNS-серверы** введите имя и DIP контроллера домена, затем щелкните **Сохранить**.
3.  Выберите виртуальную машину и щелкните **Перезагрузить**, чтобы запустить виртуальную машину для настройки параметров сопоставителя DNS с использованием IP-адреса нового DNS-сервера.

## <span id="domainmembers"></span></a>Шаг 5: создание виртуальных машин для членов домена и присоединение к домену

Создайте дополнительные виртуальные машины для подготовки компьютеров-членов домена. Можно использовать пользовательский интерфейс или Azure PowerShell. При использовании пользовательского интерфейса просто выполните те же действия, что и при создании первой виртуальной машины. Затем присоедините виртуальные машины к домену так же, как в локальной версии. При использовании Azure PowerShell можно подготовить виртуальные машины и присоединить их к домену при первом запуске, как показано в следующем примере.

В данном примере мы создаем присоединенную к домену виртуальную машину с именем DC2, на которой запущен Windows Server 2012 R2 Datacenter. После подготовки DC2 войдите в нее как администратор домена и разверните ее как реплику DC.

Чтобы получить имена образов, можно выполнить командлет Get-AzureVMImage. Например, чтобы вернуть список образов Windows Server 2012 R2, выполните Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

    '
    cls
    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"
    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'
    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 
    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

При повторном выполнении скрипта необходимо предоставить уникальное значение для переменной $service. Можно выполнить командлет Test-AzureName -Service *имя службы*, результат которого показывает, используется ли уже это имя.

## См. также

-   [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure][1]

-   [Настройка виртуальной сети только для облака на портале управления][Настройка виртуальной сети только для облака на портале управления]

-   [Настройка VPN «cеть-сеть» на портале управления][Настройка VPN «cеть-сеть» на портале управления]

-   [Установка реплики контроллера домена Active Directory в виртуальной сети Azure][2]

-   [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной][Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной]

-   [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями][Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями]

-   [Виртуальная сеть Azure][виртуальной сети Azure]

-   [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]

-   [Azure PowerShell][Azure PowerShell]

-   [Командлеты управления Azure][Командлеты управления Azure]

-   [Задание статического IP-адреса виртуальной машины Azure][Задание статического IP-адреса виртуальной машины Azure]

-   [Как назначать статический IP-адрес виртуальной машине Azure][Как назначать статический IP-адрес виртуальной машине Azure]

-   [Установка нового леса Active Directory][Установите службы AD DS]

-   [Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)][Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)]

-   [Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure][Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure]

  [виртуальной сети Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx
  [настроить подключение VPN типа «сеть-сеть» с помощью мастера портала управления]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn133795.aspx
  [Установка реплики контроллера домена Active Directory в виртуальной сети Azure]: http://www.windowsazure.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx
  [Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure]: http://www.microsoft.com/ru-ru/download/details.aspx?id=41684
  [В чем отличие от локальной версии?]: #differ
  [Шаг 1. Создание виртуальной сети Azure]: #createvnet
  [Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера]: #createvm
  [Шаг 3: Установка Windows Server Active Directory]: #installad
  [Шаг 4: Задание DNS-сервера для виртуальной сети Azure]: #dns
  [Шаг 5: Создание виртуальных машин для членов домена и присоединение к домену]: #domainmembers
  [портал управления Azure]: https://manage.windowsazure.com
  [выполнив командлет Azure PowerShell Set-AzureStaticVNetIP]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn630228.aspx
  [Установите службы AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [1]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Настройка виртуальной сети только для облака на портале управления]: http://msdn.microsoft.com/ru-ru/library/dn631643.aspx
  [Настройка VPN «cеть-сеть» на портале управления]: http://msdn.microsoft.com/ru-ru/library/dn133795.aspx
  [2]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Установка и настройка Azure PowerShell]: http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx
  [Командлеты управления Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841
  [Задание статического IP-адреса виртуальной машины Azure]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Как назначать статический IP-адрес виртуальной машине Azure]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)]: http://technet.microsoft.com/ru-ru/library/hh831734.aspx

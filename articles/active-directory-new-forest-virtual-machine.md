<properties urlDisplayName="Active Directory forest" pageTitle="Установка леса Active Directory в виртуальной сети Azure" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="Justinha" />




#Установка нового леса Active Directory в виртуальной сети Azure

В этом разделе показано, как создать новую среду Windows Server Active Directory на виртуальной машине (ВМ) в [виртуальной сети Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx). В данном случае виртуальная сеть Azure не подключена к локальной сети. 

Вас также могут заинтересовать следующие связанные разделы:

- При необходимости можно дополнительно [настроить подключение VPN типа "сеть-сеть" с помощью мастера портала управления](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn133795.aspx), а затем установить новый лес или расширить локальный лес на виртуальную сеть Azure. Инструкции по выполнению этих действий приведены в разделе [Установка реплики контроллера домена Active Directory в виртуальной сети Azure](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Теоретические инструкции по установке доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в статье [Рекомендации по развертыванию Windows Server Active Directory на виртуальных машинах Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx).
-  Пошаговые инструкции по созданию тестовой среды в Azure, включающей AD DS, см. в документе [Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure](http://www.microsoft.com/ru-ru/download/details.aspx?id=41684).



##Оглавление##

* [В чем отличие от локальной версии?](#differ)
* [Шаг 1. Создание виртуальной сети Azure](#createvnet)
* [Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера](#createvm)
* [Шаг 3: Установка Windows Server Active Directory](#installad)
* [Шаг 4: Задание DNS-сервера для виртуальной сети Azure](#dns)
* [Шаг 5: Создание виртуальных машин для членов домена и присоединение к домену](#domainmembers)


<h2><a id="differ"></a>В чем отличие от локальной версии?</h2>
Этот процесс не сильно отличается от установки контроллера домена в локальной версии Azure. Основные различия перечислены в приведенной ниже таблице. 

Чтобы настроить...  | Локальная версия  | Виртуальная сеть Azure	
------------- | -------------  | ------------
**IP-адрес контроллера домена**  | Назначьте статический IP-адрес в свойствах сетевого адаптера   | Запустите командлет Set-AzureStaticVNetIP для назначения статического IP-адреса
**Сопоставитель DNS-клиента**  | Задайте в свойствах сетевого адаптера адреса предпочитаемого и дополнительного DNS-серверов для членов домена   | Задайте адрес DNS-сервера в свойствах виртуальной сети
**Хранилище базы данных Active Directory**  | При необходимости измените расположение хранилища по умолчанию C:\  | Необходимо изменить расположение хранилища по умолчанию C:\



<h2><a id="createvnet"></a>Шаг 1. Создание виртуальной сети Azure</h2>
1. Выполните вход на [портал управления Azure](https://manage.windowsazure.com).
2. Создайте виртуальную сеть. Щелкните <b>Сети</b> > <b>Создать виртуальную сеть</b>. Для завершения работы мастера используйте значения из приведенной ниже таблицы. 

	На странице мастера...   | Укажите следующие значения
	------------- | -------------
	**Информация о виртуальной сети**  | <p>Имя: введите имя своей виртуальной сети</p><p>Регион: выберите ближайший регион</p>
	**DNS и VPN**  | <p>Оставьте пустым поле DNS-сервера</p><p>Не выбирайте никаких параметров VPN</p>
	**Пространства адресов виртуальной сети**  | <p>Имя подсети: введите имя своей подсети</p><p>Начальный IP-адрес: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Шаг 2: Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера</h2>
 
1. Щелкните <b>Создать</b> > <b>Среда выполнения приложений</b> > <b>Виртуальная машина</b> > <b>Из коллекции</b>. 
2. Для завершения работы мастера используйте значения из приведенной ниже таблицы.

	На странице мастера...   | Укажите следующие значения
	------------- | -------------
	**Операционная система**  | Выберите **Windows Server 2012 R2 Datacenter**
	**Конфигурация виртуальной машины**  | <p>Дата выпуска: сегодняшняя дата</p><p>Имя компьютера: укажите уникальное значение</p><p>Уровень: стандартный</p><p>Размер: выберите любой размер</p><p>Имя пользователя: введите имя. Эта учетная запись пользователя будет членом встроенной группы "Администраторы". </p><p>Пароль: должен содержать не менее 8 символов и включать 3 символа следующих типов: </p><ul><li>прописная буква;</li><li>строчная буква;</li><li>число;</li><li>специальный знак</li></ul>
	**Облачная служба**  | <p>Облачная служба: <b>создайте новую облачную службу</b></p><p>Имя облачной службы: примите значение, установленное по умолчанию</p><p>Регион, территориальная группа и виртуальная сеть: выберите виртуальную сеть, которую вы создали</p><p>Подсеть виртуальной сети: выберите созданную вами подсеть. </p><p>Учетная запись хранения: <b>используйте автоматически созданную учетную запись хранения</b></p><p>Группа доступности: <b>нет</b></p><p>Конечные точки: примите значения по умолчанию</p>
	**Агент ВМ**  | Выберите **Установить агент ВМ**

1. Динамический IP-адрес, назначенный виртуальной машине по умолчанию, действителен во время работы с облачной службой. Но после завершения работы виртуальной машины он изменится. Можно назначить статический IP-адрес, [выполнив командлет Azure PowerShell Set-AzureStaticVNetIP](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn630228.aspx). При этом IP-адрес сохранится, даже если потребуется завершить работу виртуальной машины. 
2. Присоедините дополнительный диск к виртуальной машине для хранения базы данных Active Directory, журналов и SYSVOL. 
  3. Щелкните <b>ВМ</b> > <b>Присоединить</b> > <b>Присоединить пустой диск</b>. 
  4. Укажите размер (например, 10 ГБ) и примите все остальные значения по умолчанию.
3. Войдите в систему виртуальной машины и отформатируйте дополнительный диск. 
  4. Щелкните <b>Подключиться</b> для входа в виртуальную машину, щелкните <b>Открыть</b> для создания сеанса RDP, затем еще раз щелкните <b>Подключиться</b>.
  4. Измените учетные данные для указанного нового имени пользователя и пароля.
  5. В диспетчере серверов щелкните <b>Средства</b> > <b>Управление компьютером</b>. 
  6. Щелкните <b>Управление дисками</b> и нажмите кнопку <b>ОК</b> для инициализации нового диска. 
  6. Щелкните правой кнопкой мыши имя диска и выберите <b>Создать простой том</b>. Завершите мастер, чтобы отформатировать новый диск. 

<h2><a id="installad"></a>Шаг 3: Установка Windows Server Active Directory</h2>
[Установите службы AD DS](http://technet.microsoft.com/library/jj574166.aspx), используя ту же процедуру, которая используется при локальной установке (то есть можно использовать пользовательский интерфейс, файл ответов или Windows PowerShell). Для установки нового леса необходимо предоставить учетные данные администратора. Чтобы указать расположение для базы данных Active Directory, журналов и SYSVOL, измените расположение хранилища по умолчанию с диска операционной системы на дополнительный диск данных, присоединенный к виртуальной машине. 
<p>После завершения установки контроллера домена снова подключитесь к виртуальной машине и войдите в контроллер домена. Не забудьте указать учетные данные домена.</p>

<h2><a id="dns"></a>Шаг 4: Задание DNS-сервера для виртуальной сети Azure</h2>
1. Щелкните <b>Виртуальные сети</b>, дважды щелкните созданную вами виртуальную сеть и выберите <b>Настройка</b>. 
2. В разделе <b>DNS-серверы</b> введите имя и DIP контроллера домена, затем щелкните <b>Сохранить</B>. 
3. Выберите виртуальную машину и щелкните <b>Перезагрузить</b>, чтобы виртуальная машина выполнила настройку параметров сопоставителя DNS с использованием IP-адреса нового DNS-сервера. 


<h2><a id="domainmembers"></a>Шаг 5: Создание виртуальных машин для членов домена и присоединение к домену</h2>
<p>Создайте дополнительные виртуальные машины для подготовки компьютеров-членов домена. Можно использовать пользовательский интерфейс или Azure PowerShell. При использовании пользовательского интерфейса просто выполните те же действия, что и при создании первой виртуальной машины. Затем присоедините виртуальные машины к домену так же, как в локальной версии. При использовании Azure PowerShell можно подготовить виртуальные машины и присоединить их к домену при первом запуске, как показано в следующем примере. </p><p>В данном примере будет создана присоединенная к домену виртуальная машина с именем DC2, на которой запущен Windows Server 2012 R2 Datacenter. После подготовки DC2 войдите в нее как администратор домена и разверните ее как реплику DC. </p><p>Для получения имен образов можно выполнить командлет Get-AzureVMImage. Например, чтобы вернуть список образов Windows Server 2012 R2, следует выполнить Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
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
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Если повторно выполнить сценарий, необходимо предоставить уникальное значение переменной $service. Можно выполнить командлет Test-AzureName -Service <i>имя службы</i>, результат которого показывает, используется ли уже это имя. 	

## См. также

-  [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Настройка виртуальной сети только для облака на портале управления](http://msdn.microsoft.com/ru-ru/library/dn631643.aspx)

-  [Настройка VPN "cеть-сеть" на портале управления](http://msdn.microsoft.com/ru-ru/library/dn133795.aspx)

-  [Установка реплики контроллера домена Active Directory в виртуальной сети Azure](http://azure.microsoft.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Виртуальная сеть Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx)

-  [Установка и настройка Azure PowerShell](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx)

-  [Командлеты управления Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841)

-  [Задание статического IP-адреса виртуальной машины Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Как назначать статический IP-адрес виртуальной машине Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Установка нового леса Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)](http://technet.microsoft.com/ru-ru/library/hh831734.aspx)

-  [Руководство по лабораторному тестированию: базовая конфигурация Windows Server 2012 R2 в Azure](http://www.microsoft.com/ru-ru/download/details.aspx?id=41684)


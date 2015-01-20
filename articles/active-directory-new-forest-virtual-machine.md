<properties urlDisplayName="Active Directory forest" pageTitle="Установка леса Active Directory в виртуальной сети Azure" metaKeywords="" description="В этом учебнике объясняется, как создать новый лес Active Directory на виртуальной машине (ВМ) в виртуальной сети Windows Azure." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="Justinha" />




#Установка нового леса Active Directory в виртуальной сети Azure

В данном разделе описывается, как создать новую среду Windows Server Active Directory в виртуальной сети Azure на ВМ [виртуальной сети Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx). В данном случае виртуальная сеть Azure не соединена с локальной сетью. 

Вас также могут заинтересовать следующие связанные разделы:

- Можно также [настроить VPN между двумя сайтами с помощью мастера управления порталом](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn133795.aspx) и затем либо установить новый лес, либо расширить существующий локальный лес на виртуальную сеть Azure. Описание этих действий см. в разделе [Установка контроллера домена Active Directory реплики в виртуальной сети Azure](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Концептуальное руководство по установке доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в разделе [Рекомендации по развертыванию Windows Server Active Directory на виртуальных машинах Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx).
-  Пошаговое руководство по созданию тестовой среды в Azure, включающей AD DS, см. в статье [Руководство по лабораторному тестированию: Базовая конфигурация Windows Server 2012 R2 в Azure](http://www.microsoft.com/ru-ru/download/details.aspx?id=41684).



##Содержание##

* [Чем это отличается от локальной конфигурации?](#differ)
* [Шаг 1. Создание виртуальной сети Azure](#createvnet)
* [Шаг 2. Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера](#createvm)
* [Шаг 3. Установка Windows Server Active Directory](#installad)
* [Шаг 4. Задание DNS-сервера для виртуальной сети Azure](#dns)
* [Шаг 5. Создание виртуальных машин для членов домена и присоединение к домену](#domainmembers)


<h2><a id="differ"></a>В чем отличие от локальной версии?</h2>
Этот процесс не сильно отличается от установки контроллера домена в локальной версии Azure. Основные различия показаны в приведенной ниже таблице. 

Чтобы настроить...  | Локальная конфигурация  | Виртуальная сеть Azure	
------------- | -------------  | ------------
**IP-адрес для контроллера домена**  | Назначьте статический IP-адрес в свойствах сетевой карты   | Запустите командлет Set-AzureStaticVNetIP, чтобы назначить статический IP-адрес
**Сопоставитель клиента DNS**  | Задайте предпочтительный и альтернативный адреса сервера DNS в свойствах сетевого адаптера членов домена   | Задайте адрес сервера DNS в свойствах виртуальной сети
**Хранилище базы данных Active Directory**  | Можно изменить расположение хранилища по умолчанию с диска C:\  | Потребуется изменить расположение хранилища по умолчанию с диска C:\



<h2><a id="createvnet"></a>Шаг 1. Создание виртуальной сети Azure</h2>
1. Войдите на [портал управления Azure](https://manage.windowsazure.com).
2. Создайте виртуальную сеть. Щелкните <b>Сети</b> > <b>Создать виртуальную сеть</b>. Для завершения работы мастера используйте значения из приведенной ниже таблицы. 

	На странице мастера...  | Укажите следующие значения
	------------- | -------------
	**Сведения о виртуальной сети**  | <p>Имя: введите имя для виртуальной сети</p><p>Регион: выберите ближайший регион</p>
	**DNS и VPN**  | <p>Оставьте поле DNS-сервера пустым</p><p>Не выбирайте также и параметр VPN</p>
	**Адресные пространства виртуальной сети**  | <p>Имя подсети: введите имя своей подсети</p><p>Начальный IP-адрес: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Шаг 2. Создание виртуальной машины для выполнения ролей контроллера домена и DNS-сервера</h2>
 
1. Щелкните <b>Создать</b> > <b>Среда выполнения приложений</b> > <b>Виртуальная машина</b> > <b>Из коллекции</b>. 
2. Для завершения работы мастера используйте значения из приведенной ниже таблицы.

	На странице мастера...  | Укажите следующие значения
	------------- | -------------
	**Операционная система**  | Выберите **Windows Server 2012 R2 Datacenter**
	**Конфигурация виртуальной машины**  | <p>Дата выпуска: Сегодняшняя дата</p><p>Имя компьютера: укажите уникальное значение</p><p>Уровень: Standard</p><p>Размер: выберите любой размер</p><p>Имя пользователя: введите имя. Эта учетная запись пользователя будет членом встроенной группы "Администраторы". </p><p>Пароль: Должен быть не менее 8 символов и включать 3 символа следующих типов:</p><ul><li>прописная буква;</li><li>строчная буква;</li><li>цифра;</li><li>специальный символ.</li></ul>
	**Облачная служба**  | <p>Облачная служба: <b>Создать новую облачную службу</b></p><p>Имя облачной службы: примите значение по умолчанию</p><p>Регион, территориальная группа и виртуальная сеть: выберите созданную вами виртуальную сеть</p><p>Подсеть виртуальной сети: выберите созданную вами подсеть. </p><p>Учетная запись хранения: <b>Использовать автоматически созданную учетную запись хранения</b></p><p>Группа доступности: <b>None</b></p><p>Конечные точки: принять значения по умолчанию</p>
	**Агент ВМ**  | Выберите **Установить агент ВМ**

1. Динамический IP-адрес, назначенный виртуальной машине по умолчанию, действителен во время работы с облачной службой. Но после завершения работы виртуальной машины он изменится. Назначьте статический IP-адрес, [запустив командлет Set-AzureStaticVNetIP Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn630228.aspx), чтобы IP-адрес сохранился, если потребуется выключить виртуальную машину. 
2. Присоедините дополнительный диск к виртуальной машине для хранения базы данных Active Directory, журналов и SYSVOL. 
  3. Щелкните <b>ВМ</b> > <b>Присоединить</b> > <b>Присоединить пустой диск</b>. 
  4. Укажите размер (например, 10 ГБ) и примите все остальные значения по умолчанию.
3. Войдите в систему виртуальной машины и отформатируйте дополнительный диск. 
  4. Щелкните <b>Подключение</b> , чтобы войти на виртуальную машину, щелкните <b>Открыть</b> , чтобы создать сеанс RDP, а затем щелкните <b>Подключение</b> еще раз.
  4. Измените учетные данные для указанного нового имени пользователя и пароля.
  5. В диспетчере серверов щелкните <b>Средства</b> > <b>Управление компьютером</b>. 
  6. Щелкните <b>"Управление дисками"</b> и нажмите <b>кнопку "ОК"</b> , чтобы инициализировать новый диск. 
  6. Щелкните правой кнопкой мыши имя диска и выберите <b>Создать простой том</b>. Завершите работу мастера, чтобы отформатировать новый диск. 

<h2><a id="installad"></a>Шаг 3. установка Windows Server Active Directory</h2>
[Установите AD DS](http://technet.microsoft.com/library/jj574166.aspx), выполнив ту же процедуру, что и при локальной установке (то есть можно пользоваться тем же пользовательским интерфейсом, файлом ответов или Windows PowerShell). Для установки нового леса необходимо предоставить учетные данные администратора. Чтобы указать расположение для базы данных Active Directory, журналов и SYSVOL, измените расположение хранилища по умолчанию с диска операционной системы на дополнительный диск данных, присоединенный к виртуальной машине. 
<p>После завершения установки контроллера домена снова подключитесь к виртуальной машине и войдите в контроллер домена. Не забудьте указать учетные данные домена.</p>

<h2><a id="dns"></a>Шаг 4. задание DNS-сервера для виртуальной сети Azure</h2>
1. Щелкните <b>Виртуальные сети</b>, дважды щелкните созданную виртуальную сеть и выберите <b>Настройка</b>. 
2. В разделе <b>"Серверы DNS"</b>введите имя и DIP для контроллера домена и нажмите <b>Сохранить</B>. 
3. Выберите виртуальную машину и нажмите <b>Перезагрузить</b> , чтобы ВМ могла настроить параметры сопоставителя DNS для IP-адреса нового сервера DNS. 


<h2><a id="domainmembers"></a>Шаг 5. создание виртуальных машин для членов домена и присоединение к домену</h2>
<p>Создайте дополнительные виртуальные машины для подготовки компьютеров-членов домена. Можно использовать пользовательский интерфейс или Azure PowerShell. При использовании пользовательского интерфейса просто выполните те же действия, что и при создании первой виртуальной машины. Затем присоедините виртуальные машины к домену так же, как в локальной версии. При использовании Azure PowerShell можно подготовить виртуальные машины и присоединить их к домену при первом запуске, как показано в следующем примере. </p><p>В данном примере мы создаем присоединенную к домену виртуальную машину с именем DC2, на которой запущен Windows Server 2012 R2 Datacenter. После подготовки DC2 войдите в нее как администратор домена и разверните ее как реплику DC. </p><p>Чтобы получить имена образов, можно выполнить командлет Get-AzureVMImage. Например, чтобы вернуть список образов Windows Server 2012 R2, выполните Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
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

При повторном выполнении скрипта необходимо предоставить уникальное значение для переменной $service. Можно запустить команду "Test-AzureName -Service <i>имя_службы"</i>, которая показывает, занято ли уже это имя. 	

## См. также

-  [Рекомендации по развертыванию Windows Server Active Directory на виртуальных машинах Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Настройте чисто облачную виртуальную сеть на портале управления](http://msdn.microsoft.com/ru-ru/library/dn631643.aspx)

-  [Настройте VPN между сайтами на портале управления](http://msdn.microsoft.com/ru-ru/library/dn133795.aspx)

-  [Установите контроллер домена реплики Active Directory в виртуальной сети Azure](http://azure.microsoft.com/ru-ru/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Microsoft Azure IT Pro IaaS: (01) Основы виртуальных машин](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS: (05) Создание виртуальных сетей и обмена данными с локальными сетями](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Виртуальная сеть Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx)

-  [Как установить и настроить Azure PowerShell](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx)

-  [Командлеты управления Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841)

-  [Настройте статический IP-адрес для виртуальной машины Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Как назначить статический IP-адрес виртуальной машине Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Установка нового леса Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)](http://technet.microsoft.com/ru-ru/library/hh831734.aspx)

-  [Руководство по практической работе. Базовая конфигурация Windows Server 2012 R2 в Azure](http://www.microsoft.com/ru-ru/download/details.aspx?id=41684)


<!--HONumber=35.2-->

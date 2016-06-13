<properties 
	pageTitle="Тестовая среда Office 365 DirSync | Microsoft Azure" 
	description="Узнайте, как настроить сервер синхронизации каталогов (DirSync)Office 365 в гибридном облаке для ИТ-специалистов или тестирования разработки." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Настройка синхронизации каталогов (DirSync) Office 365 в гибридном облаке для тестирования
 
В этом разделе описываются шаги по созданию гибридной облачной среды для тестирования синхронизации каталогов (DirSync) Office 365 с синхронизацией пароля, размещенного в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Данная конфигурация имитирует сервер DirSync в рабочей среде Azure из вашего расположения в Интернете. В ее состав входит:

- упрощенная локальная сеть (подсеть Corpnet);
- виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET);
- VPN-подключение типа "сеть-сеть";
- пробная подписка Office 365 FastTrack;
- сервер DirSync, на котором запущен инструмент Azure AD Connect, и дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

- разрабатывать и тестировать приложения для Office 365, использующие синхронизацию с локальным доменом Active Directory с помощью синхронизации пароля.
- выполнять тестирование этой облачной рабочей нагрузки ИТ-среды.

Существует три основных этапа настройки данной тестовой среды гибридного облака:

1.	настройка гибридной облачной среды для тестирования.
2.	настройка пробной версии Office 365 FastTrack.
3.	настройка сервера DirSync (DS1).

Если у вас еще нет подписки Azure, вы можете зарегистрировать бесплатную учетную запись на странице [бесплатной ознакомительной версии Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас есть подписка MSDN или Visual Studio, см. раздел [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Этап 1. Настройка гибридной облачной среды

Следуйте указаниям в статье [Настройка гибридной облачной среды для тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph1.png)

> [AZURE.NOTE] На этапе 1 можно также настроить [смоделированную гибридную облачную среду для тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).

## Этап 2. Настройка пробной версии Office 365 FastTrack

Чтобы запустить пробную версию Office 365 FastTrack, необходимо вымышленное имя компании и учетная запись Майкрософт. В качестве имени компании рекомендуется использовать вариант названия компании Contoso. Это вымышленная компания, которая используется в учебных материалах Майкрософт. Но эта рекомендация не носит обязательный характер.

Далее необходимо зарегистрировать новую учетную запись Майрософт. Перейдите на страницу **http://outlook.com** и создайте учетную запись с электронным адресом типа user123@outlook.com. Вам предстоит зарегистрироваться для получения пробной версии Office 365 с использованием этой учетной записи.

Теперь зарегистрируйтесь для получения новой пробной версии Office 365 корпоративный E3.

1.	Войдите в CLIENT1 с учетными данными CORP\\User1.
2.	Откройте Internet Explorer и перейдите на страницу **https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Пройдите регистрацию для получения пробной версии Office 365 корпоративный E3.

При появлении запроса **служебной электронной почты** укажите новую учетную запись Майкрософт.

При появлении предложения создать идентификатор введите имя начальной учетной записи Office 365, вымышленное название компании и пароль. Запишите полученный электронный адрес (например, user123@contoso123.onmicrosoft.com) и пароль в надежном месте. Эта информация понадобится для завершения настройки Azure AD Connect на этапе 3.

После этого должна отобразиться главная страница портала Office 365. В верхней ленте щелкните **Администратор**, а затем нажмите кнопку **Office 365**. Открывается страница центра администрирования Office 365. Оставьте эту страницу открытой на компьютере CLIENT1.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph2.png)

## Этап 3. Настройка сервера DirSync (DS1)

На портале Azure запустите компьютер DC2, если это необходимо.

Далее необходимо создать виртуальную машину Azure для DS1 с помощью следующих команд, которые вводятся в командной строке Azure PowerShell локального компьютера. Перед выполнение этих команд заполните значения переменных и удалите символы < and >.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName DS1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DS1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DS1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DS1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем используйте портал Azure, чтобы подключиться к виртуальной машине DS1 с помощью учетных данных локального администратора.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на DS1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 192.168.0.4.

После этого присоедините виртуальную машину DS1 к домену Active Directory CORP, выполнив следующие команды в командной строке Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Используйте учетную запись CORP\\User1 при появлении запроса на ввод учетных данных домена для команды **Add-Computer**.

После перезапуска используйте портал Azure для подключения к DS1 с помощью учетной записи CORP\\User1 и ее пароля.

Затем установите платформу .NET 3.5 на DS1 с помощью этой команды в командной строке Windows PowerShell с правами администратора.

	Add-WindowsFeature NET-Framework-Core

Далее следует включить синхронизацию каталогов для пробной версии Office 365 FastTrack.

1.	На компьютере CLIENT1 на странице **Центр администрирования Office 365** в левой области щелкните **Пользователи**, а затем — **Активные пользователи**.
2.	Для **синхронизации Active Directory** щелкните **Настройка**.
3.	На странице «Настройка синхронизации Active Directory и управление ею» на шаге 3 щелкните **Активировать**.
4.	При появлении запроса **Активировать синхронизацию Active Directory?** щелкните **Активировать**. После этого на шаге 3 отображается сообщение **Синхронизация Active Directory включена**.
5.	Оставьте открытой страницу **Настройка синхронизации Active Directory и управление ею** на компьютере CLIENT1.

После этого в командной строке Windows PowerShell на DC1 **по очереди** выполните следующие команды, чтобы создать новое подразделение contoso\_users и добавить две новые учетные записи для пользователей Marci Kaufman и Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

При выполнении каждой команды **New-ADUser** Windows PowerShell запрашивает пароль нового пользователя. Запишите эти пароли и сохраните их в надежном месте. Они вам потребуются позднее.

Установите и настройте инструмент Azure AD Connect на DS1.

1.	Запустите Internet Explorer, введите в **адресной строке** **https://www.microsoft.com/download/details.aspx?id=47594** и нажмите клавишу ВВОД.
2.	Запустите программу установки Microsoft Azure AD Connect.
3.	На рабочем столе дважды щелкните **Azure AD Connect**.
4.	На странице **приветствия** выберите **Я принимаю условия лицензионного соглашения и заявления о конфиденциальности**, а затем нажмите кнопку **Продолжить**.
5.	На странице **Стандартные параметры** щелкните **Использовать стандартные параметры**.
6.	На странице **Подключение к Azure AD** введите электронный адрес и пароль начальной учетной записи, созданной при настройке пробной версии Office 365 FastTrack на этапе 2. Нажмите кнопку **Далее**.
7.	На странице **Подключение к AD DS** в поле **Имя пользователя** введите **CORP\\User1**, а в поле **Пароль** — пароль учетной записи User1. Нажмите кнопку Далее.
8.	На странице **Готово к настройке** проверьте параметры и нажмите кнопку **Установить**.
9.	На странице **Настройка завершена** щелкните **Выйти**.

Далее убедитесь, что учетные записи пользователей в домене CORP синхронизируются с Office 365. Обратите внимание, что выполнение синхронизации может занять несколько минут.

На компьютере CLIENT1 на странице **Настройка синхронизации Active Directory и управление ею** перейдите по ссылке **пользователи** на шаге 6 этой страницы. Если синхронизация службы каталогов завершилась успешно, на экране должно отображаться следующее.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-example.png)

Столбец **Состояние** указывает, что учетная запись получена в результате синхронизации с доменом Active Directory.

Далее продемонстрируем синхронизацию пароля Office 365 с учетной записью Active Directory Lynda Meyer.

1.	На компьютере CLIENT1 на странице **Активные пользователи** выберите учетную запись **Lynda Meyer**.
2.	В свойствах учетной записи Lynda Meyer в разделе **Назначенная лицензия** щелкните **Изменить**.
3.	На вкладке **Назначить лицензию** выберите расположение в поле **Задание местонахождения пользователя** (например, США).
4.	Выберите **Microsoft Office 365 (план E3)** и щелкните **Сохранить**.
5.	Закройте Internet Explorer.
6.	Запустите Internet Explorer и перейдите на страницу **http://portal.microsoftonline.com**.
7.	Войдите в систему с учетными данными Lynda Meyer в Office 365. Ее имя пользователя будет lyndam@<*ваше вымышленное имя*>.onmicrosoft.com. Пароль — пароль учетной записи пользователя Lynda Meyer Active Directory.
8.	После успешного входа можно увидеть главную страницу портала Office 365 с надписью **Изменим мир сегодня**.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Эта среда готовы для выполнения тестирования приложений Office 365, использующих функцию Office 365 DirSync, или для тестирования функции DirSync и производительности из DS1.

## Дальнейшие действия

- Развертывание этой рабочей нагрузки [в рабочей среде](http://technet.microsoft.com/library/dn635310.aspx).

<!---HONumber=AcomDC_0601_2016-->
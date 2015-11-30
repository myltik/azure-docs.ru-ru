<properties 
	pageTitle="Тестовая среда Office 365 DirSync | Microsoft Azure" 
	description="Узнайте, как настроить сервер синхронизации каталогов (DirSync)Office 365 в гибридном облаке для ИТ-специалистов или тестирования разработки." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/13/2015" 
	ms.author="josephd"/>

# Настройка синхронизации каталогов (DirSync) Office 365 в гибридном облаке для тестирования

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 

В этом разделе описываются шаги по созданию гибридной облачной среды для тестирования синхронизации каталогов (DirSync) Office 365 с синхронизацией пароля, размещенного в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
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

Если у вас еще нет подписки Azure, вы можете зарегистрироваться для бесплатного пробного использования на странице [Попробуйте Azure](http://azure.microsoft.com/pricing/free-trial/). При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Этап 1. Настройка гибридной облачной среды

Следуйте указаниям в теме [Создание гибридной облачной среды для тестирования](virtual-networks-setup-hybrid-cloud-environment-testing.md). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]На этапе 1 можно также настроить смоделированную гибридную облачную среду для тестирования. Следуйте указаниям из раздела [Создание имитации гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

## Этап 2. Настройка пробной версии Office 365 FastTrack

Чтобы запустить пробную версию Office 365 FastTrack, необходимо вымышленное имя компании и учетная запись Майкрософт. В качестве имени компании рекомендуется использовать вариант названия компании Contoso. Это вымышленная компания, которая используется в учебных материалах Майкрософт. Но эта рекомендация не носит обязательный характер.

Далее необходимо зарегистрировать новую учетную запись Майрософт. Перейдите на страницу ****http://outlook.com** и создайте учетную запись с электронным адресом типа user123@outlook.com. Вам предстоит зарегистрироваться для получения пробной версии Office 365 с использованием этой учетной записи.

Теперь зарегистрируйтесь для получения новой пробной версии Office 365 корпоративный E3.

1.	Войдите в CLIENT1 с учетными данными CORP\\User1.
2.	Откройте Internet Explorer и перейдите на страницу ****https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Пройдите регистрацию для получения пробной версии Office 365 корпоративный E3.

При появлении запроса **служебной электронной почты** укажите новую учетную запись Майкрософт.

При появлении предложения создать идентификатор введите имя начальной учетной записи Office 365, вымышленное название компании и пароль. Запишите полученный электронный адрес (например, user123@contoso123.onmicrosoft.com) и пароль в надежном месте. Эта информация понадобится для завершения настройки Azure AD Connect на этапе 3.

После этого должна отобразиться главная страница портала Office 365. В верхней ленте щелкните **Администратор**, а затем нажмите кнопку **Office 365**. Открывается страница центра администрирования Office 365. Оставьте эту страницу открытой на компьютере CLIENT1.

Это текущая конфигурация.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Этап 3. Настройка сервера DirSync (DS1)

Сначала необходимо создать виртуальную машину Azure для DS1 с помощью следующих команд, которые вводятся в командной строке Azure PowerShell локального компьютера. Перед выполнение этих команд заполните значения переменных и удалите символы < and >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Далее подключитесь к виртуальной машине DS1.

1.	На странице виртуальной машины на портале управления Azure щелкните **Работает** в столбце «СОСТОЯНИЕ» для виртуальной машины DS1.
2.	На панели задач щелкните **Подключиться**. 
3.	При появлении запроса на открытие DS1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
	- имя: **CORP\\User1**;
	- пароль: [пароль учетной записи User1].
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Да**.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на DS1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

Затем установите .NET 3.5 на DS1 с помощью этой команды в командной строке Windows PowerShell.

	Add-WindowsFeature NET-Framework-Core

Далее следует включить синхронизацию каталогов для пробной версии Office 365 FastTrack.

1.	На компьютере CLIENT1 на странице **Центр администрирования Office 365** в левой области щелкните **Пользователи**, а затем — **Активные пользователи**.
2.	Для **синхронизации Active Directory** щелкните **Настройка**.
3.	На странице «Настройка синхронизации Active Directory и управление ею» на шаге 3 щелкните **Активировать**.
4.	При появлении запроса **Активировать синхронизацию Active Directory?** щелкните **Активировать**. После этого на шаге 3 отобразится сообщение **Синхронизация Active Directory включена**.
5.	Оставьте открытой страницу **Настройка синхронизации Active Directory и управление ею** на компьютере CLIENT1.

Затем войдите в DC1 с учетной записью CORP\\User1 и откройте командную строку Windows PowerShell с правами администратора. По очереди выполните следующие команды для создания нового подразделения с именем contoso\_users и добавьте две новые учетные записи пользователей для Marci Kaufman и Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

При выполнении каждой команды Windows PowerShell запрашивает пароль нового пользователя. Запишите эти пароли и сохраните их в надежном месте. Они вам потребуются позднее.

Установите и настройте инструмент Azure AD Connect на DS1.

1.	Запустите Internet Explorer, введите в **адресной строке** ****https://www.microsoft.com/download/details.aspx?id=47594** и нажмите клавишу ВВОД.
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

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

Столбец **Состояние** указывает, что учетная запись получена в результате синхронизации с доменом Active Directory.

Далее продемонстрируем синхронизацию пароля Office 365 с учетной записью Active Directory Lynda Meyer.

1.	На компьютере CLIENT1 на странице **Активные пользователи** выберите учетную запись **Lynda Meyer**.
2.	В свойствах учетной записи Lynda Meyer в разделе **Назначенная лицензия** щелкните **Изменить**.
3.	На вкладке **Назначить лицензию** выберите расположение в поле **Задание местонахождения пользователя** (например, США).
4.	Выберите **Microsoft Office 365 (план E3)** и щелкните **Сохранить**.
5.	Закройте Internet Explorer.
6.	Запустите Internet Explorer и перейдите на страницу ****http://portal.microsoftonline.com**.
7.	Войдите в систему с учетными данными Lynda Meyer в Office 365. Ее имя пользователя будет lyndam@<*ваше вымышленное имя*>.onmicrosoft.com. Пароль — пароль учетной записи пользователя Lynda Meyer Active Directory.
8.	После успешного входа можно увидеть главную страницу портала Office 365 с надписью **Изменим мир сегодня**.

Это текущая конфигурация.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Эта среда готовы для выполнения тестирования приложений Office 365, использующих функцию Office 365 DirSync, или для тестирования функции DirSync и производительности из DS1.

## Дополнительные ресурсы

[Развертывание службы синхронизации каталогов Office 365 (DirSync) в Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Решения, использующие серверы Office и облако](http://technet.microsoft.com/library/dn262744.aspx)

[Настройка гибридной облачной среды для тестирования](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Создание имитации гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Гибридные облачные тестовые среды Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Руководство по реализации служб инфраструктуры Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Nov15_HO4-->
<properties linkid="develop-php-how-to-guides-web-sso" urlDisplayName="Единый вход в Интернете" pageTitle="Единый вход с помощью Azure Active Directory (PHP)" metaKeywords="веб-приложение PHP Azure, единый вход Azure, Azure PHP Active Directory" description="Узнайте, как создать веб-приложение PHP, использующее единый вход с помощью Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="PHP" title="Единый вход в Интернете с помощью PHP и Azure Active Directory" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

# Единый вход в Интернете с помощью PHP и Azure Active Directory

##<a name="introduction"></a>Введение

В этом учебнике показано, как разработчики PHP могут использовать Azure Active Directory, чтобы включить единый вход для пользователей Office 365. Вы научитесь:

* Выполнять подготовку веб-приложения в клиенте заказчика
* Обеспечивать защиту приложения с помощью WS-Federation

###Предварительные требования
Для данного пошагового руководства необходимы следующие компоненты среды разработки:

* [Пример PHP-кода для Azure Active Directory]
* [Пакеты Eclipse PDT 3.0.x All In One]
* PHP 5.3.1 (через установщик веб-платформы)
* Службы IIS 7.5 с поддержкой SSL
* Windows PowerShell
* [Командлеты PowerShell для Office 365]

###Оглавление
* [Введение][]
* [Шаг 1. Создание приложения PHP][]
* [Шаг 2. Подготовка приложения в клиенте каталога компании][]
* [Шаг 3. Защита приложения с использованием WS-Federation для входа сотрудников][]
* [Сводка][]

##<a name="createapp"></a>Шаг 1. Создание приложения PHP
На этом шаге рассказывается, как создать простое приложение PHP, которое будет представлять защищенный ресурс. Доступ к этому ресурсу будет предоставляться через федеративную проверку подлинности под управлением службы STS компании; этот вопрос будет рассмотрен подробно далее в этом же модуле.

1. Откройте новый экземпляр Eclipse.
2. В меню **Файл** выберите **Создать**, затем щелкните **Новый проект PHP**.
3. В диалоговом окне **Новый проект PHP** присвойте проекту имя *phpSample*, затем нажмите кнопку **Готово**.
4. В расположенном слева меню **обозревателя PHP** щелкните правой кнопкой мыши *phpProject*, выберите **Создать**, затем щелкните **PHP-файл**.
5. В диалоговом окне **Новый PHP-файл** присвойте файлу имя **index.php**, затем нажмите кнопку **Готово**.
6. Замените сгенерированную разметку приведенной ниже, а затем сохраните **index.php**.

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. Откройте **Диспетчер IIS**, для чего введите команду *inetmgr* в командной строке "Выполнить" и нажмите клавишу ВВОД.

8. В диспетчере IIS в расположенном слева меню разверните папку **Сайты**, щелкните правой кнопкой мыши **Веб-сайт по умолчанию** и выберите **Добавить приложение...**.

9. В диалоговом окне **Добавить приложение** задайте для элемента **Псевдоним** значение *phpSample*, а для элемента **Физический путь** — путь к файлу, в котором создается проект PHP.

10. В Eclipse в меню **Запуск** щелкните **Запуск**.

11. В меню **Запуск веб-приложения PHP** нажмите кнопку **ОК**.

12. Откроется страница **index.php** на новой вкладке в Eclipse. На странице просто должен отображаться текст: *Страница индексов*. 

##<a name="provisionapp"></a>Шаг 2. Подготовка приложения в клиенте каталога компании
На этом шаге рассказывается, как администратор пользователя Azure Active Directory готовит приложения PHP в клиенте пользователя и настраивает единый вход. После выполнения этого шага сотрудники компании могут проходить проверку подлинности в веб-приложении, используя свои учетные записи Office 365.

Процесс подготовки начинается с создания нового субъекта-службы для приложения. Azure Active Directory использует субъекты-службы для регистрации и проверки подлинности приложений в каталоге.

1. Загрузите и установите командлеты PowerShell для Office 365, если это еще не сделано.
2. В меню **Пуск** запустите консоль **Модуль Microsoft Online Services для Windows PowerShell**. Эта консоль предоставляет среду командной строки для настройки атрибутов клиента Office 365, например для создания и изменения субъектов-служб.
3. Чтобы импортировать необходимый модуль **MSOnlineExtended**, введите следующую команду и нажмите клавишу ВВОД:

		Import-Module MSOnlineExtended –Force
4. Чтобы подключиться к каталогу Office 365, необходимо предоставить учетные данные администратора компании. Введите следующую команду и нажмите клавишу ВВОД, а затем введите свои учетные данные в командной строке:

		Connect-MsolService
5. Теперь необходимо создать новый субъект-службу для приложения. Введите следующую команду и нажмите клавишу ВВОД:

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
На этом шаге выводится информация следующего вида:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Web Site
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [WACOM.NOTE] 
> Следует сохранить эти выходные данные, особенно сгенерированный симметричный ключ. Этот ключ отображается только во время создания субъекта-службы, в дальнейшем вы его получить не сможете. Другие значения необходимы, чтобы с помощью Graph API считывать данные из каталога и записывать данные в каталог.

6. На последнем шаге задается URL-адрес ответа для вашего приложения. На URL-адрес ответа отправляются ответы после попыток проверки подлинности. Введите следующие команды и нажмите клавишу ВВОД:

		$replyUrl = New-MsolServicePrincipalAddresses â€“Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal â€“AppPrincipalId "7829c758-2bef-43df-a685-717089474505" â€“Addresses $replyUrl 
	
Теперь веб-приложение в каталоге подготовлено, и сотрудники компании могут его использовать для единого входа.

##<a name="protectapp"></a>Шаг 3. Защита приложения с использованием WS-Federation для входа сотрудников
На этом шаге показано, как добавить поддержку федеративного входа с помощью Windows Identity Foundation (WIF) и библиотек simpleSAML.php, загруженных вами вместе с примером кода на этапе предварительных требований. Вы также научитесь добавлять страницу входа и настраивать отношения доверия между приложением и клиентом каталога.

1. В Eclipse щелкните правой кнопкой мыши проект **phpSample**, выберите **Создать**, затем щелкните **Файл**. 

2. В диалоговом окне **Новый файл** присвойте файлу имя **federation.ini**, затем нажмите кнопку **Готово**.

3. В новом файле **federation.ini** введите приведенную ниже информацию, указывая значения, сохраненные на шаге 2 при создании субъекта-службы.

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 

		<div class="dev-callout"><strong>Note</strong><p>The <b>audienceuris</b> and <b>realm</b> values must be prefaced by "spn:".</p></div>

4. В Eclipse щелкните правой кнопкой мыши проект **phpSample**, выберите **Создать**, затем щелкните **PHP-файл**. 

5. В диалоговом окне **Новый PHP-файл** присвойте файлу имя **secureResource.php**, затем нажмите кнопку **Готово**.

6. В новом файле **secureResource.php** введите приведенный ниже код, указав вместо пути **c:\phpLibraries** корневую папку, в которую вы загрузили пример кода. Корневая папка должна содержать файл **simpleSAML.php** и папку **federation**.

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. Откройте страницу **index.php** и обновите ее содержимое, чтобы защитить страницу, а затем сохраните ее.

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. В меню **Запуск** щелкните **Запуск**. Вы должны автоматически перейти на страницу поставщика удостоверений Office 365, на которой можете выполнить вход, используя ваши учетные данные клиента каталога. Например, *john.doe@fabrikam.onmicrosoft.com*.

##<a name="summary"></a>Сводка
В этом учебнике показано, как создать и настроить приложение PHP для одного клиента, использующее возможности единого входа Azure Active Directory.

Пример, показывающий, как использовать Azure Active Directory и единый вход для веб-сайтов PHP, можно найти на веб-странице <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.


[Шаг 1. Создание приложения PHP]: #createapp
[Шаг 2. Подготовка приложения в клиенте каталога компании]: #provisionapp
[Шаг 3. Защита приложения с использованием WS-Federation для входа сотрудников]: #protectapp
[Сводка]: #summary
[Введение]: #introduction
[Разработка многопользовательских облачных приложений с помощью Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Пакет SDK для Windows Identity Foundation 3.5]: http://www.microsoft.com/ru-ru/download/details.aspx?id=4451
[Среда выполнения Windows Identity Foundation 1.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=17331
[Командлеты Powershell для Office 365]: http://onlinehelp.microsoft.com/ru-ru/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/ru-ru/download/details.aspx?id=4211
[Пакеты Eclipse PDT 3.0.x All In One]: http://www.eclipse.org/pdt/downloads/
[Пример PHP-кода для Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP


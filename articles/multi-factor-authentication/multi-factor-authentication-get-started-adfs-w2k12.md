<properties 
	pageTitle="Защита облачных и локальных ресурсов с помощью сервера Azure MFA и сервера Windows Server 2012 R2 AD FS" 
	description="Эта страница посвящена Azure Multi-Factor Authentication. Она содержит сведения по началу работы с Azure MFA и AD FS на сервере Windows Server 2012 R2." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>


# Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и сервера Windows Server 2012 R2 AD FS

Если в организации настроена федерация с Azure Active Directory и существуют локальные или облачные ресурсы, которые нужно защитить, вы можете это сделать с помощью сервера Azure Multi-Factor Authentication. Его нужно настроить для работы с AD FS, чтобы многофакторная проверка подлинности запускалась для конечных точек с самым большим значением.

Эта статья содержит информацию об использовании сервера Azure Multi-Factor Authentication вместе с AD FS на сервере Windows Server 2012 R2. Сведения об использовании Azure Multi-Factor Authentication вместе с AD FS 2.0 см. в статье [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Защита Windows Server 2012 R2 AD FS с помощью сервера Azure Multi-Factor Authentication

Для установки сервера Azure Multi-Factor Authentication вы можете выбрать один из двух вариантов.

- Установка сервера Azure Multi-Factor Authentication локально, на том же сервере, на котором находятся службы AD FS. 
- Установка адаптера Azure Multi-Factor Authentication локально на сервере AD FS и установка сервера MFA на другом компьютере.

Прежде чем начать, ознакомьтесь с приведенной ниже информацией.

- Установка сервера Azure Multi-Factor Authentication на сервере федерации AD FS не является необходимой. Однако установка адаптера Azure Multi-Factor Authentication для AD FS на сервере Windows Server 2012 R2, на котором запускаются службы AD FS, является обязательной. Вы можете установить сервер на другом компьютере, если он находится по управлением ОС поддерживаемой версии, и установить адаптер AD FS отдельно на вашем сервере федерации AD FS. Сведения о том, как устанавливать адаптер отдельно, см. ниже.

- У учетной записи с выполненным входом должны быть определенные права, чтобы с ее помощью можно было создавать группы безопасности в Active Directory.

- Мастер установки адаптера Multi-Factor Authentication AD FS создает группу безопасности под названием PhoneFactor Admins в Active Directory и затем добавляет учетную запись службы AD FS в эту группу. Мы рекомендуем проверить на контроллере домена, что группа PhoneFactor Admins действительно создана и что учетная запись службы AD FS входит в эту группу. При необходимости добавьте учетную запись службы AD FS в группу PhoneFactor Admins в контроллере домена вручную.
  

### Установка сервера Azure Multi-Factor Authentication локально на том же сервере, на котором находятся службы AD FS

<ol>
<li>Загрузите и установите сервер Azure Multi-Factor Authentication на сервере федерации AD FS. Дополнительные сведения об установке сервера Azure Multi-Factor Authentication см. в статье [Приступая к работе с сервером Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md)</li>
<li>В пользовательском интерфейсе сервера Azure Multi-Factor Authentication щелкните значок AD FS и настройте параметры «Разрешить регистрацию пользователя» и «Разрешить пользователям выбирать метод».</li>
<li>Настройте дополнительные параметры.</li>
<li>Нажмите кнопку «Установить адаптер AD FS».</li>

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>

<li>Если компьютер присоединен к домену, но в Active Directory не закончена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится запрос на настройку Active Directory. Нажмите кнопку «Далее», чтобы автоматически завершить настройку, или установите флажок «Пропустить автоматическую настройку Active Directory и настроить параметры вручную» и нажмите кнопку «Далее».</li>
<li>Если компьютер присоединен к домену, но в локальной группе не закончена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится запрос на настройку локальной группы. Нажмите кнопку «Далее», чтобы автоматически завершить настройку, или установите флажок «Пропустить автоматическую настройку локальной группы и настроить параметры вручную» и нажмите кнопку «Далее».</li>
<li>Откроется мастер установки. Нажмите кнопку «Далее», чтобы разрешить серверу Azure Multi-Factor Authentication создать группу PhoneFactor Admins и добавьте в эту группу учетную запись службы AD FS.</li>

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>

<li>Когда запускается установщик, нажмите кнопку «Далее».</li>
<li>В установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку «Далее».</li>
<li>По завершении установки нажмите кнопку «Закрыть».</li>
<li>После установки адаптер нужно зарегистрировать в службах AD FS. Откройте Windows PowerShell и запустите команду C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1</li>

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>

<li>Теперь, чтобы использовать зарегистрированный адаптер, нужно изменить глобальную политику проверки подлинности в AD FS. В консоли управления AD FS перейдите к узлу политик проверки подлинности и в разделе «Многофакторная проверка подлинности» щелкните ссылку «Изменить» возле подраздела «Глобальные параметры». В окне «Изменение глобальной политики проверки подлинности» выберите Multi-Factor Authentication в качестве дополнительного метода проверки подлинности и нажмите кнопку «ОК». Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS.</li>


<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

На этом этапе сервер Multi-Factor Authentication уже настроен в качестве дополнительного поставщика проверки подлинности, который можно использовать с AD FS.

## Установка изолированного адаптера AD FS с помощью пакета SDK веб-службы
<ol>
<li>Установите пакет SDK веб-службы на сервере, на котором работает сервер Multi-Factor Authentication.</li>
<li>Скопируйте файлы MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 и MultiFactorAuthenticationAdfsAdapter.config из каталога \Program Files\Multi-Factor Authentication Server на сервер, на который вы планируете установить адаптер AD FS.</li>
<li>Запустите файл MultiFactorAuthenticationAdfsAdapterSetup64.msi.</li>
<li>Чтобы выполнить установку, в установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку «Далее».</li>
<li>По завершении установки нажмите кнопку «Закрыть».</li>
<li>Отредактируйте файл MultiFactorAuthenticationAdfsAdapter.config согласно инструкциям, приведенным ниже.</li>

Этап MultiFactorAuthenticationAdfsAdapter.config| Подэтап
:------------- | :------------- |
Задайте для узла UseWebServiceSdk значение true.||
В поле WebServiceSdkUrl укажите URL-адрес пакета SDK веб-службы Multi-Factor Authentication.||
Вариант 1: настройка пакета SDK веб-службы с помощью имени пользователя и пароля.|<ol><li>Задайте WebServiceSdkUsername для учетной записи, которая является участником группы безопасности PhoneFactor Admins. Используйте формат <domain><username>.<li>Задайте WebServiceSdkPassword в качестве подходящего пароля учетной записи.</li></ol>
Вариант 2: настройка пакета SDK веб-службы с помощью сертификата клиента.|<ol><li>Получите сертификат клиента из центра сертификации для сервера, на котором запущен пакет SDK веб-службы.</li><li>Импортируйте сертификат клиента в хранилище личного сертификата на локальном компьютере (на сервере, на котором запущен пакет SDK веб-службы). Примечание. Убедитесь, что общий сертификат центра сертификации входит в число доверенных корневых сертификатов.</li><li>Экспортируйте закрытые и открытые ключи сертификата клиента в PFX-файл.</li><li>Экспортируйте открытый ключ в кодировке Base64 в CER-файл.</li><li>В диспетчере серверов проверьте, установлен ли такой компонент, как веб-сервер (IIS)\\веб-сервер\\компонент безопасности\\проверка подлинности с сопоставлением сертификатов клиентов.</li><li>Если нужный компонент не установлен, чтобы его добавить, выберите элемент «Добавить роли и компоненты».</li><li>В диспетчере IIS дважды щелкните редактор конфигураций на веб-сайте, который содержит виртуальный каталог пакета SDK веб-службы. Примечание. Очень важно делать это на уровне веб-сайта, а не на уровне виртуального каталога.</li><li>Перейдите к разделу system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Для параметра «Включено» укажите значение true.</li><li>Для параметра oneToOneCertificateMappingsEnabled укажите значение true.</li><li>Нажмите кнопку «...» возле элемента oneToOneMappings.</li><li>Нажмите кнопку «Добавить ссылку».</li><li>Откройте CER-файл, экспортированный ранее, в кодировке Base64. Удалите -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- и любые разрывы строк. Скопируйте получившуюся строку.</li><li>Назначьте сертификат для строки, скопированной при выполнении предыдущего пункта.</li><li>Для параметра «Включено» укажите значение true.</li><li>Задайте userName для учетной записи, которая входит в группу безопасности PhoneFactor Admins. Используйте формат <domain><username>.</li><li>Задайте подходящий пароль для учетной записи</li><li>Закройте редактор коллекции.</li><li>Щелкните ссылку «Применить».</li><li>Перейдите к виртуальному каталогу пакета SDK веб-службы.</li><li>Выполните проверку подлинности двойным щелчком.</li><li>Убедитесь, что олицетворение ASP.NET и обычная проверка подлинности включены, а все другие элементы отключены.</li><li>Еще раз перейдите к виртуальному каталогу пакета SDK веб-службы.</li><li>Дважды щелкните элемент «Параметры SSL».</li><li>Для параметра «Сертификаты клиента» задайте значение «Принять» и нажмите кнопку «Применить».</li><li>Скопируйте PFX-файл, экспортированный ранее, на сервер, на котором запущен адаптер AD FS.</li><li>Импортируйте PFX-файл на локальный компьютер.</li><li>В контекстном меню выберите пункт «Управление закрытыми ключами» и предоставьте доступ на чтение учетной записи, от имени которой работают службы федерации Active Directory.</li><li>Откройте сертификат клиента и скопируйте отпечаток из вкладки «Подробности».</li><li>В файле MultiFactorAuthenticationAdfsAdapter.config задайте WebServiceSdkCertificateThumbprint для строки, скопированной при выполнении предыдущего пункта.</li></ol>
Измените сценарий Register-MultiFactorAuthenticationAdfsAdapter.ps1: добавьте -ConfigurationFilePath <path> в конец команды Register-AdfsAuthenticationProvider, где <path> — это полный путь к файлу MultiFactorAuthenticationAdfsAdapter.config.|

<li>Чтобы зарегистрировать адаптер, откройте в PowerShell сценарий \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1. Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS. </li>

<!---HONumber=July15_HO2-->
<properties
	pageTitle="Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication в Windows Server 2012 R2 AD FS | Microsoft Azure"
	description="В этой статье объясняется, как начать работу с Azure Multi-Factor Authentication и AD FS в Windows Server 2012 R2."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>


# Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication с AD FS в Windows Server 2012 R2

Если ваша организация использует службы федерации Active Directory (AD FS) и вы хотите защитить облачные или локальные ресурсы, вы можете развернуть и настроить сервер Azure Multi-Factor Authentication для работы с AD FS. Эта конфигурация активирует многофакторную проверку подлинности для важных конечных точек.

Эта статья содержит информацию об использовании сервера Azure Multi-Factor Authentication вместе с AD FS на сервере Windows Server 2012 R2. Дополнительную информацию см. в статье [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Защита AD FS в Windows Server 2012 R2 с помощью сервера Azure Multi-Factor Authentication

Для установки сервера Azure Multi-Factor Authentication вы можете выбрать один из следующих вариантов.

- Установка сервера Azure Multi-Factor Authentication локально на том же сервере, на котором находятся службы AD FS.
- Установка адаптера Azure Multi-Factor Authentication локально на сервере AD FS и установка сервера Multi-Factor Authentication на другом компьютере.

Прежде чем начать, ознакомьтесь с приведенной ниже информацией.

- Не обязательно устанавливать сервер Azure Multi-Factor Authentication на сервере AD FS. Но необходимо установить адаптер Multi-Factor Authentication для AD FS на сервере Windows Server 2012 R2, на котором работают службы федерации AD FS. Вы можете установить сервер на другом компьютере, если он работает под управлением ОС поддерживаемой версии, и установить адаптер AD FS отдельно на сервере федерации AD FS. Чтобы узнать, как установить адаптер отдельно, см. следующие процедуры.
- При проектировании адаптера AD FS сервера Multi-Factor Authentication предполагалось, что службы AD FS могут передавать имя проверяющей стороны адаптеру, который может использоваться как имя приложения. Но получилось не так, как ожидалось. Если ваша организация использует проверку подлинности с помощью текстового сообщения или мобильного приложения, строки, определенные в разделе "Параметры компании", содержат заполнитель <$*application\_name*$> (имя приложения). Этот заполнитель не заменяется автоматически при использовании адаптера AD FS. Рекомендуем удалить заполнитель из соответствующих строк, если вы защищаете AD FS.

- Учетная запись, которую вы используете для входа, должна иметь права на создание групп безопасности в службе Active Directory.

- Мастер установки адаптера AD FS Multi-Factor Authentication создает группу безопасности с именем PhoneFactor Admins в вашем экземпляре Active Directory, а затем добавляет учетную запись службы AD FS в эту группу. Рекомендуем убедиться с помощью контроллера домена, что группа PhoneFactor Admins действительно создана и учетная запись службы AD FS является участником этой группы. При необходимости добавьте вручную учетную запись службы AD FS в группу PhoneFactor Admins в контроллере домена.
- Сведения об установке пакета SDK веб-службы с помощью пользовательского портала см. в статье [Развертывание пользовательского портала для сервера Azure Multi-Factor Authentication](multi-factor-authentication-get-started-portal.md).


### Установка сервера Azure Multi-Factor Authentication локально на сервере, на котором находятся службы AD FS

1. Скачайте и установите сервер Azure Multi-Factor Authentication на сервере федерации AD FS. Сведения об установке см. в статье [Приступая к работе с сервером Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. В консоли управления сервером Azure Multi-Factor Authentication щелкните значок **AD FS**, а затем выберите параметры **Разрешить регистрацию пользователей** и **Разрешить пользователям выбирать метод**.
3. Выберите дополнительные параметры, которые вы хотите указать для своей организации.
4. Щелкните **Установить AD FS-адаптер**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Если компьютер присоединен к домену, но в Active Directory не завершена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится окно **Active Directory**. Нажмите кнопку **Далее**, чтобы автоматически завершить настройку, или установите флажок **Пропустить автоматическую настройку Active Directory и настроить параметры вручную** и нажмите кнопку **Далее**.
6. Если компьютер не присоединен к домену и в локальной группе не завершена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится окно **Локальная группа**. Нажмите кнопку **Далее**, чтобы автоматически завершить настройку, или установите флажок **Пропустить автоматическую настройку локальной группы и настроить параметры вручную** и нажмите кнопку **Далее**.
7. В мастере установки нажмите кнопку **Далее**. Сервер Azure Multi-Factor Authentication создаст группу PhoneFactor Admins и добавит в эту группу учетную запись службы AD FS.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. На странице **Запуск установщика** нажмите кнопку **Далее**.
9. В установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку **Далее**.
10. По завершении установки нажмите кнопку **Закрыть**.
11. После установки адаптера необходимо зарегистрировать его в AD FS. Откройте Windows PowerShell и выполните следующую команду:<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Чтобы использовать только что зарегистрированный адаптер, измените глобальную политику проверки подлинности в AD FS. В консоли управления AD FS откройте узел **Authentication Policies** (Политики проверки подлинности). В разделе **Multi-Factor Authentication** щелкните ссылку **Изменить** рядом с разделом **Глобальные параметры**. В окне **Edit Global Authentication Policy** (Изменение глобальной политики проверки подлинности) выберите **Multi-Factor Authentication** в качестве дополнительного метода проверки подлинности и нажмите кнопку **ОК**. Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

На этом этапе сервер Multi-Factor Authentication уже настроен в качестве дополнительного поставщика проверки подлинности, который можно использовать с AD FS.

## Установка изолированного экземпляра адаптера AD FS с помощью пакета SDK веб-службы
1. Установите пакет SDK веб-службы на сервере, на котором запущен сервер Multi-Factor Authentication.
2. Скопируйте следующие файлы из папки \\Program Files\\Multi-Factor Authentication Server на сервер, на котором вы планируете установить адаптер AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Запустите файл установки MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Чтобы выполнить установку, в установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку **Далее**.
5. По завершении установки нажмите кнопку **Закрыть**.
6. Отредактируйте файл MultiFactorAuthenticationAdfsAdapter.config согласно инструкциям, приведенным ниже.

|Этап MultiFactorAuthenticationAdfsAdapter.config| Подэтап|
|:------------- | :------------- |
|Задайте для узла **UseWebServiceSdk** значение **true**.||
|В поле **WebServiceSdkUrl** укажите URL-адрес пакета SDK веб-службы Multi-Factor Authentication.</br></br>Пример: **https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**</br></br>В этом примере certificatename — имя сертификата. ||
|Настройка пакета SDK веб-службы.<br><br>*Вариант 1*. С помощью имени пользователя и пароля|<ol type="a"><li>Для параметра **WebServiceSdkUsername** укажите учетную запись, которая является участником группы безопасности PhoneFactor Admins. Используйте формат &lt;домен&gt;&#92;&lt;имя пользователя&gt;.<li>Для параметра **WebServiceSdkPassword** укажите пароль соответствующей учетной записи.</li></ol>
|Настройка пакета SDK веб-службы *(продолжение)*.<br><br>*Вариант 2*. С помощью сертификата клиента|<ol type="a"><li>Получите в центре сертификации сертификат клиента для сервера, на котором запущен пакет SDK веб-службы. Дополнительные сведения см. в статье о [получении сертификатов клиента](https://technet.microsoft.com/library/cc770328.aspx).</li><li>Импортируйте сертификат клиента в хранилище личных сертификатов локального компьютера на сервере, на котором запущен пакет SDK веб-службы. Примечание. Убедитесь, что открытый сертификат центра сертификации находится в хранилище доверенных корневых сертификатов.</li><li>Экспортируйте закрытые и открытые ключи сертификата клиента в PFX-файл.</li><li>Экспортируйте открытый ключ в кодировке Base64 в CER-файл.</li><li>В диспетчере серверов проверьте, установлен ли компонент "Веб-сервер (IIS)\\Веб-сервер\\Безопасность\\Проверка подлинности с сопоставлением сертификата клиента IIS". Если этот компонент не установлен, добавьте его, выбрав **Добавить роли и компоненты**.</li><li>В диспетчере IIS дважды щелкните **Редактор конфигураций** на веб-сайте, который содержит виртуальный каталог пакета SDK веб-службы. Примечание. Очень важно делать это на уровне веб-сайта, а не на уровне виртуального каталога.</li><li>Перейдите к разделу **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.</li><li>Для параметра **Включено** укажите значение **true**.</li><li>Для параметра **oneToOneCertificateMappingsEnabled** укажите значение **true**.</li><li>Нажмите кнопку **…** возле элемента **oneToOneMappings**, а затем щелкните ссылку **Добавить**.</li><li>Откройте CER-файл в кодировке Base64, экспортированный ранее. Удалите *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* и все разрывы строк. Скопируйте получившуюся строку.</li><li>Задайте для **сертификата** строку, скопированную при выполнении предыдущего шага.</li><li>Для параметра **Включено** укажите значение **true**.</li><li>В параметре **userName** задайте имя учетной записи, которая входит в группу безопасности PhoneFactor Admins. Используйте формат &lt;домен&gt;&#92;&lt;имя пользователя&gt;.</li><li>Задайте пароль для соответствующей учетной записи и закройте редактор конфигураций.</li><li>Щелкните ссылку **Применить**.</li><li>Перейдите к виртуальному каталогу пакета SDK веб-службы. Дважды щелкните **Проверка подлинности**. </li><li>Убедитесь, что параметры **Олицетворение ASP.NET** и **Обычная проверка подлинности** **включены**, а остальные элементы **отключены**.</li><li> В виртуальном каталоге пакета SDK веб-службы дважды щелкните элемент **Параметры SSL**.</li><li>Для параметра **Сертификаты клиента** выберите значение **Принять** и нажмите кнопку **Применить**.</li><li>Скопируйте PFX-файл, экспортированный ранее, на сервер, на котором запущен адаптер AD FS.</li><li>Импортируйте PFX-файл в хранилище личных сертификатов на локальном компьютере.</li><li>Щелкните правой кнопкой мыши и выберите пункт **Управление закрытыми ключами**, а затем предоставьте доступ на чтение учетной записи, которую вы использовали для входа в службу AD FS.</li><li>Откройте сертификат клиента и скопируйте отпечаток на вкладке **Подробности**.</li><li>В файле MultiFactorAuthenticationAdfsAdapter.config укажите для параметра **WebServiceSdkCertificateThumbprint** строку, скопированную при выполнении предыдущего шага.</li></ol>
| Измените скрипт Register-MultiFactorAuthenticationAdfsAdapter.ps1. Для этого добавьте *-ConfigurationFilePath &lt;путь&gt;* в конец команды `Register-AdfsAuthenticationProvider`, где *&lt;путь&gt;* — это полный путь к файлу MultiFactorAuthenticationAdfsAdapter.config.||

Чтобы зарегистрировать адаптер, выполните в PowerShell сценарий \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1. Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS.

<!---HONumber=AcomDC_0921_2016-->
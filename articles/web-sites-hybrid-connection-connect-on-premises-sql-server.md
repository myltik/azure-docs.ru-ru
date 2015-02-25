<properties 
	pageTitle="Пошаговое создание гибридного подключения: подключение к локальному SQL Server с веб-сайта Azure" 
	description="Создание веб-сайта в Microsoft Azure и его подключение к локальной базе данных SQL Server" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений

##Введение
Гибридные подключения могут подключать веб-сайты Microsoft Azure к локальным ресурсам, которые используют TCP-порт. В поддерживаемые ресурсы входят Microsoft SQL Server, MySQL, веб-интерфейсы API HTTP, мобильные службы и самые настраиваемые веб-службы. 

В этом учебнике вы узнаете, как создать веб-сайт на портале Azure (предварительная версия), подключить веб-сайт к локальной базе данных SQL Server с помощью новой функции гибридного подключения, создать простое веб-приложение ASP.NET, которое будет использовать гибридное подключение, и развернуть приложение на веб-сайте Azure. В полном веб-сайте Azure учетные данные пользователя хранятся в локальной базе данных участников. Этот учебник разработан для читателей, не имеющих опыта использования Azure или ASP.NET.

> [AZURE.NOTE] Часть функции "Веб-сайты" гибридных подключений доступна только на [портале предварительной версии Azure](https://portal.azure.com). Информацию о создании подключения в службах BizTalk см. в разделе [Гибридные подключения](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Необходимые компоненты
Для работы с этим учебником вам потребуются следующие продукты. У всех этих продуктов есть бесплатные версии, так что можно начать разработку для Azure полностью бесплатно.

- **Подписка Azure** - бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/)

- **Visual Studio 2013** - о скачивании бесплатной пробной версии см. на странице [Загружаемые файлы для Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Прежде чем продолжить, установите этот продукт.

- **Microsoft .NET Framework 3.5 с пакетом обновления 1 (SP1)**. Если у вас операционная система Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 или Windows Server 2008 R2, ее можно включить на панели управления, выбрав "Программы и компоненты > Включение и выключение компонентов Windows". В противном случае программу можно скачать в [Центре скачивания Майкрософт](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014, экспресс-выпуск с инструментами** - скачайте экспресс-выпуск Microsoft SQL Server бесплатно на [странице базы данных веб-платформы Майкрософт](http://www.microsoft.com/web/platform/database.aspx). Выберите версию **Express** (не LocalDB). Версия **Express с инструментами** включает в себя SQL Server Management Studio, который будет использоваться в этом руководстве.

- **SQL Server Management Studio Express**. Сюда входит SQL Server 2014, экспресс-выпуск с инструментами, указанный выше, но если требуется установить его отдельно, вы можете скачать и установить его на [странице скачивания экспресс-выпуска SQL Server](http://www.microsoft.com/web/platform/database.aspx).

В учебнике предполагается, что у вас есть подписка Azure, установлена служба Visual Studio 2013 и установлена или включена .NET Framework 3.5. В учебнике показано, как установить SQL Server 2014 Express в конфигурации, которая хорошо работает с функцией гибридных подключений Azure (экземпляр по умолчанию со статическим TCP-портом). Перед тем как начать учебник, если у вас не установлен SQL Server, скачайте SQL Server 2014, экспресс-выпуск с инструментами, из указанного выше расположения.

### Примечания
Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Экземпляры SQL Server по умолчанию используют статический порт 1433, а именованные экземпляры не используют этот порт. 

Компьютер, на котором установлен локальный агент диспетчера гибридных подключений:

- Необходимо иметь исходящее подключение к Azure через:

> <table border="1">
    <tr>
       <th><strong>Порт</strong></th>
        <th>Назначение</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Требуется</strong> для порта HTTP при проверке сертификатов и (необязательно) для подключения к данным.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Необязательно</strong> для подключения к данным. Если исходящее подключение к порту 443 недоступно, то используется порт TCP 80.</td>
    </tr>
	<tr>
        <td>5671 и 9352</td>
        <td><strong>Рекомендуется</strong>, но необязательно для подключения к данным. Обратите внимание, что в этом режиме обеспечивается более высокая пропускная способность. Если исходящее подключение к этим портам недоступно, то используется порт TCP 443.</td>
	</tr>
</table>

- Должен подключаться к *hostname*:portnumber локального источника. 

В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.

Если SQL Server уже установлен в конфигурации и среде, которые соответствуют приведенным выше условиям, вы можете пропустить этот этап и перейти к разделу [Создание локальной базы данных SQL Server](#CreateSQLDB).

##В этой статье
[1. Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server](#InstallSQL)

[B. Создание веб-сайта на портале предварительной версии Azure](#CreateSite)

[3. Создание гибридного подключения и службы BizTalk](#CreateHC)

[4. Установка локального диспетчера гибридных подключений для завершения подключения](#InstallHCM)

[5. Создание базового веб-проекта ASP.NET, изменение строки подключения базы данных и локальный запуск проекта](#CreateASPNET)

[6. Публикация веб-приложения в Azure и его тестирование](#PubNTest)

<a name="InstallSQL"></a>
## 1. Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server

В этом разделе показывается, как установить SQL Server Express, включить TCP/IP и создать базу данных, чтобы веб-приложение работало со средой Azure (предварительная версия).

###Установка экспресс-выпуска SQL Server

1. Чтобы установить экспресс-выпуск SQL Server, запустите скачанный файл **SQLEXPRWT_x64_ENU.exe** или **SQLEXPR_x86_ENU.exe**. Появится мастер центра установки SQL Server.
	
	![SQL Server Install][SQLServerInstall]
	
2. Нажмите кнопку **Создать изолированную установку SQL Server или добавить компоненты к существующему экземпляру**. Следуйте указаниям, принимая варианты и параметры по умолчанию, пока не откроется страница **Конфигурация экземпляров**.
	
3. На странице **Конфигурация экземпляров** выберите **Экземпляр по умолчанию**.
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	Экземпляр SQL Server по умолчанию прослушивает запросы от клиентов SQL Server в статическом порте 1433 в соответствии с требованиями функции гибридных подключений. Именованные экземпляры используют динамические порты и UDP, которые не поддерживаются гибридными подключениями.
	
4. Примите значения по умолчанию на странице **Конфигурация сервера**.
	
5. На странице **Конфигурация модуля базы данных** в разделе **Режим проверки подлинности** выберите **Смешанный режим (проверка подлинности SQL Server и Windows)** и укажите пароль.
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	В этом учебнике вы будете использовать проверку подлинности SQL Server. Убедитесь, что запомнили указанный пароль, поскольку он понадобится вам позже.
	
6. Выполните все инструкции мастера, чтобы завершить установку.

###Включение TCP/IP
Чтобы включить TCP/IP, необходимо использовать диспетчер конфигурации SQL Server, который устанавливается при установке SQL Server Express. Перед тем как продолжить, выполните шаги в разделе [Включение протокола сети TCP/IP для SQL Server](http://technet.microsoft.com/ru-ru/library/hh231672%28v=sql.110%29.aspx).

<a name="CreateSQLDB"></a>
###Создание локальной базы данных SQL Server

Для веб-приложения Visual Studio требуется база данных участников, к которой может получить доступ Azure. Для этого необходим SQL Server или база данных SQL Server Express (а не база данных LocalDB, которую шаблон MVC использует по умолчанию), поэтому далее следует создать базу данных. 

1. В SQL Server Management Studio подключитесь к установленному SQL Server. (Если диалоговое окно **Подключение к серверу** не появляется автоматически, перейдите к **обозревателю объектов** на левой панели, щелкните **Подключение**, а затем щелкните **Ядро СУБД**.) 	
	![Connect to Server][SSMSConnectToServer]
	
	В качестве **типа сервера** выберите **Ядро СУБД**. В качестве **имени сервера** можно использовать **localhost** или имя используемого компьютера. Выберите **Проверка подлинности SQL Server**, а затем войдите с именем пользователя и паролем, созданным ранее. 
	
2. Чтобы создать новую базу данных, используя SQL Server Management Studio, щелкните правой кнопкой мыши **Базы данных** в обозревателе сервера, а затем нажмите кнопку **Создать базу данных**.
	
	![Create new database][SSMScreateNewDB]
	
3. В диалоговом окне **Создание базы данных** введите в качестве имени базы данных MembershipDB и нажмите кнопку **ОК**. 
	
	![Provide database name][SSMSprovideDBname]
	
	Обратите внимание, что в этот момент в базу данных не вносятся никакие изменения. Сведения об участниках веб-приложение добавит позже, автоматически при запуске.
	
4. Если развернуть в обозревателе объектов **Базы данных**, вы увидите, что была создана база данных участников.
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## 2. Создание веб-сайта на портале Azure (предварительная версия)

> [AZURE.NOTE] Если вы уже создали веб-сайт на портале предварительной версии Azure, который хотите использовать в этом учебнике, можно пропустить этот шаг и начать с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC) .

1. В левом нижнем углу портала [Azure (предварительная версия)](https://portal.azure.com) нажмите кнопку **Создать** и выберите **Веб-сайт**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. В колонке **Вебсайт** укажите имя веб-сайта и нажмите кнопку **Создать**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Через несколько мгновений будет создан веб-сайт и появится выноска веб-сайта. Выноска - это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять сайтом.
	
	![Website running][WebSiteRunningBlade]
	
4. Чтобы проверить, что веб-сайт работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
Далее вы создадите гибридное подключение и службу BizTalk для веб-сайта.

<a name="CreateHC"></a>
## 3. Создание гибридного подключения и службы BizTalk

1. На портале (предварительная версия) прокрутите вниз выноску для веб-сайта и выберите пункт **Гибридные подключения**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. На выноске "Гибридные подключения" нажмите кнопку **Добавить**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Откроется выноска **Добавление гибридного подключения**.  Поскольку это ваше первое гибридное подключение, параметр **Новое гибридное подключение** выбран автоматически, и откроется колонка **Создание гибридного подключения**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	На выноске **Создание гибридного подключения**:
	- в поле **Имя** укажите имя подключения;
	- в поле **Имя узла** введите имя компьютера главного компьютера SQL Server;
	- в поле **Порт** введите 1433 (порт по умолчанию для SQL Server).
	- Нажмите кнопку **Служба BizTalk**.


4. Откроется выноска **Создание службы BizTalk**. Введите имя службы BizTalk и нажмите кнопку **ОК**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Колонка **Создание службы BizTalk** закроется, и вы вернетесь к колонке **Создание гибридного подключения**.
	
5. На выноске "Создание гибридного подключения" нажмите кнопку **ОК**. 
	
	![Click OK][CreateBTScomplete]
	
6. По завершении процесса в области "Уведомления" на портале появится сообщение, что подключение успешно создано.
	
	![Success notification][CreateHCSuccessNotification]
	
7. В выноске веб-сайта значок **Гибридные подключения** теперь покажет, что создано 1 гибридное подключение.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>
## 4. Установка локального диспетчера гибридных подключений для завершения подключения

1. На выноске веб-сайта щелкните значок "Гибридные подключения". 
	
	![Hybrid connections icon][HCIcon]
	
2. В колонке **Гибридные подключения** в столбце **Состояние** для недавно добавленной конечной точки показано **Не подключено**. Щелкните подключение, чтобы настроить его.
	
	![Not connected][NotConnected]
	
	Откроется выноска гибридного подключения.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Щелкните в колонке элемент **Установка прослушивателя**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Откроется колонка **Свойства гибридного подключения**. Нажмите в **локальном диспетчере гибридных подключений** кнопку **Щелкните здесь, чтобы установить**.
	
	![Click here to install][ClickToInstallHCM]
	
5. В диалоговом окне предупреждения системы безопасности "Запуск приложения" нажмите кнопку **Запустить**, чтобы продолжить.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	В диалоговом окне **Контроль учетных записей пользователей** щелкните **Да**.
	
	![Choose Yes][UAC]
	
7. Диспетчер гибридных подключений скачан и установлен. 
	
	![Installing][HCMInstalling]
	
8. После завершения установки нажмите кнопку **Закрыть**.
	
	![Click Close][HCMInstallComplete]
	
	В колонке **Гибридные подключения** в столбце **Состояние** теперь отображается **Подключено**. 
	
	![Connected Status][HCStatusConnected]

Теперь по завершении инфраструктуры гибридных подключений создайте веб-приложение, которое использует ее.

<a name="CreateASPNET"></a>
## 5. Создание базового веб-проекта ASP.NET, изменение строки подключения базы данных и локальный запуск проекта

###Создание базового проекта ASP.NET
1. В Visual Studio в меню **Файл** создайте новый проект:
	
	![New Visual Studio project][HCVSNewProject]
	
2. В разделе **Шаблоны** диалогового окна **Новый проект** выберите **Веб-приложение** и нажмите **Веб-приложение ASP.NET**, а затем нажмите кнопку **ОК**.
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. В диалоговом окне **Новый проект ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.
	
	![Choose MVC][HCVSChooseMVC]
	
4. При создании проекта появится страница файла сведений приложения. Не запускайте пока что веб-проект.
	
	![Readme page][HCVSReadmePage]

###Изменение строки подключения базы данных для приложения

В этом шаге необходимо настроить строку подключения, которая сообщает приложению, где найти локальную базу данных SQL Server Express. Строка подключения находится в файле Web.config приложения, в котором содержатся сведения о конфигурации приложения. 

> [AZURE.NOTE] Чтобы убедиться, что приложение использует базу данных, созданную в экспресс-выпуске SQL Server, а не LocalDB Visual Studio по умолчанию, важно выполнить это действие до запуска проекта.

1. Дважды щелкните в обозревателе решений файл Web.config.
	
	![Web.config][HCVSChooseWebConfig]
	
2. Измените раздел **connectionStrings**, чтобы он указывал на базу данных SQL Server на локальном компьютере, используя синтаксис в следующем примере: 
	
	![Connection string][HCVSConnectionString]
	
	При создании строки подключения помните о следующем.
	
	- Если вы подключаетесь к именованному экземпляру, а не к экземпляру по умолчанию (например, YourServer\SQLEXPRESS), необходимо настроить SQL Server для использования статических портов. Информацию о настройке статических портов см. в разделе [Настройка SQL Server для прослушивания определенного порта](http://support.microsoft.com/kb/823938). По умолчанию именованные экземпляры используют UDP и динамические порты, которые не поддерживаются гибридными подключениями. 
	
	- Рекомендуется указать порт (1433 по умолчанию, как показано в примере) в строке подключения, чтобы убедиться, что в локальном SQL Server включен TCP, использующий правильный порт. 
	
	- Следует использовать проверку подлинности SQL Server для подключения, указав идентификатор и пароль пользователя в строке подключений.
	
3. Нажмите кнопку **Сохранить** в Visual Studio, чтобы сохранить файл Web.config.

###Запуск проекта локально и регистрация нового пользователя

1. Теперь запустите новый веб-проект локально, щелкнув кнопку "Обзор" в разделе "Отладка". В этом примере используется Internet Explorer.
	
	![Run project][HCVSRunProject]
	
2. В правом верхнем углу веб-страницы по умолчанию нажмите кнопку **Зарегистрировать**, чтобы зарегистрировать новую учетную запись:
	
	![Register a new account][HCVSRegisterLocally]
	
3. Введите имя пользователя и пароль:
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	Это автоматически создаст базу данных в локальном SQL Server, в котором хранятся сведения об участниках для вашего приложения. В одной из таблиц (**dbo.AspNetUsers**) сохранены учетные данные пользователя веб-сайта, аналогичные указанным только что. Эта таблица появится позже в данном учебном курсе.
	
4. Закройте окно браузера веб-страницы по умолчанию. Это остановит приложение в Visual Studio.

Теперь вы готовы к следующему шагу - публикации приложения в Azure и тестировании его.

<a name="PubNTest"></a>
## 6. Публикация веб-приложения в Azure и его тестирование

Далее вы опубликуете приложение на веб-сайте Azure и протестируете его, чтобы увидеть, как настроенное ранее гибридное подключение используется для подключения приложения веб-сайта к базе данных на локальном компьютере. 

###Публикация веб-приложения

1. Вы можете скачать профиль публикации для веб-сайта на портале Azure. Выберите на выноске веб-сайта **Скачать профиль публикации** и сохраните файл на компьютере.
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	![Publish profile in downloads folder][HCVSPublishProfileInDownloadsFolder]
	
	Далее вы импортируете этот файл в веб-приложение Visual Studio. 
	
2. В Visual Studio щелкните правой кнопкой мыши имя проекта в обозревателе решений и выберите **Опубликовать**.
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. В диалоговом окне **Публикация веб-сайта** нажмите на вкладке **Профиль** кнопку **Импорт**.
	
	![Import][HCVSPublishWebDialogImport]
	
4. Откройте скачанный профиль публикации, выберите его и нажмите кнопку **ОК**.
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. Информация о публикации импортируются и отображаются на вкладке **Подключение** диалогового окна. 
	
	![Click Publish][HCVSClickPublish]
	
	Щелкните **Опубликовать**.
	
	По завершении публикации браузер запустит и покажет знакомый веб-сайт, который теперь еще и работает в облаке Azure!

Далее вы используете веб-приложение, чтобы увидеть гибридные подключения в действии.

###Тестирование полного веб-приложения в Azure
1. В правом верхнем углу веб-страницы Azure нажмите кнопку **Войти**.
	
	![Test log in][HCTestLogIn]
	
2. Веб-сайт Azure теперь подключен к базе данных участников веб-приложения на локальном компьютере. Чтобы проверить это, войдите с помощью тех же учетных данных, которые указали в локальной базе данных раньше.
	
	![Hello greeting][HCTestHelloContoso]
	
3. Для дальнейшего тестирования гибридного подключения выйдите из веб-приложения Azure и зарегистрируйте другого пользователя. Укажите новое имя пользователя и пароль, а затем нажмите кнопку **Зарегистрировать**.
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. Чтобы убедиться, что учетные данные нового пользователя сохранены в локальной базе данных с помощью гибридного подключения, откройте SQL Management Studio на локальном компьютере. Разверните в обозревателе объектов базу данных **MembershipDB**, а затем разверните **Таблицы**. Щелкните правой кнопкой мыши таблицу участников **dbo.AspNetUsers** и выберите пункт **Выбрать топ-1000 строк**, чтобы просмотреть результаты.
	
	![View the results][HCTestSSMSTree]
	
5. В локальной таблице участников теперь отображаются обе учетные записи: созданная локально и созданная в облаке Azure. Учетная запись, созданная в облаке, сохранена в локальной базе данных с помощью функции гибридного подключения Azure.
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
Теперь вы создали и развернули веб-приложение ASP.NET, которое использует гибридное подключение между веб-сайтом в облаке Azure и локальной базой данных SQL Server. Поздравляем!

##См. также
[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Веб-сайт гибридных подключений](http://azure.microsoft.com/ru-ru/services/biztalk-services/)

[Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"](http://azure.microsoft.com/ru-ru/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Информацию об удостоверениях ASP.NET](http://www.asp.net/identity)

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png


<!--HONumber=42-->

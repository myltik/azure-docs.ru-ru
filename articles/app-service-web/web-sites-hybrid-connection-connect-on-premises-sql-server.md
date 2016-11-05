---
title: Подключение к локальному SQL Server из веб-приложения, размещенного в службе приложений Azure с помощью гибридных подключений
description: Создание веб-приложения в Microsoft Azure и его подключение к локальной базе данных SQL Server
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: mollybos

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: cephalin

---
# Подключение к локальному SQL Server из веб-приложения, размещенного в службе приложений Azure с помощью гибридных подключений
Гибридные подключения могут подключать [службу приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) к локальным ресурсам, которые используют статический TCP-порт. В поддерживаемые ресурсы входят Microsoft SQL Server, MySQL, веб-интерфейсы API HTTP, мобильные службы и самые настраиваемые веб-службы.

В этом учебнике вы узнаете, как создать веб-приложение службы приложений [на портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715), подключить веб-приложение к локальной базе данных SQL Server с помощью новой функции гибридного подключения, создать простое приложение ASP.NET, которое будет использовать гибридное подключение, и развернуть приложение в веб-приложение службы приложений Azure. В полном веб-приложении Azure учетные данные пользователя хранятся в локальной базе данных участников. Этот учебник разработан для читателей, не имеющих опыта использования Azure или ASP.NET.

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> Часть функции "Веб-приложения" гибридных подключений доступна только на [портале Azure](https://portal.azure.com). Сведения о создании подключения в службах BizTalk см. в разделе [Гибридные подключения](http://go.microsoft.com/fwlink/p/?LinkID=397274).
> 
> 

## Предварительные требования
Для работы с этим учебником вам потребуются следующие продукты. У всех этих продуктов есть бесплатные версии, так что можно начать разработку для Azure полностью бесплатно.

* **Подписка Azure**. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](/pricing/free-trial/).
* **Visual Studio 2013** — о загрузке бесплатной пробной версии см. на странице [Загружаемые файлы для Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Прежде чем продолжить, установите этот продукт.
* **Microsoft .NET Framework 3.5 с пакетом обновления 1 (SP1)**. Если у вас операционная система Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 или Windows Server 2008 R2, ее можно включить на панели управления > Программы и компоненты > Включение и выключение компонентов Windows. В противном случае программу можно скачать в [Центре скачивания Майкрософт](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).
* **SQL Server 2014 Express с инструментами** — загрузите Microsoft SQL Server Express бесплатно на [странице базы данных веб-платформы Майкрософт](http://www.microsoft.com/web/platform/database.aspx). Выберите версию **Express** (не LocalDB). Версия **Express с инструментами** включает SQL Server Management Studio, который будет использоваться в этом руководстве.
* **SQL Server Management Studio Express**. Сюда включается SQL Server 2014, экспресс-выпуск с инструментами, указанный выше, но если требуется установить его отдельно, можно скачать и установить его на [странице скачивания SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

В учебнике предполагается, что у вас есть подписка Azure, установлена служба Visual Studio 2013 и установлена или включена .NET Framework 3.5. В учебнике показано, как установить SQL Server 2014 Express в конфигурации, которая хорошо работает с функцией гибридных подключений Azure (экземпляр по умолчанию со статическим TCP-портом). Перед тем как начать учебник, если у вас не установлен SQL Server, скачайте SQL Server 2014, экспресс-выпуск с инструментами, из указанного выше расположения.

### Примечания
Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Экземпляры SQL Server по умолчанию используют статический порт 1433, а именованные экземпляры не используют этот порт.

Компьютер, на котором установлен локальный агент диспетчера гибридных подключений:

* Необходимо иметь исходящее подключение к Azure через:

| Порт | Назначение |
| --- | --- |
| 80 |**Требуется** для порта HTTP при проверке сертификатов и (необязательно) для подключения к данным. |
| 443 |**Необязательно** для подключения к данным. Если исходящее подключение к порту 443 недоступно, то используется порт TCP 80. |
| 5671 и 9352 |**Рекомендуется**, но необязательно для подключения к данным. Обратите внимание, что в этом режиме обеспечивается более высокая пропускная способность. Если исходящее подключение к этим портам недоступно, то используется порт TCP 443. |

* должен подключаться к *hostname*:*portnumber* локального источника.

В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.

Если SQL Server уже установлен в конфигурации и среде, которые соответствуют приведенным выше условиям, можно пропустить этот этап и перейти к разделу [Создание локальной базы данных SQL Server](#CreateSQLDB).

<a name="InstallSQL"></a>

## О. Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server
В этом разделе показывается, как установить SQL Server Express, включить TCP/IP и создать базу данных, чтобы веб-приложение работало с порталом Azure.

### Установка SQL Server Express
1. Чтобы установить SQL Server Express, запустите скачанный файл **SQLEXPRWT\_x64\_ENU.exe** или **SQLEXPR\_x86\_ENU.exe**. Появится мастер центра установки SQL Server.
   
    ![Установка SQL Server][SQLServerInstall]
2. Нажмите кнопку **Создать изолированную установку SQL Server или добавить компоненты к существующему экземпляру**. Следуйте инструкциям, принимая варианты и параметры по умолчанию, пока не откроется страница **Конфигурация экземпляров**.
3. На странице **Конфигурация экземпляров** выберите **Экземпляр по умолчанию**.
   
    ![Выбор экземпляра по умолчанию][ChooseDefaultInstance]
   
    Экземпляр SQL Server по умолчанию прослушивает запросы от клиентов SQL Server в статическом порте 1433 в соответствии с требованиями функции гибридных подключений. Именованные экземпляры используют динамические порты и UDP, которые не поддерживаются гибридными подключениями.
4. Примите значения по умолчанию на странице **Конфигурация сервера**.
5. На странице **Конфигурация модуля базы данных** в разделе **Режим проверки подлинности** выберите **Смешанный режим (проверка подлинности SQL Server и проверка подлинности Windows)** и укажите пароль.
   
    ![Выбор смешанного режима][ChooseMixedMode]
   
    В этом учебнике вы будете использовать проверку подлинности SQL Server. Убедитесь, что запомнили указанный пароль, поскольку он понадобится вам позже.
6. Выполните все инструкции мастера, чтобы завершить установку.

### Включение TCP/IP
Чтобы включить TCP/IP, необходимо использовать диспетчер конфигурации SQL Server, который устанавливается при установке SQL Server Express. Перед тем как продолжить, выполните шаги в разделе [Включение протокола сети TCP/IP для SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx).

<a name="CreateSQLDB"></a>

### Создание локальной базы данных SQL Server
Для веб-приложения Visual Studio требуется база данных участников, к которой может получить доступ Azure. Для этого необходим SQL Server или база данных SQL Server Express (а не база данных LocalDB, которую шаблон MVC использует по умолчанию), поэтому далее следует создать базу данных.

1. В SQL Server Management Studio подключитесь к установленному SQL Server. (Если диалоговое окно **Подключение к серверу** не появляется автоматически, перейдите к **обозревателю объектов** на левой панели, щелкните **Подключение**, а затем щелкните **Модуль базы данных**.)
   
    ![Подключение к серверу][SSMSConnectToServer]
   
    В качестве **типа сервера** выберите **модуль базы данных**. В качестве **имени сервера** можно использовать **localhost** или имя используемого компьютера. Выберите **Проверка подлинности SQL Server**, а затем войдите с именем пользователя и паролем, созданным ранее.
2. Чтобы создать новую базу данных, используя SQL Server Management Studio, щелкните правой кнопкой мыши **Базы данных** в обозревателе сервера, а затем нажмите кнопку **Создать базу данных**.
   
    ![Создание базы данных][SSMScreateNewDB]
3. В диалоговом окне **Создание базы данных** введите в качестве имени базы данных MembershipDB и нажмите кнопку **ОК**.
   
    ![Предоставление имени базы данных][SSMSprovideDBname]
   
    Обратите внимание, что в этот момент в базу данных не вносятся никакие изменения. Сведения об участниках веб-приложение добавит позже, автоматически при запуске.
4. Если развернуть в обозревателе объектов **Базы данных**, вы увидите, что была создана база данных участников.
   
    ![Создана MembershipDB][SSMSMembershipDBCreated]

<a name="CreateSite"></a>

## B. Создание веб-приложения на портале Azure
> [!NOTE]
> Если вы уже создали веб-приложение на портале Azure, которое хотите использовать при прохождении этого учебника, можно пропустить этот шаг и продолжить с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC).
> 
> 

1. На [портале Azure](https://portal.azure.com) щелкните **Создать** > **Интернет + мобильные устройства** > **Веб-приложение**.
   
    ![Кнопка «Создать»][New]
2. Настройте веб-приложение и нажмите кнопку **Создать**.
   
    ![Имя веб-сайта][WebsiteCreationBlade]
3. Через несколько секунд будет создано веб-приложение и появится его колонка. Колонка — это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять веб-приложением.
   
    ![Запущенный веб-сайт][WebSiteRunningBlade]
   
    Чтобы проверить, что веб-приложение работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.

Далее вы создадите гибридное подключение и службу BizTalk для веб-приложения.

<a name="CreateHC"></a>

## C. Создание гибридного подключения и службы BizTalk
1. Вернитесь на портал, откройте настройки и выберите **Сети** > **Настроить конечные точки гибридного подключения**.
   
    ![Гибридные подключения][CreateHCHCIcon]
2. В колонке "Гибридные подключения" щелкните **Добавить** > **Создать гибридное подключение**.
3. В колонке **Создание гибридного подключения**:
   
   * в поле **Имя** укажите имя подключения;
   * в поле **Имя узла** введите имя компьютера главного компьютера SQL Server;
   * в поле **Порт** введите 1433 (порт по умолчанию для SQL Server).
   * Щелкните **Служба BizTalk** > **Создать службу BizTalk** и введите имя службы.
     
     ![Создание гибридного подключения][TwinCreateHCBlades]
4. Нажмите **ОК** дважды.
   
    По завершении процесса в области **Уведомления** будет мигать зеленым надпись **УСПЕШНО**, а в колонке **Гибридное подключение** будет отображено новое гибридное подключение с состоянием **Не подключено**.
   
    ![Создано одно гибридное подключение][CreateHCOneConnectionCreated]

На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>

## D. Установка локального диспетчера гибридных подключений для завершения подключения
[!INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Теперь по завершении инфраструктуры гибридных подключений создайте веб-приложение, которое использует ее.

<a name="CreateASPNET"></a>

## E. Создание базового веб-проекта ASP.NET, изменение строки подключения к базе данных и локальный запуск проекта
### Создание базового проекта ASP.NET
1. В Visual Studio в меню **Файл** создайте новый проект:
   
    ![Новый проект Visual Studio][HCVSNewProject]
2. В разделе **Шаблоны** диалогового окна **Новый проект** выберите **Веб-приложение** и нажмите **Веб-приложение ASP.NET**, а затем щелкните кнопку **ОК**.
   
    ![Выбор веб-приложения ASP.NET][HCVSChooseASPNET]
3. В диалоговом окне **Новый проект ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.
   
    ![Выбор MVC][HCVSChooseMVC]
4. При создании проекта появится страница файла сведений приложения. Не запускайте пока что веб-проект.
   
    ![Страница файла сведений][HCVSReadmePage]

### Изменение строки подключения базы данных для приложения
В этом шаге необходимо настроить строку подключения, которая сообщает приложению, где найти локальную базу данных SQL Server Express. Строка подключения находится в файле Web.config приложения, в котором содержатся сведения о конфигурации приложения.

> [!NOTE]
> Чтобы убедиться, что приложение использует базу данных, созданную в экспресс-выпуске SQL Server, а не LocalDB Visual Studio по умолчанию, важно выполнить это действие до запуска проекта.
> 
> 

1. Дважды щелкните в обозревателе решений файл Web.config.
   
    ![Web.config][HCVSChooseWebConfig]
2. Измените раздел **connectionStrings**, чтобы он указывал на базу данных SQL Server на локальном компьютере, используя синтаксис в следующем примере:
   
    ![Строка подключения][HCVSConnectionString]
   
    При создании строки подключения помните о следующем.
   
   * Если вы подключаетесь к именованному экземпляру, а не к экземпляру по умолчанию (например, YourServer\\SQLEXPRESS), необходимо настроить SQL Server для использования статических портов. Сведения о настройке статических портов см. в разделе [Настройка SQL Server для прослушивания определенного порта](http://support.microsoft.com/kb/823938). По умолчанию именованные экземпляры используют UDP и динамические порты, которые не поддерживаются гибридными подключениями.
   * Рекомендуется указать порт (1433 по умолчанию, как показано в примере) в строке подключения, чтобы убедиться, что в локальном SQL Server включен TCP, использующий правильный порт.
   * Следует использовать проверку подлинности SQL Server для подключения, указав идентификатор и пароль пользователя в строке подключений.
3. Щелкните кнопку **Сохранить** в Visual Studio, чтобы сохранить файл Web.config.

### Запуск проекта локально и регистрация нового пользователя
1. Теперь запустите новый веб-проект локально, щелкнув кнопку «Обзор» в разделе «Отладка». В этом примере используется Internet Explorer.
   
    ![Запуск проекта][HCVSRunProject]
2. В правом верхнем углу веб-страницы по умолчанию нажмите кнопку **Зарегистрировать**, чтобы зарегистрировать новую учетную запись:
   
    ![Регистрация новой учетной записи][HCVSRegisterLocally]
3. Введите имя пользователя и пароль:
   
    ![Ввод имени пользователя и пароля][HCVSCreateNewAccount]
   
    Это автоматически создаст базу данных в локальном SQL Server, в котором хранятся сведения об участниках для вашего приложения. В одной из таблиц (**dbo.AspNetUsers**) сохранены учетные данные пользователя веб-приложения, аналогичные только что указанным. Эта таблица появится позже в данном учебном курсе.
4. Закройте окно браузера веб-страницы по умолчанию. Это остановит приложение в Visual Studio.

Теперь вы готовы к следующему шагу — публикации приложения в Azure и тестировании его.

<a name="PubNTest"></a>

## F. Публикация веб-приложения в Azure и его тестирование
Далее вы опубликуете приложение в веб-приложение службы приложений и протестируете его, чтобы увидеть, как настроенное ранее гибридное подключение используется для подключения веб-приложения к базе данных на локальном компьютере.

### Публикация веб-приложения
1. Можно загрузить свой профиль публикации для данного веб-приложения службы приложений на портале Azure. В колонке веб-приложения нажмите **Загрузить профиль публикации** и сохраните файл на компьютере.
   
    ![Загрузить профиль публикации][PortalDownloadPublishProfile]
   
    Далее вы импортируете этот файл в веб-приложение Visual Studio.
2. В Visual Studio щелкните правой кнопкой мыши имя проекта в обозревателе решений и выберите **Опубликовать**.
   
    ![Выбор публикации][HCVSRightClickProjectSelectPublish]
3. В диалоговом окне **Публикация в Интернете** нажмите на вкладке **Профиль** кнопку **Импорт**.
   
    ![Импорт][HCVSPublishWebDialogImport]
4. Откройте скачанный профиль публикации, выберите его и щелкните кнопку **ОК**.
   
    ![Открытие профиля][HCVSBrowseToImportPubProfile]
5. Сведения о публикации импортируются и отображаются на вкладке **Подключение** диалогового окна.
   
    ![Нажмите кнопку "Опубликовать"][HCVSClickPublish]
   
    Щелкните **Опубликовать**.
   
    По завершении публикации браузер запустит и покажет знакомое приложение ASP.NET, которое теперь еще и работает в облаке Azure!

Далее вы используете веб-приложение, чтобы увидеть гибридные подключения в действии.

### Тестирование полного веб-приложения в Azure
1. В правом верхнем углу веб-страницы Azure нажмите кнопку **Войти**.
   
    ![Тестирование входа][HCTestLogIn]
2. Веб-приложение службы приложений теперь подключено к базе данных участников веб-приложения на локальном компьютере. Чтобы проверить это, войдите с помощью тех же учетных данных, которые указали в локальной базе данных раньше.
   
    ![Приветствие][HCTestHelloContoso]
3. Для дальнейшего тестирования гибридного подключения выйдите из веб-приложения Azure и зарегистрируйте другого пользователя. Укажите новое имя пользователя и пароль, а затем щелкните кнопку **Зарегистрировать**.
   
    ![Тестирование регистрации другого пользователя][HCTestRegisterRelecloud]
4. Чтобы убедиться, что учетные данные нового пользователя сохранены в локальной базе данных с помощью гибридного подключения, откройте SQL Management Studio на локальном компьютере. Разверните в обозревателе объектов базу данных **MembershipDB**, а затем разверните **Таблицы**. Щелкните правой кнопкой мыши таблицу участников **dbo.AspNetUsers** и выберите пункт **Выбрать топ-1000 строк**, чтобы просмотреть результаты.
   
    ![Просмотр результатов][HCTestSSMSTree]
5. В локальной таблице участников теперь отображаются обе учетные записи: созданная локально и созданная в облаке Azure. Учетная запись, созданная в облаке, сохранена в локальной базе данных с помощью функции гибридного подключения Azure.
   
    ![Зарегистрированные пользователи в локальной базе данных][HCTestShowMemberDb]

Теперь вы создали и развернули веб-приложение ASP.NET, которое использует гибридное подключение между веб-приложением в облаке Azure и локальной базой данных SQL Server. Поздравляем!

## См. также
[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Обзор гибридных подключений](/services/biztalk-services/)

[Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Общие сведения об удостоверениях ASP.NET](http://www.asp.net/identity)

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!-----HONumber=AcomDC_0504_2016-->
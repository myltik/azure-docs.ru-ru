<properties 
	pageTitle="Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure" 
	description="Создавайте подключения между веб-приложением в службе приложений Azure и локальным ресурсом, который использует статический TCP-порт." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure

Вы можете подключить веб-приложение службы приложений Azure к любому локальному ресурсу, который использует статический TCP-порт, например к SQL Server, MySQL, веб-интерфейсам API HTTP, мобильным службам и большинству настраиваемых веб-служб. В этой статье показано, как создать гибридное подключение между веб-приложением в службе приложений и локальной базой данных SQL Server.

> [AZURE.NOTE] Часть функции "Веб-приложения" гибридных подключений доступна только на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Информацию о создании подключения в службах BizTalk см. в разделе [Гибридные подключения](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## Предварительные требования
- Подписка Azure. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Мы рекомендуем использовать экземпляр SQL Server по умолчанию, так как он использует статический порт 1433. Информацию об установке и настройке экспресс-выпуска SQL Server для использования с гибридными подключениями см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979)

- Компьютер, на котором установлен локальный агент диспетчера гибридных подключений, описан далее в этой статье:

	- должен подключаться к Azure через порт 5671;
	- должен подключаться к *hostname*:*portnumber* вашего локального источника. 

> [AZURE.NOTE] В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.


## Создание веб-приложения на портале Azure ##

> [AZURE.NOTE] Если вы уже создали веб-приложение на портале Azure, которое хотите использовать в этом учебнике, можно пропустить этот шаг и начать с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC) .

1. В нижнем левом углу [портала Azure](https://portal.azure.com) нажмите кнопку **Создать** > **Веб + мобильный** > **Веб-сайт**.
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. В колонке **Веб-приложение** укажите URL-адрес > **Создать**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Через несколько секунд будет создано веб-приложение и появится его колонка. Выноска - это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять сайтом.
	
	![Website running][WebSiteRunningBlade]
	
4. Чтобы убедиться, что веб-приложение работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
Далее вы создадите гибридное подключение и службу BizTalk для веб-приложения.

<a name="CreateHC"></a>
## Создание гибридного подключения и службы BizTalk

1. Прокрутите колонку своего веб-приложения вниз и выберите **Гибридные подключения**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. На выноске "Гибридные подключения" нажмите кнопку **Добавить**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Откроется выноска **Добавление гибридного подключения**.  Поскольку это ваше первое гибридное подключение, параметр **Новое гибридное подключение** выбран автоматически, и откроется колонка **Создание гибридного подключения**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	В **колонке "Создание гибридного подключения"**:
	- в поле **Имя** укажите имя подключения;
	- в поле **Имя узла** введите имя локального компьютера, на котором размещен ваш ресурс;
	- в поле **Порт** введите номер порта, который использует ваш локальный ресурс (1433 для экземпляра SQL Server по умолчанию).
	- Нажмите кнопку **Служба Biz Talk**.


4. Откроется колонка **Создание службы BizTalk**. Введите имя службы BizTalk и нажмите кнопку **ОК**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Колонка **Создание службы BizTalk** закроется, и вы вернетесь к колонке **Создание гибридного подключения**.
	
5. На выноске "Создание гибридного подключения" нажмите кнопку **ОК**. 
	
	![Click OK][CreateBTScomplete]
	
6. После завершения процесса в области уведомлений на портале появится сообщение об успешном создании подключения.
	<!-- TODO

    На этом шаге ничего не получается. У меня не получается создать службу BizTalk на тестовой версии портала. Пришлось перейти на старую версию портала
	(полная версия) и создать службу BizTalk. Но, кажется, не получается подключить их: после выполнения шага
	"Создание гибридного подключения" отображается ошибка
	"Не удалось создать гибридное подключение RelecIoudHC". Не 
	удалось найти тип ресурса в пространстве имен 
	"Microsoft.BizTaIkServices для api версии 2014-06-01".
	
	Ошибка указывает, что невозможно найти тип, а не экземпляр.
	![Success notification][CreateHCSuccessNotification]
	-->
7. Теперь в колонке веб-приложения значок **Гибридные подключения** показывает, что создано одно гибридное подключение.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>
## Установка локального диспетчера гибридных подключений для завершения подключения

1. В колонке веб-приложения щелкните значок "Гибридные подключения". 
	
	![Hybrid connections icon][HCIcon]
	
2. В колонке **Гибридные подключения** в столбце **Состояние** для недавно добавленной конечной точки показано **Не подключено**. Щелкните подключение, чтобы настроить его.
	
	![Not connected][NotConnected]
	
	Откроется выноска гибридного подключения.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. В колонке выберите элемент **Установка прослушивателя**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Откроется колонка **Свойства гибридного подключения**. В разделе **Локальный диспетчер гибридных подключений** выберите ссылку **Щелкните, чтобы установить**.
	
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

Теперь по завершении инфраструктуры гибридных подключений вы можете создать гибридное приложение, которое использует ее. 

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

<a name="NextSteps"></a>
## Дальнейшие действия ##

- Информацию о создании веб-приложения ASP.NET, которое использует гибридное подключение, см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979)

- Сведения об использовании гибридного подключения с мобильной службой см. в статье [Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Дополнительные ресурсы

[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Веб-сайт гибридных подключений](http://azure.microsoft.com/services/biztalk-services/)

[Службы BizTalk: Вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридное подключение"](biztalk-dashboard-monitor-scale-tabs.md)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочные материалы по веб-сайтам и веб-приложениям в службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=49-->
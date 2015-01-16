<properties title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Гибридные подключения: подключение веб-сайта Azure к локальному ресурсу" description="Создание подключение между веб-сайтом Azure и локальным ресурсом, который использует статический порт TCP" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Подключение веб-сайта Azure к локальным ресурсам с помощью гибридных подключений

Вы можете подключить веб-сайт Microsoft Azure к любому локальному ресурсу, который использует статический TCP-порт: например, SQL Server, MySQL, веб-интерфейсы API HTTP, мобильные службы и самые настраиваемые веб-службы. В этой статье показано, как создать гибридное подключение между веб-сайтом Azure и локальной базой данных SQL Server.

> [WACOM.NOTE] Часть функции "Веб-сайты" гибридных подключений доступна только на [портале предварительной версии Azure](https://portal.azure.com). Сведения о создании подключения в службах BizTalk см. в разделе [Гибридные подключения](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Предварительные требования
- Подписка Azure. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/). 

- Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Мы рекомендуем использовать экземпляр SQL Server по умолчанию, так как он использует статический порт 1433. Информацию об установке и настройке экспресс-выпуска SQL Server для использования с гибридными подключениями см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979).

- Компьютер, на котором установлен локальный агент диспетчера гибридных подключений, описан далее в этой статье:

	- должен подключаться к Azure через порт 5671;
	- Должен подключаться к hostname:portnumber локального источника. 

> [WACOM.NOTE] В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.


##В этой статье


[Создание веб-сайта на портале Azure (предварительная версия)](#CreateSite)

[Создание гибридного подключения и службы BizTalk](#CreateHC)

[Установка локального диспетчера гибридных подключений для завершения подключения](#InstallHCM)

[Дальнейшие действия](#NextSteps)


## Создание веб-сайта на портале предварительной версии Azure

> [WACOM.NOTE] Если вы уже создали веб-сайт на портале предварительной версии Azure, который хотите использовать в этом учебнике, можно пропустить этот шаг и начать с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC) .

1. В левом нижнем углу [портала предварительной версии Azure](https://portal.azure.com) щелкните **Создать** и затем выберите **Веб-сайт**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. В колонке **Веб­сайт** укажите имя веб-сайта и щелкните кнопку **Создать**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Через несколько мгновений будет создан веб-сайт и появится выноска веб-сайта. Выноска - это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять сайтом.
	
	![Website running][WebSiteRunningBlade]
	
4. Чтобы проверить, что веб-сайт работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
Далее вы создадите гибридное подключение и службу BizTalk для веб-сайта.

<a name="CreateHC"></a>
## Создание гибридного подключения и службы BizTalk

1. На портале (предварительная версия) прокрутите вниз колонку для веб-сайта и выберите пункт **Гибридные подключения**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. В колонке "Гибридные подключения" щелкните кнопку **Добавить**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Откроется колонка **Добавление гибридного подключения**.  Поскольку это ваше первое гибридное подключение, параметр **Новое гибридное подключение** выбран автоматически, и откроется колонка **Создание гибридного подключения**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	В колонке **Создание гибридного подключения**:
	- В поле **Имя** укажите имя подключения.
	- В поле **Имя узла** введите имя локального компьютера, на котором размещается ваш ресурс.
	- В поле **Порт** введите номер порта, который использует ваш локальный ресурс (1433 для экземпляра SQL Server по умолчанию).
	- Щелкните кнопку **Служба BizTalk**


4. Откроется колонка **Создание службы BizTalk**. Введите имя службы BizTalk и нажмите кнопку **ОК**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Колонка **Создание службы BizTalk** закроется, и вы вернетесь к колонке **Создание гибридного подключения**.
	
5. В колонке "Создание гибридного подключения" щелкните кнопку **ОК**. 
	
	![Click OK][CreateBTScomplete]
	
6. По завершении процесса в области "Уведомления" на портале появится сообщение, что подключение успешно создано.
	
	![Success notification][CreateHCSuccessNotification]
	
7. В колонке веб-сайта значок **Гибридные подключения** теперь покажет, что создано 1 гибридное подключение.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>
## Установка локального диспетчера гибридных подключений для завершения подключения

1. На выноске веб-сайта щелкните значок "Гибридные подключения". 
	
	![Hybrid connections icon][HCIcon]
	
2. В колонке **Гибридные подключения** в столбце **Состояние** для недавно добавленной конечной точки показано **Не подключено**. Щелкните подключение, чтобы настроить его.
	
	![Not connected][NotConnected]
	
	Откроется выноска гибридного подключения.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Щелкните колонку **Установка прослушивателя**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Откроется колонка **Свойства гибридного подключения**. Нажмите в **локальном диспетчере гибридных подключений** кнопку **Щелкните здесь, чтобы установить**.
	
	![Click here to install][ClickToInstallHCM]
	
5. В диалоговом окне предупреждения системы безопасности "Запуск приложения" нажмите кнопку **Запустить**, чтобы продолжить.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	В диалоговом окне **Элемент управления учетной записью пользователя** щелкните **Да**.
	
	![Choose Yes][UAC]
	
7. Диспетчер гибридных подключений скачан и установлен. 
	
	![Installing][HCMInstalling]
	
8. После завершения установки щелкните кнопку **Закрыть**.
	
	![Click Close][HCMInstallComplete]
	
	В колонке **Гибридные подключения** в столбце **Состояние** теперь отображается **Подключено**. 
	
	![Connected Status][HCStatusConnected]

Теперь по завершении инфраструктуры гибридных подключений вы можете создать гибридное приложение, которое использует ее. 

<a name="NextSteps"></a>
## Дальнейшие действия

- Информацию о создании веб-приложения ASP.NET, которое использует гибридное подключение, см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979).

- Информацию об использовании гибридного подключения с мобильной службой см. в разделе [Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Дополнительные ресурсы

[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Веб-сайт гибридных подключений](http://azure.microsoft.com/ru-ru/services/biztalk-services/)

[Службы BizTalk: Вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](http://azure.microsoft.com/ru-ru/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

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

<!--HONumber=35.1-->

<properties 
	pageTitle="Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure" 
	description="Создание подключения между веб-приложением в службе приложений Azure и локальным ресурсом, который использует статический TCP-порт." 
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

> [AZURE.NOTE]Часть функции "Веб-приложения" гибридных подключений доступна только на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Сведения о создании подключения в службах BizTalk см. в разделе [Гибридные подключения](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Предварительные требования
- Подписка Azure. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Мы рекомендуем использовать экземпляр SQL Server по умолчанию, так как он использует статический порт 1433. Информацию об установке и настройке SQL Server Express для использования с гибридными подключениями см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979).

- Компьютер, на котором установлен локальный агент диспетчера гибридных подключений, описан далее в этой статье:

	- должен подключаться к Azure через порт 5671;
	- должен подключаться к *имя_узла*:*номер_порта* локального источника. 

> [AZURE.NOTE]В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.


## Создание веб-приложения на портале Azure ##

> [AZURE.NOTE]Если вы уже создали веб-приложение на портале Azure, которое хотите использовать в этом учебнике, можно пропустить этот шаг и начать с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC).

1. В нижнем левом углу [портала Azure](https://portal.azure.com) нажмите **Создать** > **Интернет + мобильные устройства** > **Веб-сайт**.
	
	![Кнопка «Создать»][New]
	
	![Создание веб-приложения][NewWebsite]
	
2. В колонке **Веб-приложение** укажите URL-адрес > **Создать**.
	
	![Имя веб-сайта][WebsiteCreationBlade]
	
3. Через несколько секунд будет создано веб-приложение и появится его колонка. Выноска — это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять сайтом.
	
	![Запущенный веб-сайт][WebSiteRunningBlade]
	
4. Чтобы проверить, что веб-сайт работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.
	
	![Щелкните "Обзор", чтобы посмотреть веб-приложение][Browse]
	
	![Страница веб-приложения по умолчанию][DefaultWebSitePage]
	
Далее вы создадите гибридное подключение и службу BizTalk для веб-приложения.

<a name="CreateHC"></a>
## Создание гибридного подключения и службы BizTalk ##

1. Прокрутите колонку веб-приложения вниз и выберите **Гибридные подключения**.
	
	![Гибридные подключения][CreateHCHCIcon]
	
2. На выноске «Гибридные подключения» щелкните кнопку **Добавить**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Откроется выноска **Добавление гибридного подключения**. Поскольку это ваше первое гибридное подключение, параметр **Новое гибридное подключение** выбран автоматически, и откроется выноска **Создание гибридного подключения**.
	
	![Создание гибридного подключения][TwinCreateHCBlades]
	
	В колонке **Создание гибридного подключения**: в поле **Имя** укажите имя подключения, в поле **Имя узла** введите имя локального компьютера, на котором размещен ресурс, в поле **Порт** введите номер порта, который использует локальный ресурс (1433 для экземпляра SQL Server по умолчанию). Нажмите **Служба Biz Talk**


4. Откроется колонка **Создание службы BizTalk**. Введите имя службы BizTalk и нажмите кнопку **ОК**.
	
	![Создание службы BizTalk][CreateHCCreateBTS]
	
	Колонка **Создание службы BizTalk** закроется, и вы вернетесь к колонке **Создание гибридного подключения**.
	
5. На выноске «Создание гибридного подключения» щелкните кнопку **ОК**.
	
	![Нажмите кнопку "ОК"][CreateBTScomplete]
	
6. После завершения процесса в области уведомлений на портале появится сообщение об успешном создании подключения.
	<!-- TODO

Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the old portal
(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
Create hybrid conn step, you get the following error
Failed to create hybrid connection RelecIoudHC. The 
resource type could not be found in the namespace 
'Microsoft.BizTaIkServices for api version 2014-06-01'.

The error indicates it couldn't find the type, not the instance.
![Success notification][CreateHCSuccessNotification]
-->
7. Теперь в колонке веб-приложения значок **Гибридные подключения** показывает, что создано одно гибридное подключение.
	
	![Создано одно гибридное подключение][CreateHCOneConnectionCreated]
	
На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>
## Установка локального диспетчера гибридных подключений для выполнения подключения ##

1. В колонке веб-приложения щелкните значок "Гибридные подключения". 
	
	![Значок «Гибридные подключения»][HCIcon]
	
2. На выноске **Гибридные подключения** в столбце **Состояние** для недавно добавленных конечных точек показано **Не подключено**. Щелкните подключение, чтобы настроить его.
	
	![Не подключено][NotConnected]
	
	Откроется выноска гибридного подключения.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Щелкните на выноске **Установка прослушивателя**.
	
	![Щелкните «Установка прослушивателя»][ClickListenerSetup]
	
4. Откроется выноска **Свойства гибридного подключения**. Нажмите в **локальном диспетчере гибридных подключений** кнопку **Щелкните здесь, чтобы установить**.
	
	![Щелкните здесь, чтобы установить][ClickToInstallHCM]
	
5. В диалоговом окне предупреждения системы безопасности «Запуск приложения» нажмите кнопку **Запустить**, чтобы продолжить.
	
	![Нажмите кнопку «Запустить», чтобы продолжить][ApplicationRunWarning]
	
6.	В диалоговом окне **Элемент управления учетной записью пользователя** щелкните **Да**.
	
	![Щелкните «Да»][UAC]
	
7. Диспетчер гибридных подключений скачан и установлен.
	
	![Установка][HCMInstalling]
	
8. После завершения установки щелкните кнопку **Закрыть**.
	
	![Щелкните кнопку «Закрыть»][HCMInstallComplete]
	
	На выноске **Гибридные подключения** в столбце **Состояние** теперь отображается **Подключено**.
	
	![Состояние «Подключено»][HCStatusConnected]

Теперь по завершении инфраструктуры гибридных подключений вы можете создать гибридное приложение, которое использует ее.

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

<a name="NextSteps"></a>
## Дальнейшие действия ##

- Сведения о создании веб-приложения ASP.NET, которое использует гибридное подключение, см. в разделе [Подключение в локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979).

- Сведения о создании гибридного подключения с мобильной службой см. в разделе [Подключение к локальному SQL Server с мобильной службы Azure с помощью гибридных подключений](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Дополнительные ресурсы

[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Веб-сайт гибридных подключений](http://azure.microsoft.com/services/biztalk-services/)

[Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](../biztalk-dashboard-monitor-scale-tabs/)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по веб-сайтам и веб-приложениям в службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=July15_HO4-->
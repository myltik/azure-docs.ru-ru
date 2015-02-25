<properties 
	pageTitle="Рекомендуемые ресурсы веб-заданий Azure" 
	description="Рекомендуемые ресурсы для обучения работе с элементами Azure WebJob и пакетом SDK для Azure для WebJobs." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tdykstra"/>

#Рекомендуемые ресурсы веб-заданий Azure

В данной теме содержатся ссылки на документацию о том, как применять веб-задания Azure и SDK веб-заданий Azure. Веб-задания Azure предоставляет простой способ запуска скриптов и программ в фоновом режиме на веб-сайтах Azure. Можно загружать и запускать исполняемые файлы, например cmd, bat, exe (.NET), ps1, sh, php, py, js и jar. Эти программы могут работать как веб-задания по расписанию (cron) или постоянно.

Пакет SDK веб-заданий упрощает использование хранилища Azure. Пакет SDK веб-заданий имеет систему привязки и запуска, которая работает совместно с хранилищем Microsoft Azure для BLOB-объектов, очередей и таблиц, а также очередей служебной шины.

Создание, развертывание веб-заданий и управление ими средствами Visual Studio не вызовет проблем. Веб-задания можно создавать из шаблонов, публиковать их и управлять ими (запуск/остановка/мониотринг/jnkflrf). 

Информационная панель веб-заданий на портале управления Azure предоставляет мощные возможности управления и полный контроль над выполнением веб-заданий, в том числе возможность вызова отдельных функций в самом веб-задании. Информационная панель также показывает состояние исполняемой среды и записи журнала. 

## Оглавление

* [Приступая к работе с заданиями WebJob](#getstarted)
* [Развертывание заданий WebJob](#deploy)
* [Проверка и отладка заданий WebJob](#debug)
* [Дополнительные ресурсы по заданиям WebJob](#additional)
* [Дополнительные ресурсы по пакету SDK для заданий WebJob](#additionalsdk)
* [Примеры приложений заданий WebJob](#samples)
* [Блоги](#blogs) 
* [Справочная информация по заданиям WebJob](#gethelp)

##<a name="getstarted"></a>Приступая к работе с заданиями WebJob

* [Введение в задания Azure WebJob](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Компоненты заданий Azure WebJob](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Информация о пакете SDK веб-заданий](../websites-dotnet-webjobs-sdk/)
* [Объявление о выпуске SDK для заданий Microsoft Azure WebJob версии 1.0.0 RTM](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Начало работы с пакетом SDK для заданий Azure WebJob](../websites-dotnet-webjobs-sdk-get-started/)
* [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [Как использовать табличное хранилище Azure с пакетом SDK для WebJob](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [Как использовать Azure Service Bus с пакетом SDK для WebJob](../websites-dotnet-webjobs-sdk-service-bus)
* [Краткий справочник по пакету SDK для Azure WebJob (скачиваемый PDF-файл)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Видео
	* [Задания WebJob и пакет SDK для заданий WebJob](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Серия видеопрограмм по заданиям Azure WebJob на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Введение в средства для работы с заданиями WebJob для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

См. также разделы [Развертывание заданий WebJob](#deploy) и [Проверка и отладка заданий WebJob](#debug).

##<a name="deploy"></a>Развертывание заданий WebJob

* [Развертывание веб-заданий Azure на веб-сайтах Azure](../websites-dotnet-deploy-webjobs/)
* [Как развернуть задания WebJob с использованием портала управления Azure](../web-sites-create-web-jobs/)
* [Включение предоставления заданий Azure WebJob из командной строки или постоянно](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Развертывание из Git приложения консоли .NET на Azure с помощью заданий WebJob](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Видео
	* [Введение в средства для работы с заданиями WebJob для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>Проверка и отладка заданий WebJob

* [Новые функции для разработки и отладки для задания Azure WebJob в Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Устранение неполадок, связанных с веб-сайтами Azure, в Visual Studio](../web-sites-dotnet-troubleshoot-visual-studio/)
* [Кто записал этот большой двоичный объект?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Размещение интерактивного кода в облаке](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Получение панели мониторинга для локальной разработки с помощью пакета SDK для заданий WebJob](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Добавление трассировки в Azure веб-сайтов и заданий WebJob](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Наблюдение, диагностика и устранение неисправностей службы хранилища Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Видео
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>Дополнительные ресурсы по заданиям WebJob

* [Общедоступная запись в блоге о заданиях Azure WebJob Магнуса Мортенссона (Magnus Mårtensson)](http://magnusmartensson.com/azure-webjobs-ga)
* [Документация по настройке заданий WebJob в GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Запуск веб-заданий Powershell на веб-сайтах Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Получение уведомлений после завершения заданий WebJob, инициированных Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Веб-сайты Azure: Конструирование веб-приложений для бизнеса c широкими возможностями масштабирования](https://channel9.msdn.com/Events/Build/2014/3-626). Охватывает масштабирование веб-сайтов Azure с веб-заданиями, в том числе SDK веб-заданий.
* [Простые правила резервного копирования веб-сайтов с заданиями WebJob](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Веб-сайты Microsoft Azure и Cloud Services Slow по первому требованию](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Показывает применение веб-заданий для симуляции компонента AlwaysOn, который доступен только для ветви веб-сайтов уровня Standard.
* [Нормальное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Для получения информации относительно правильного завершения работы SDK для заданий WebJob см. [Нормальное завершение работы](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful)).
* [Масштабирование приложения с помощью веб-сайтов Azure](http://msdn.microsoft.com/ru-ru/magazine/dn786914.aspx)
* Видео
	* [Масштабирование заданий WebJob](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Видеопрограммы по заданиям Azure WebJob, Магнус Мортенссон (Magnus Mårtensson)](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Серия видеопрограмм по заданиям Azure WebJob на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Дополнительные ресурсы по пакету SDK для заданий WebJob

* [Переключатели, привязки, маршрутные параметры в заданиях Azure](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Привязки хранилищ Azure
	* [Большие двоичные объекты](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Очереди](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Таблицы](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Как работает [BlobTrigger]](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Расширенные привязки с помощью SDK веб-заданий Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Задания WebJob по загрузке файлов FREB в службу хранилища Azure с помощью пакета SDK для заданий WebJob](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Размещение заданий Azure WebJob вне Azure с преимуществами ведения журнала из заданий WebJob, размещенных на Azure](http://bypassion.dk/?p=510)
* [Создание средства импорта данных с помощью заданий Azure WebJob](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Видео
	* [Серия видеопрограмм по заданиям Azure WebJob на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Примеры приложений заданий WebJob

* [Примеры приложений, предлагаемых командой заданий WebJob на GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Простой веб-сайт Azure с ядром на заданиях WebJob с использованием SDK для заданий WebJob](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Демонстрирует применение веб-заданий по расписанию или при наступлении определенных событий. См. публикацию в блоге [Создание SiteMonitR с помощью SDK для заданий Azure WebJob](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Блоги

* [Блог Azure](/blog)
* [Блог Amit Apple](http://blog.amitapple.com/). Ориентирован на задания WebJob (не SDK).
* [Блог Магнуса Мортенссона (Magnus Mårtensson)](http://magnusmartensson.com/)

##<a name="gethelp"></a>Справочная информация по заданиям WebJob

* [StackOverflow для заданий WebJob](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow для SDK заданий WebJob](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Форум по Azure и ASP.NET](http://forums.asp.net/1247.aspx)
* [Форум по веб-сайтам Azure](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Сайт пользовательских мнений по веб-сайтам Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Используйте хештег #AzureWebJobs.


<!--HONumber=42-->

<properties 
	pageTitle="Документация по веб-заданиям Azure" 
	description="Рекомендуемые ресурсы для обучения работе с элементами Azure WebJob и пакетом SDK для Azure для WebJobs." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Документация по веб-заданиям Azure

## Обзор

В данной теме содержатся ссылки на документацию о том, как применять веб-задания Azure и SDK веб-заданий Azure. Веб-задания Azure предоставляют простой способ запуска скриптов и программ в виде фоновых процессов [веб-приложений службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714). Можно загружать и запускать исполняемые файлы, например cmd, bat, exe (.NET), ps1, sh, php, py, js и jar. Эти программы могут работать как веб-задания по расписанию (cron) или постоянно.

Пакет SDK веб-заданий упрощает использование хранилища Azure. Пакет SDK веб-заданий имеет систему привязки и запуска, которая работает совместно с хранилищем Microsoft Azure для BLOB-объектов, очередей и таблиц, а также очередей служебной шины.

Создание, развертывание веб-заданий и управление ими средствами Visual Studio не вызовет проблем. Веб-задания можно создавать из шаблонов, публиковать их и управлять ими (запуск/остановка/мониторинг/jnkflrf).

Панель мониторинга веб-заданий на портале Azure предоставляет широкие возможности управления и полный контроль над выполнением веб-заданий, включая возможность вызова отдельных функций в рамках самого веб-задания. Информационная панель также показывает состояние исполняемой среды и записи журнала.

##<a name="getstarted"></a>Начало работы с веб-заданиями и пакетом SDK веб-заданий

* [Общие сведения о веб-заданиях Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Веб-задания Azure потрясающие, начните использовать их прямо сейчас!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Запись блога Троя Ханта (Troy Hunt).)
* [Компоненты веб-заданий Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Информация о пакете SDK веб-заданий](websites-dotnet-webjobs-sdk.md)
* [Руководство по фоновым заданиям на основе шаблонов и рекомендаций от Майкрософт](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Объявление о выпуске SDK веб-заданий Microsoft Azure версии 1.0.0 RTM](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Начало работы с пакетом SDK веб-заданий Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Использование хранилища больших двоичных объектов Azure с пакетом SDK веб-заданий](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Использование табличного хранилища Azure с пакетом SDK веб-заданий](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Использование служебной шины Azure с пакетом SDK веб-заданий](websites-dotnet-webjobs-sdk-service-bus.md)
* [Краткий справочник по пакету SDK веб-заданий (скачиваемый PDF-файл)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Документация по настройке веб-заданий в GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Видеоролики
	* [Веб-задания и пакет SDK веб-заданий](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Введение в средства для работы с веб-заданиями для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

См. также разделы [Развертывание веб-заданий](#deploy) и [Тестирование и отладка веб-заданий](#debug).

##<a name="deploy"></a>Развертывание веб-заданий

* [Развертывание веб-заданий Azure с помощью Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Как развернуть веб-задания с использованием портала управления Azure](web-sites-create-web-jobs.md)
* [Включение предоставления веб-заданий Azure из командной строки или постоянно](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Развертывание из Git приложения консоли .NET на Azure с помощью веб-заданий](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Развертывание веб-заданий F# в Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Развертывание пользовательских служб в качестве веб-заданий Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Видеоролики
	* [Введение в средства для работы с веб-заданиями для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Планирование веб-заданий

* [Диалоговое окно "Добавление веб-заданий Azure"](websites-dotnet-deploy-webjobs.md#configure)
* [Создание запланированного веб-задания на портале управления Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Подключение задания планировщика к веб-заданию](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Планирование веб-заданий Azure с помощью выражений cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)

##<a name="debug"></a>Проверка и отладка веб-заданий

* [Новые функции для разработки и отладки для веб-заданий Azure в Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Просмотр панели мониторинга веб-заданий](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Запись журналов с использованием пакета SDK веб-заданий и просмотр журналов на панели мониторинга](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Удаленная отладка веб-заданий](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Кто записал этот большой двоичный объект?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Размещение интерактивного кода в облаке](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Получение панели мониторинга для локальной разработки с помощью пакета SDK веб-заданий](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Добавление трассировки в веб-задания Azure](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Наблюдение, диагностика и устранение неисправностей хранилища Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Видеоролики
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Масштабирование веб-заданий

* [Масштабирование приложения с помощью веб-сайтов Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Служба приложений Azure: конструирование веб-приложений для бизнеса c широкими возможностями масштабирования](https://channel9.msdn.com/Events/Build/2014/3-626). Охватывает масштабирование веб-приложений с веб-заданиями, в том числе пакет SDK веб-заданий.
* Видеоролики
	* [Масштабирование веб-заданий](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Дополнительные ресурсы по веб-заданиям

* [Общедоступная запись в блоге о веб-заданиях Azure Магнуса Мортенссона (Magnus Mårtensson)](http://magnusmartensson.com/azure-webjobs-ga)
* [Запуск веб-заданий Powershell в службе приложений Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Получение уведомлений после завершения веб-заданий, инициированных Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Простые правила резервного копирования веб-приложений с веб-заданиями](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Замедление работы веб-приложений и облачных служб Azure по первому требованию](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Показывает применение веб-заданий для симуляции компонента AlwaysOn, который доступен только для ценовой категории Standard.
* [Нормальное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Для получения информации относительно правильного завершения работы пакета SDK веб-заданий см. [Нормальное завершение работы](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Автоматизация резервного копирования с помощью веб-заданий Azure и AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Видеоролики
	* [Видеопрограммы по веб-заданиям Azure от Магнуса Мортенссона (Magnus Mårtensson)](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Дополнительные ресурсы по пакету SDK для веб-заданий

* [Исходный код пакета SDK веб-заданий](https://github.com/Azure/azure-webjobs-sdk)
* [Как работает [BlobTrigger]](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Расширенные привязки с помощью SDK веб-заданий Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Веб-задания по загрузке файлов FREB в службу хранилища Azure с помощью пакета SDK для веб-заданий](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Размещение веб-заданий Azure вне Azure с преимуществами ведения журнала из веб-заданий, размещенных на Azure](http://bypassion.dk/?p=510)
* [Создание средства импорта данных с помощью веб-заданий Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Видеоролики
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Примеры приложений веб-заданий

* [Примеры приложений, предлагаемых командой разработчиков веб-заданий на GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Простое веб-приложение Azure с ядром на веб-заданиях с использованием SDK веб-заданий](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Демонстрирует применение веб-заданий по расписанию или при наступлении определенных событий. См. публикацию в блоге [Создание SiteMonitR с помощью SDK веб-заданий Azure](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Блоги

* [Блог Azure](/blog)
* [Блог Amit Apple](http://blog.amitapple.com/). Ориентирован на задания WebJob (не SDK).
* [Блог Магнуса Мортенссона (Magnus Mårtensson)](http://magnusmartensson.com/)

##<a name="gethelp"></a>Справочная информация о веб-заданиях

* [StackOverflow для веб-заданий](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow для SDK веб-заданий](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Форум по Azure и ASP.NET](http://forums.asp.net/1247.aspx)
* [Форум по веб-приложениям службы приложений Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Сайт пользовательских мнений по веб-приложениям Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Используйте хештег #AzureWebJobs.
* [Сообщение об ошибке или проблеме в веб-задании](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=Sept15_HO4-->
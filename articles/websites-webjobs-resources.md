<properties 
	pageTitle="Ресурсы по веб-заданиям Azure" 
	description="Рекомендуемые ресурсы по изучению работы с веб-заданиями Azure и пакетом SDK для веб-заданий Azure." 
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
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Ресурсы по веб-заданиям Azure

## Обзор

В данной теме содержатся ссылки на документацию о том, как применять веб-задания Azure и SDK веб-заданий Azure. Веб-задания Azure предоставляют простой способ запуска сценариев и программ в виде фоновых процессов [веб-приложений службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714). Можно загружать и запускать исполняемые файлы, например cmd, bat, exe (.NET), ps1, sh, php, py, js и jar. Эти программы могут работать как веб-задания по расписанию (cron) или постоянно.

Пакет SDK веб-заданий упрощает использование хранилища Azure. Пакет SDK веб-заданий имеет систему привязки и запуска, которая работает совместно с хранилищем Microsoft Azure для BLOB-объектов, очередей и таблиц, а также очередей служебной шины.

Создание, развертывание веб-заданий и управление ими средствами Visual Studio не вызовет проблем. Веб-задания можно создавать из шаблонов, публиковать их и управлять ими (запуск/остановка/мониторинг/отладка). 

Панель мониторинга веб-заданий на портале Azure предоставляет широкие возможности управления и полный контроль над выполнением веб-заданий, включая возможность вызова отдельных функций в рамках самого веб-задания. Информационная панель также показывает состояние исполняемой среды и записи журнала. 

##<a name="getstarted"></a>Начало работы с веб-заданиями и пакетом SDK для веб-заданий

* [Введение в задания Azure WebJob](http://www.hanselman.comblog.md/IntroducingWindowsAzureWebJobs.aspx)
* [Компоненты веб-заданий Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Что такое пакет SDK веб-заданий](websites-dotnet-webjobs-sdk.md)
* [Объявление о выпуске пакета SDK для веб-заданий Microsoft Azure версии 1.0.0 (RTM)](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Начало работы с пакетом SDK для веб-заданий Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Использование хранилища очередей Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Использование хранилища BLOB-объектов Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Использование табличного хранилища Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Использование служебной шины Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-service-bus.md)
* [Краткий справочник по пакету SDK для Azure WebJob (скачиваемый PDF-файл)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Видео
	* [Задания WebJob и пакет SDK для заданий WebJob](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Серия видео о веб-заданиях Azure на веб-сайте channel9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Введение в средства для работы с заданиями WebJob для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

См. также разделы [Развертывание заданий WebJob](#deploy) и [Проверка и отладка заданий WebJob](#debug).

##<a name="deploy"></a>Развертывание заданий WebJob

* [Развертывание веб-заданий Azure на веб-сайтах Azure](websites-dotnet-deploy-webjobs.md)
* [Развертывание веб-заданий с использованием портала управления Azure](web-sites-create-web-jobs.md)
* [Включение предоставления заданий Azure WebJob из командной строки или постоянно](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Развертывание из Git приложения консоли .NET на Azure с помощью заданий WebJob](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Видео
	* [Введение в средства для работы с заданиями WebJob для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Планирование веб-заданий

* [Диалоговое окно добавления веб-заданий Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Создание запланированного веб-задания на портале управления Azure](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Проверка и отладка заданий WebJob

* [Новые функции для разработки и отладки для задания Azure WebJob в Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Просмотр панели мониторинга веб-заданий](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Запись журналов с использованием пакета SDK для веб-заданий и просмотр журналов на панели мониторинга](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Веб-задания по удаленной отладке](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Кто записал этот большой двоичный объект?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Размещение интерактивного кода в облаке](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Получение панели мониторинга для локальной разработки с помощью пакета SDK для заданий WebJob](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Добавление трассировки в Azure веб-сайтов и заданий WebJob](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Наблюдение, диагностика и устранение неисправностей службы хранилища Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
* Видео
	* [Средства для работы с заданиями WebJob и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Масштабирование веб-заданий

* [Масштабирование приложения с помощью веб-сайтов Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Веб-сайты Azure: Конструирование веб-приложений для бизнеса c широкими возможностями масштабирования](https://channel9.msdn.com/Events/Build/2014/3-626). Охватывает тему масштабирования веб-сайтов Azure с веб-заданиями, а также тему пакета SDK для веб-заданий.
* Видео
	* [Масштабирование заданий WebJob](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Дополнительные ресурсы по заданиям WebJob

* [Общедоступная запись в блоге о заданиях Azure WebJob Магнуса Мортенссона (Magnus Mårtensson)](http://magnusmartensson.com/azure-webjobs-ga)
* [Документация по настройке заданий WebJob в GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Запуск веб-заданий Powershell на веб-сайтах Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Получение уведомлений после завершения заданий WebJob, инициированных Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Простые правила резервного копирования веб-сайтов с заданиями WebJob](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Замедление работы веб-сайтов и облачных служб Azure по первому требованию](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Показывает применение веб-заданий для симуляции компонента AlwaysOn, который доступен только для ветви веб-сайтов уровня Standard.
* [Нормальное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Сведения о нормальном завершении работы пакета SDK для веб-заданий см. в разделе [Нормальное завершение работы](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).).
* Видео
	* [Видео о веб-заданиях Azure от Магнуса Мортенссона (Magnus Mårtensson)](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Серия видео о веб-заданиях Azure на веб-сайте channel9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Дополнительные ресурсы по пакету SDK для заданий WebJob

* [Переключатели, привязки, маршрутные параметры в заданиях Azure](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Привязки хранилищ Azure
	* [BLOB-объекты](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Очереди](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Таблицы](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Как работает [BlobTrigger]](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Расширенные привязки с использованием пакета SDK для веб-заданий Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Задания WebJob по загрузке файлов FREB в службу хранилища Azure с помощью пакета SDK для заданий WebJob](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Размещение заданий Azure WebJob вне Azure с преимуществами ведения журнала из заданий WebJob, размещенных на Azure](http://bypassion.dk/?p=510)
* [Создание средства импорта данных с помощью заданий Azure WebJob](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Видео
	* [Серия видео о веб-заданиях Azure на веб-сайте channel9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

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
* [Форум по веб-сайтам Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Сайт пользовательских мнений по веб-сайтам Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Использование хэш-тега #AzureWebJobs.

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

<!--HONumber=49-->
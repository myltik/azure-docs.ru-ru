<properties pageTitle="Рекомендуемые ресурсы веб-заданий Azure " metaKeywords="Azure WebJobs, Azure WebJobs SDK, Azure storage, Azure queues, Azure tables, Azure Service Bus" description="Recommended resources for learning how to use Azure WebJobs and the Azure WebJobs SDK." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Azure WebJobs Recommended Resources" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/27/2014" ms.author="tdykstra" />

#Рекомендуемые ресурсы веб-заданий Azure

В данной теме содержатся ссылки на документацию о том, как применять веб-задания Azure и SDK веб-заданий Azure. Веб-задания Azure предоставляет простой способ запуска скриптов и программ в фоновом режиме на веб-сайтах Azure. Можно загружать и запускать исполняемые файлы, например cmd, bat, exe (.NET), ps1, sh, php, py, js и jar. Эти программы могут работать как веб-задания по расписанию (cron) или постоянно.

Пакет SDK веб-заданий упрощает использование хранилища Azure. Пакет SDK веб-заданий имеет систему привязки и запуска, которая работает совместно с хранилищем Microsoft Azure для BLOB-объектов, очередей и таблиц, а также очередей служебной шины.

Создание, развертывание веб-заданий и управление ими средствами Visual Studio не вызовет проблем. Веб-задания можно создавать из шаблонов, публиковать их и управлять ими (запуск/остановка/мониотринг/jnkflrf). 

Информационная панель веб-заданий на портале управления Azure предоставляет мощные возможности управления и полный контроль над выполнением веб-заданий, в том числе возможность вызова отдельных функций в самом веб-задании. Информационная панель также показывает состояние исполняемой среды и записи журнала. 

## Оглавление

* [Приступая к работе с веб-заданиями](#getstarted)
* [Развертывание веб-заданий](#deploy)
* [Проверка и отладка веб-заданий](#debug)
* [Дополнительные ресурсы по веб-заданиям](#additional)
* [Дополнительные ресурсы пакета SDK WebJobs](#additionalsdk)
* [Примеры приложений веб-заданий](#samples)
* [Блоги](#blogs) 
* [Справочная информация по веб-заданиям](#gethelp)

##<a name="getstarted"></a>Приступая к работе с веб-заданиями

* [Введение в веб-задания Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Компоненты веб-заданий Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Что такое пакет SDK веб-заданий](../websites-dotnet-webjobs-sdk/)
* [Объявление о выпуске SDK веб-заданий Microsoft Azure версии 1.0.0 RTM](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Начало работы с пакетом SDK веб-заданий Azure](../websites-dotnet-webjobs-sdk-get-started/)
* [Как работать с хранилищем очередей Azure с помощью пакета SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* Видеоклипы
	* [Веб-задания и пакет SDK веб-заданий](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Введение в средства для работы с веб-заданиями для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

См. также разделы [Развертывание веб-заданий](#deploy) и [Тестирование и отладка веб-заданий](#debug).

##<a name="deploy"></a>Развертывание веб-заданий

* [Развертывание веб-заданий Azure на веб-сайтах Azure](../websites-dotnet-deploy-webjobs/)
* [Как развернуть веб-задания с использованием портала управления Azure](../web-sites-create-web-jobs/)
* [Включение предоставления веб-заданий Azure из командной строки или постоянно](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Развертывание из Git приложения консоли .NET на Azure средствами веб-заданий](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Видеоклипы
	* [Введение в средства для работы с веб-заданиями для Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>Проверка и отладка веб-заданий

* [Устранение неполадок, связанных с веб-сайтами Azure, в Visual Studio](../web-sites-dotnet-troubleshoot-visual-studio/).
* [Кто записал этот BLOB-объект?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Размещение интерактивного кода в облаке](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Получение информационной панели для локальной разработки с SDK веб-заданий](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Добавление трассировки в Azure веб-сайтов и веб-заданий](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Наблюдение, диагностика и устранение неисправностей хранилища Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Видеоклипы
	* [Средства для работы с веб-заданиями и удаленная отладка](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>Дополнительные ресурсы по веб-заданиям

* [Общедоступная запись в блоге о веб-заданиях Azure Магнуса Вортенссона (Magnus Mårtensson)](http://magnusmartensson.com/azure-webjobs-ga)
* [Документация по настройке веб-заданий в GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Запуск веб-заданий Powershell на веб-сайтах Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Получение уведомлений после завершения веб-заданий, инициированных Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Веб-сайты Azure: Разработка широкомасштабируемых, готовых к использованию проектов веб-приложений](https://channel9.msdn.com/Events/Build/2014/3-626). Охватывает масштабирование веб-сайтов Azure с веб-заданиями, в том числе SDK веб-заданий.
* [Простые правила резервного копирования веб-сайтов с веб-заданиями](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Веб-сайты Windows Azure и Cloud Services Slow по первому требованию](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Показывает применение веб-заданий для симуляции компонента AlwaysOn, который доступен только для ветви веб-сайтов уровня Standard.
* [Нормальное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Для получения сведений относительно правильного завершения работы SDK веб-заданий см. [Нормальное завершение работы](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful).)
* [Масштабирование приложения с помощью веб-сайтов Azure](http://msdn.microsoft.com/ru-ru/magazine/dn786914.aspx)
* Видеоклипы
	* [Масштабирование веб-заданий](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Видеопрограммы по веб-заданиям Azure, Магнус Мортенссон (Magnus Mårtensson)](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Дополнительные ресурсы пакета SDK WebJobs

* [Переключатели, привязки, маршрутные параметры в заданиях Azure](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Привязки хранилищ Azure
	* [BLOB-объекты](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Очереди](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Таблицы](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Как работает [BLOB-переключатель]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Расширенные привязки с помощью SDK веб-заданий Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Веб-задания по загрузке файлов FREB в хранилище Storage с помощью SDK веб-заданий](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Размещение веб-заданий Azure вне Azure с преимуществами ведения журнала из веб-заданий, размещенных на Azure](http://bypassion.dk/?p=510)
* [Создание средства импорта данных с помощью веб-заданий Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Видеоклипы
	* [Серия видеопрограмм по веб-заданиям Azure на канале 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Примеры приложений веб-заданий

* [Примеры приложений, предлагаемых командой веб-заданий на GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Простой веб-сайт Azure с ядром на веб-заданиях с использованием SDK веб-заданий](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Демонстрирует применение веб-заданий по расписанию или при наступлении определенных событий. См. публикацию в блоге [Создание SiteMonitR с помощью SDK веб-заданий Azure ](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Блоги

* [Блог Azure](/blog)
* [Amit Apple's blog](http://blog.amitapple.com/). Ориентирован на веб-задания (не SDK).
* [Блог Магнуса Мортинссона (Magnus Mårtensson)](http://magnusmartensson.com/)

##<a name="gethelp"></a>Справочная информация по веб-заданиям

* [StackOverflow для веб-заданий](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow для SDK веб-заданий](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Форум по Azure и ASP.NET](http://forums.asp.net/1247.aspx)
* [Форум по веб-сайтам Azure](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Сайт пользовательских мнений по веб-сайтам Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Используйте хештег #AzureWebJobs.

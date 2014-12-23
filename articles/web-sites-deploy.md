<properties pageTitle="Развертывание веб-сайта Azure" metaKeywords="Azure развертывание опубликовать веб-сайт" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

#Как развернуть веб-сайт Azure

У вас есть множество вариантов развертывания своего содержимого на веб-сайте Azure. В этом разделе приводится краткое описание каждого варианта и даются ссылки на дополнительные сведения.

* [Развертывание из облачной системы управления версиями](#cloud)
	* Visual Studio Online (VSO)
	* Репозиторий веб-сайтов с использованием Git
	* Репозиторий веб-сайтов с использованием Mercurial
	* DropBox
* [Развертывание из IDE](#ide)
	* Visual Studio
	* WebMatrix
* [Развертывание с помощью служебной программы FTP](#ftp)
* [Развертывание из локальной системы управления версиями](#onpremises)
	* Team Foundation Server (TFS)
	* Локальные репозитории Git или Mercurial
* [Использование программ командной строки и API-интерфейса управления Azure REST](#commandline)
	* MSBuild
	* Сценарии FTP
	* Windows PowerShell
	* API управления .NET
	* Межплатформенный интерфейс командной строки (xpat-cli)
	* Командная строка веб-развертывания


##<a name="cloud"></a>Развертывание из облачной системы управления версиями

Лучший способ развертывания веб-сайта - установка [непрерывного рабочего процесса доставки](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery), интегрированного с вашей [системой управления версиями](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). Автоматизация не только повышает эффективность процесса разработки, но также может сделать более управляемыми и надежными процессы резервного копирования и восстановления. 

Если система управления версиями еще не установлена, проще всего для начала использовать систему управления версиями, размещенную в облаке.

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (ранее Team Foundation Service) - это облачные решение корпорации Майкрософт для управления версиями и совместной работы. Служба предоставляется бесплатно для группы, включающей до 5 разработчиков. Вы можете настроить в VSO выполнение непрерывной доставки на веб-сайт Azure, а в репозитории можно использовать [Git или TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Для получения дополнительных сведений см. следующие ресурсы:

* [Непрерывная доставка в Azure с использованием VSO и TFVC](http://www.visualstudio.com/ru-ru/learn/continuous-delivery-in-vs). Краткий пошаговый учебник, в котором показано, как настроить непрерывную доставку из VSO на веб-сайт Azure, используя TFVC. TFVC - это централизованный компонент системы управления версиями в VSO в противовес Git, который является распределенным компонентом системы управления версиями.
* [Непрерывная доставка в Azure с использованием VSO и TFVC](/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso/). Этот учебник схож с предыдущим, но дополнен описанием шагов регистрации для получения учетной записи VSO и регистрации проекта в системе управления версиями.
* [Непрерывная доставка в Azure с использованием Visual Studio Online и Git](http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-continuous-delivery-use-vso-git/). Схож с предыдущим учебником, но использует Git вместо TFVC.

###<a name="git"></a>Репозиторий веб-сайтов с использованием Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) - это популярная распределенная система управления версиями. В Azure имеются встроенные функции, упрощающие автоматизацию развертывания на веб-сайте Azure из популярных веб-сайтов репозитория, хранящихся в репозиториях Git, включая [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) и [BitBucket](https://bitbucket.org/). Преимущество использования Git для развертывания заключается в том, что в случае необходимости довольно легко выполнить откат к более раннему развертыванию. 

Для получения дополнительных сведений см. следующие ресурсы:

* [Публикация из системы управления версиями на веб-сайты Azure](/ru-ru/documentation/articles/web-sites-publish-source-control/). Использование Git для выполнения публикации непосредственно с локального компьютера на веб-сайт Azure (в Azure такой способ публикации называется "локальный репозиторий Git"). Здесь также показано, как включить непрерывное развертывание репозиториев Git из GitHub, CodePlex или BitBucket.
* [Развертывание на веб-сайтах с GitHub с помощью Kudu](/ru-ru/documentation/videos/deploying-to-azure-from-github/). Видео, созданное Скоттом Хансельманом (Scott Hanselman) и Дэвидом Эббо (David Ebbo), в котором показан процесс развертывания веб-сайта непосредственно из GitHub на веб-сайт Azure.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Репозиторий веб-сайтов с использованием Mercurial

Если вы используете [Mercurial](http://mercurial.selenic.com/) в качестве вашей системы управления версиями и храните репозиторий в [CodePlex](http://www.codeplex.com/) или [BitBucket](https://bitbucket.org/), вы можете использовать встроенные функции веб-сайтов Azure для автоматического развертывания вашего контента.

Для получения дополнительных сведений о развертывании с использованием Mercurial см. следующие ресурсы:

* [Публикация из системы управления версиями на веб-сайты Azure](/ru-ru/documentation/articles/web-sites-publish-source-control/). В этом учебнике показано, как опубликовать репозиторий Git, но для репозиториев Mercurial, размещенных в CodePlex или BitBucket, процесс будет аналогичный.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>DropBox

[DropBox](https://www.dropbox.com/) не является системой управления версиями, но если вы сохраняете исходный код в DropBox, то можете автоматизировать развертывание из учетной записи DropBox.

* [Развертывание в Windows Azure с использованием Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Как использовать портал управления Azure для настройки развертывания в DropBox.
* [DropBox и веб-сайты Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). В этом видео показан пошаговый процесс подключения папки DropBox к веб-сайту Azure и продемонстрировано, как быстро вы можете запустить веб-сайт или осуществить его обслуживание, используя простое развертывание путем перетаскивания.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Развертывание из IDE

[Visual Studio](http://www.visualstudio.com/ru-ru/downloads/download-visual-studio-vs.aspx) и [WebMatrix](http://www.microsoft.com/web/webmatrix/) - это интегрированные среды разработки (IDE) корпорации Майкрософт, которые можно использовать для разработки веб-приложений. Обе среды предоставляют встроенные функции, упрощающие развертывание на веб-сайтах Azure.  Обе среды могут использовать [веб-развертывание](http://www.iis.net/downloads/microsoft/web-deploy) для автоматизации дополнительных задач, связанных с развертыванием, таких как развертывание базы данных и изменение строки подключения. В обеих средах можно выполнять развертывание с использованием [FTP и FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol). 

WebMatrix можно быстро установить и легко освоить, но Visual Studio предлагает значительно больше возможностей для работы с веб-сайтами Azure. В Visual Studio IDE вы можете создавать, останавливать, запускать и удалять веб-сайты Azure, можете просматривать журналы по мере их создания в режиме реального времени, а также выполнять удаленную отладку и многое другое. Visual Studio также интегрируется с такими системами управления версиями, как [Visual Studio Online](#vso), [Team Foundation Server](#tfs) и [репозитории Git](#git).

###<a name="vs"></a>Visual Studio

Для получения информации о выполнении развертывания на веб-сайты Azure из Visual Studio см. следующие ресурсы:

* [Начало работы с Azure и ASP.NET](/ru-ru/develop/net/tutorials/get-started/). Создание и развертывание простого веб-проекта ASP.NET MVC с помощью Visual Studio и веб-развертывания.
* [Как развернуть задания Azure WebJob на веб-сайтах Azure](/ru-ru/documentation/articles/websites-dotnet-deploy-webjobs/). Как настроить проекты консольных приложений для их развертывания как заданий WebJob.  
* [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL на веб-сайте Azure](/ru-ru/develop/net/tutorials/web-site-with-sql-database/). Создание и развертывание веб-проекта ASP.NET MVC с базой данных SQL с помощью Visual Studio, веб-развертывания и Entity Framework Code First Migrations.
* [Обзор веб-развертывания для Visual Studio и ASP.NET](http://msdn.microsoft.com/ru-ru/library/dd394698.aspx). Основные сведения о веб-развертывании с помощью Visual Studio. Устарели, но включают сведения, которые могут представлять интерес, в том числе обзор параметров для развертывания базы данных вместе с веб-приложением и список дополнительных задач развертывания, которые вам может потребоваться выполнить или вручную настроить их выполнение в Visual Studio. Этот раздел посвящен развертыванию в целом, а не только развертыванию на веб-сайтах Azure.
* [Веб-развертывание ASP.NET с помощью Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Серия учебников из 12 частей, в которой рассматривается более полный список задач развертывания, чем в других ресурсах из этого списка. 
* [Развертывание веб-сайта ASP.NET в Azure с помощью Visual Studio 2012 непосредственно из репозитория Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Описание способов развертывания веб-проекта ASP.NET в Visual Studio с использованием подключаемого модуля Git для применения кода к Git и подключения Azure к репозиторию Git.

###<a name="webmatrix"></a>WebMatrix

Для получения информации о выполнении развертывания на веб-сайты Azure из WebMatrix см. следующие ресурсы:

* [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-dotnet-using-webmatrix/). Как создать простой веб-сайт ASP.NET с использованием шаблона WebMatrix и развернуть его на веб-сайте Azure с использованием WebMatrix и веб-развертывания.
* [Создание и развертывание веб-сайта Node.js в Azure с использованием WebMatrix](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Создание и развертывание веб-сайта PHP-MySQL Azure с использованием WebMatrix](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: интеграция Git и развертывание в Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Использование WebMatrix для развертывания из репозитория Git системы управления версиями.

##<a name="ftp"></a>Развертывание с помощью служебной программы FTP

Независимо от типа используемой вами среды IDE при развертывании контента на сайте вы можете использовать [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) для копирования файлов. Вы легко можете создать учетные данные FTP для веб-сайта Azure и использовать их в любом приложении для работы с FTP, включая браузеры, например Internet Explorer, и полнофункциональные бесплатные служебные программы, например [FileZilla](https://filezilla-project.org/).  Веб-сайты Azure также поддерживают более безопасный протокол FTPS. 

Хотя с помощью программ FTP можно легко копировать файлы веб-сайта в Azure, они не обеспечивают автоматически выполнение или координирование связанных с развертыванием задач, таких как развертывание базы данных или изменение строк подключения. Кроме того, многие программы FTP не сравнивают исходные и целевые файлы, чтобы пропускать копирование файлов, которые не были изменены. Для больших сайтов постоянное копирование всех файлов может привести к увеличению времени развертывания даже при наличии незначительных обновлений, поскольку всегда копируются все файлы.

Для получения дополнительных сведений см. следующие ресурсы:

* [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью FTP](/ru-ru/documentation/articles/web-sites-php-mysql-deploy-use-ftp/). 
* [Управление веб-сайтами](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-manage#ftp-credentials). Содержит дополнительные сведения, не включенные в учебник по PHP, о том, как задать учетные данные для FTP. 


##<a name="onpremises"></a>Развертывание из локальной системы управления версиями

При использовании TFS, Git или Mercurial в локальном (не размещенном в облаке) репозитории можно выполнять развертывание прямо из репозитория на веб-сайты Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server - это локальное решение корпорации Майкрософт для управления версиями и совместной работы. Вы можете настроить в TFS выполнение непрерывной доставки на веб-сайт Azure.

Для получения дополнительных сведений см. следующий ресурс:

* [Непрерывная доставка для облачных служб в Azure](/ru-ru/develop/net/common-tasks/continuous-delivery/). Этот документ предназначен для облачной службы Azure, но часть его содержания относится к веб-сайтам.

###<a name="gitmercurial"></a>Локальные репозитории Git или Mercurial

В Azure вы можете ввести URL-адрес любого репозитория, использующего Git или Mercurial, чтобы выполнить развертывание из этого расположения. Можно также принудительно отправить содержимое прямо из локального репозитория Git на веб-сайт Azure. 

Для получения дополнительных сведений см. следующие ресурсы:

* [Публикация из системы управления версиями на веб-сайты Azure](/ru-ru/documentation/articles/web-sites-publish-source-control/). Использование Git для выполнения публикации непосредственно с локального компьютера на веб-сайт Azure (в Azure такой способ публикации называется "локальный репозиторий Git"). Здесь также показано, как включить непрерывное развертывание репозиториев Git из GitHub, CodePlex или BitBucket.
* [Публикация на веб-сайты Azure из любого репозитория Git или Mercurial](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Блог Дэвида Эббо (David Ebbo), который дает пояснения по компоненту "Внешний репозиторий" на веб-сайтах Azure.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Развертывание ДВУХ веб-сайтов в Azure из одного репозитория Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Запись в блоге Скотта Хансельмана (Scott Hanselman).











##<a name="commandline"></a> Использование программ командной строки и API-интерфейса управления Azure REST

Всегда лучше автоматизировать рабочий процесс разработки, но если вы не можете это сделать непосредственно в вашей системе управления версиями, то можно выполнить настройку вручную с использованием программ командной строки. Обычно при этом предполагается использование более чем одного средства или платформы, поскольку развертывание часто включает выполнение функций управления веб-сайтом, а также копирование содержания.

Azure упрощает задачи управления сайтом, которые необходимо выполнить для развертывания, предоставляя API-интерфейс управления REST и несколько платформ, облегчающих работу с API.

###<a name=msbuild></a>MSBuild

If you use the [Visual Studio IDE](#vs) for development, you can use [MSBuild](http://msbuildbook.com/) to automate anything you can do in your IDE. You can configure MSBuild to use either [Web Deploy](#webdeploy) or [FTP/FTPS](#ftp) to copy files. Web Deploy can also automate many other deployment-related tasks, such as deploying databases.

For more information about command-line deployment using MSBuild, see the following resources:

* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tenth in a series of tutorials about deployment to Azure using Visual Studio. Shows how to use the command line to deploy after setting up publish profiles in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Hard-copy book that includes chapters on how to use MSBuild for deployment.

###<a name="ftp2"></a>Сценарии FTP

Вы легко можете создать учетные данные [FTP или FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) для веб-сайта Azure, а затем использовать эти учетные данные с пакетными сценариями FTP.

Для получения дополнительных сведений см. следующий ресурс:

* [Использование пакетных скриптов FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Вы можете выполнять функции развертывания с использованием MSBuild или FTP из [Windows PowerShell](http://msdn.microsoft.com/ru-ru/library/dd835506.aspx). В этом случае вы можете также использовать набор командлетов Windows PowerShell, упрощающих вызов API-интерфейса управления Azure REST.

Для получения дополнительных сведений см. следующий ресурс:

* [Создание реальных облачных приложений в Azure - полная автоматизация](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Глава электронной книги, в которой объясняется, как пример приложения, показанный в электронной книге, использует скрипты Windows PowerShell для создания тестовой среды Azure и выполнения в ней развертывания. Ссылки на дополнительную документацию по Azure PowerShell см. в разделе [Ресурсы](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).

###<a name="api"></a>API управления .NET

Вы можете написать на C# код, выполняющий функции MSBuild или FTP для развертывания. В этом случае вы можете вызвать API-интерфейс управления REST в Azure для выполнения функций управления сайтом.

Для получения дополнительных сведений см. следующий ресурс:

* [Полная автоматизация с использованием библиотек управления Azure и .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Введение в API-интерфейс управления .NET и ссылки на дополнительную документацию.

###<a name="cli"></a>Межплатформенный интерфейс командной строки (xpat-cli)

Для развертывания с помощью FTP можно использовать командную строку на компьютерах Linux или Mac. В этом случае можно также вызвать API-интерфейс управления Azure REST, использующий межплатформенный интерфейс командной строки (xpat cli). На компьютерах Windows также можно использовать интерфейс xpat-cli.

Для получения дополнительных сведений см. следующий ресурс:

* [Программы командной строки](/ru-ru/downloads/#cmd-line-tools). Portal page in WindowsAzure.com for command line tool information.

###<a name="webdeploy"></a>Web Deploy command line

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) is Microsoft software for deployment to IIS that not only provides intelligent file sync features but also can perform or coordinate many other deployment-related tasks that can't be automated when you use FTP. For example, Web Deploy can deploy a new database or database updates along with your website. Web Deploy can also minimize the time required to update an existing site since it can intelligently copy only changed files. Microsoft WebMatrix, Visual Studio, Visual Studio Online, and Team Foundation Server have support for Web Deploy built-in, but you can also use Web Deploy directly from the command line to automate deployment. Web Deploy commands are very powerful but the learning curve can be steep.

For more information, see the following resource:

* [Web Deployment Tool](http://technet.microsoft.com/ru-ru/library/dd568996). Official documentation on the Microsoft TechNet site. Dated but still a good place to start.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Official documentation on the Microsoft IIS.NET site. Also dated but a good place to start.
* [StackOverflow](http://www.stackoverflow.com). The best place to go for more current information about how to use Web Deploy from the command line.
* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild is the build engine used by Visual Studio, and it can also be used from the command line to deploy web applications to Azure Websites. This tutorial is part of a series that is mainly about Visual Studio deployment.

##<a name="nextsteps"></a>Next Steps

In some scenarios you might want to be able to easily switch back and forth between a staging and a production version of your website. You can do this with the Azure Websites staged deployment feature. For more information, see [Staged Deployment on Microsoft Azure Web Sites](/ru-ru/documentation/articles/web-sites-staged-publishing/).

Наличие резервного копирования и плана восстановления является важной частью рабочего процесса развертывания. Информацию о функции резервного копирования и восстановления веб-сайтов Azure см. в разделе [Резервные копии веб-сайтов Azure](/ru-ru/documentation/articles/web-sites-backup/).  

Дополнительную информацию по теме развертывания см. в разделе "Развертывание" [документации по веб-сайтам Azure](/ru-ru/documentation/services/web-sites/).

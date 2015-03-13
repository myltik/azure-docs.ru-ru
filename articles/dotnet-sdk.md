<properties 
	pageTitle="Информация о пакете Azure SDK для NET" 
	description="Узнайте, что включено в пакет SDK для Azure для .NET." 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="tdykstra"/>

# Что такое "пакет Azure SDK для .NET"?

Пакет Azure SDK для .NET представляет собой набор средств для Visual Studio, средств командной строки, исполняемых двоичных файлов и клиентских библиотек, помогающих при разработке, тестировании и развертывании приложений, запускаемых в Azure. Эта статья содержит подробные сведения о том, что вы получите после установки пакета SDK. Вы можете скачать пакет SDK со [страницы загрузок Azure](/ru-ru/downloads/). 

Пакет Azure SDK для .NET также имеет в составе клиентские библиотеки для использования служб Azure. Эти библиотеки устанавливаются отдельно при использовании [NuGet](http://go.microsoft.com/fwlink/?LinkId=510472).

## Оглавление

- [Что входит в состав пакета Azure SDK для .NET?](#included)
- [Что не входит в состав пакета Azure SDK для .NET?](#notincluded)
- [Часто задаваемые вопросы](#faq)
- [Ресурсы](#resources)

##<a id="included"></a>Что входит в состав пакета Azure SDK для .NET?

Пакет Azure SDK для .NET устанавливает следующие программы:

- [Visual Studio Express для Web](#vwd)
- [Microsoft ASP.NET и пакет Web Tools для Visual Studio](#wte)
- [Пакет Microsoft Azure Tools для Microsoft Visual Studio](#tools)
- [Средства создания Microsoft Azure](#auth)
- [Эмулятор Microsoft Azure](#emulator)
- [Эмулятор хранилища Microsoft Azure](#stgemulator)
- [Средства хранилища Microsoft Azure](#stgtools)
- [Библиотеки Microsoft Azure для .NET](#libraries)
- [Дополнение LightSwitch Azure Publishing для Visual Studio](#ls)

###<a id="vwd"></a>Visual Studio Express для Web

Если на компьютере не установлен Visual Studio, пакет SDK установит [Visual Studio Express для Web](http://www.visualstudio.com/ru-ru/products/visual-studio-express-vs.aspx). 
 
###<a id="wte"></a>Microsoft ASP.NET и пакет Web Tools для Visual Studio

Это позволит вам работать с веб-сайтами Azure:

* [Публикация веб-проектов на веб-сайтах Azure](../web-sites-dotnet-get-started/).
* [Публикация проектов консольных приложений в веб-заданиях Azure](../websites-dotnet-deploy-webjobs/).
* [Создание ресурсов веб-сайтов Azure и баз данных SQL при создании нового веб-проекта или его публикации](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
* [Создание сценариев развертывания PowerShell при создании новых веб-сайтов](http://msdn.microsoft.com/library/dn642480.aspx).
* [Управление и устранение неполадок веб-сайтов Azure в обозревателе серверов](../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement).
* [Удаленный запуск веб-сайтов и веб-заданий в режиме отладки](../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug). 

>[WACOM.NOTE] Чтобы пользоваться этими возможностями, не нужно устанавливать пакет Azure SDK для .NET, так как они включены в состав обновлений Visual Studio. 

###<a id="tools"></a>Пакет Microsoft Azure Tools для Microsoft Visual Studio

Позволяет работать с облачными службами и виртуальными машинами Azure:

* [Создание, открытие и публикация проектов облачных служб](../cloud-services-dotnet-get-started/).
* [Создание пакетов развертывания для облачных служб](http://msdn.microsoft.com/library/ff683672.aspx).
* [Создание виртуальных машин Azure при построении новых веб-проектов](../virtual-machines-dotnet-create-visual-studio-powershell/).
* [Создание сценариев PowerShell при создании новых виртуальных машин](http://msdn.microsoft.com/library/dn642480.aspx).
* [Просмотр настроек проекта облачных служб в окне свойств проекта Visual Studio и управление ими;](http://msdn.microsoft.com/library/ee405486.aspx).
* Просмотр [облачных служб](http://msdn.microsoft.com/library/ff683675.aspx), [виртуальных машин](http://msdn.microsoft.com/library/jj131259.aspx)и [служебной шины](http://msdn.microsoft.com/library/jj149828.aspx) в обозревателе сервера и управление ими. 
* [Удаленный запуск в режиме отладки облачных служб и виртуальных машин](http://msdn.microsoft.com/library/ff683670.aspx).

###<a id="auth"></a>Средства создания Microsoft Azure

В их состав входят:

* [Средство командной строки CSPack](http://msdn.microsoft.com/library/gg432988.aspx) для создания пакетов развертывания.
* [Средство командной строки CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) для шифрования паролей, используемых для доступа к экземплярам ролей облачных служб путем подключения к удаленному рабочему столу.
* Исполнимые двоичные файлы, которые требуются проектам облачных служб для коммуникации со средой выполнения и диагностики. Эти файлы недоступны в пакетах NuGet.

###<a id="emulator"></a>Эмулятор Microsoft Azure

[Эмулятор Azure](http://msdn.microsoft.com/library/dn339018.aspx) имитирует среду облачной службы, чтобы вы могли тестировать проекты облачных служб на локальном компьютере перед их развертыванием в Azure.

###<a id="stgemulator"></a>Эмулятор хранилища Microsoft Azure

[Эмулятор хранения Azure](http://msdn.microsoft.com/library/hh403989.aspx) использует экземпляр SQL Server и локальную файловую систему для эмуляции хранилища Azure (запросы, таблицы, двоичные объекты), что позволяет проводить тестирование локально. 

###<a id="stgtools"></a>Средства хранилища Microsoft Azure

Будет установлено [AzCopy](http://aka.ms/AzCopy), средство командной строки, которое можно использовать для переноса данных в учетную запись хранения Azure, а также их извлечения.

###<a id="libraries"></a>Библиотеки Microsoft Azure для .NET

В их состав входят:

* Пакеты NuGet для хранилища Azure, сервисной шины и кэширования, которые сохраняются на компьютере, чтобы в Visual Studio можно было создать новые проекты облачных служб без доступа в Интернет.
* Подключаемый модуль для Visual Studio, позволяющий запускать проекты [кэша роли](http://msdn.microsoft.com/library/dn386103.aspx) локально в Visual Studio. 

###<a id="ls"></a>Дополнение LightSwitch Azure Publishing для Visual Studio

Оно позволяет [публиковать приложения LightSwitch на веб-сайтах Azure](http://msdn.microsoft.com/library/jj131261.aspx). Дополнение LightSwitch входит в состав обновлений Visual Studio, а также в состав пакета Azure SDK для .NET. Установленный пакет SDK является гарантией того, что у вас последняя версия дополнения. 

##<a id="notincluded"></a>Что не входит в состав пакета Azure SDK для .NET?

Есть некоторые компоненты, которые не включены в установочный пакет SDK, но могут вам понадобиться при разработке для Azure. Наиболее важные из них:

* [Клиентские библиотеки](http://go.microsoft.com/fwlink/?LinkId=510472). 

	В пакет SDK для Azure включены клиентские библиотеки для использования служб Azure, но не все они устанавливаются при установке SDK. Если приложению необходима клиентская библиотека, которая не устанавливается с SDK, ее можно получить на сайте [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Если приложение использует клиентскую библиотеку, которая устанавливается с SDK, ее лучше всего обновить текущей версией с сайта NuGet.org.

  	**Локальные копии клиентских библиотек**Пакет Azure SDK для .NET копирует на компьютер пакеты NuGet для некоторых клиентских библиотек Azure, таких как Хранилище, Service Bus и Кэширование. Эти клиентские библиотеки автоматически добавляются в новые проекты облачных служб, поэтому локальные пакеты NuGet позволяют Visual Studio создавать проекты даже в случае отсутствия подключения к Интернету. Клиентские библиотеки, как правило, обновляются чаще, чем выпускаются новые версии пакета SDK, поэтому на сайте NuGet.org зачастую имеются более новые версии клиентских библиотек, чем те, которые включены в пакет SDK. 

	**Шаблоны проектов, включающие клиентские библиотеки** Только в шаблоны проектов [облачной службы Azure](../cloud-services-dotnet-get-started/) и [мобильной службы Azure](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/) автоматически включены некоторые клиентские библиотеки. Чтобы получить другие библиотеки или шаблоны, установите необходимые [пакеты клиентских библиотек NuGet](http://go.microsoft.com/fwlink/?LinkId=510472).

* [Azure PowerShell](../install-configure-powershell/). 

	Azure PowerShell позволяет [автоматизировать создание и развертывание среды Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* [Шаблоны проектов мобильных служб Azure](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/).

	Шаблоны проектов мобильных служб Azure доступны только в Visual Studio 2013 с пакетом обновлений 2 и в более поздних версиях. При этом недоступны в Visual Studio 2012 и более ранних версиях, а также в Visual Studio 2013 с пакетом обновлений 1 или более ранних версиях, даже после установки Azure SDK для .NET.

##<a id="faq"></a>Часто задаваемые вопросы

- [Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?](#azinvs)
- [Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?](#clientlib)
- [Где можно найти предыдущие версии пакета Azure SDK для .NET?](#olderversions)
- [Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?](#lifecycle)
- [С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?](#guestos)

###<a id="azinvs"></a>Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?

Чтобы выполнять разработку для Azure с использованием современных средств, рекомендуется установка пакета SDK. Если вы не хотите его устанавливать, можно сделать это при выполнении следующих условий:

* у вас установлено последнее [обновление Visual Studio](http://www.visualstudio.com/ru-ru/downloads/download-visual-studio-vs#DownloadFamilies_5).
* вы разрабатываете только для веб-сайтов или мобильных служб Azure, а не для облачных служб или виртуальных машин;
* приложение не использует службу хранилища либо оно ее использует, но у вас нет необходимости в эмуляторе хранилища или средстве AzCopy.

###<a id="clientlib"></a>Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?

Пакет SDK устанавливает только клиентские библиотеки, поэтому можно создать проект облачной службы, даже если вы не подключены к Интернету. Самые последние версии клиентских библиотек доступны в виде пакетов NuGet на сайте [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Подробнее об этом см. в статье [Что не входит в пакет Azure SDK для .NET](#notincluded) ранее в этом документе.

###<a id="olderversions"></a>Где можно найти предыдущие версии пакета Azure SDK для .NET?

Предыдущие версии пакета размещены на странице загрузки пакета [Azure SDK для .NET](/ru-ru/downloads/archive-net-downloads/). 

###<a id="lifecycle"></a>Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?

См. [Политика поддержки жизненного цикла облачных служб Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?

См. [Таблицу совместимости версий гостевых ОС и Azure SDK](http://msdn.microsoft.com/library/ee924680.aspx).



##<a id="resources"></a>Ресурсы

Чтобы загрузить пакет Azure SDK для .NET или клиентскую библиотеку, см. [страницу загрузок Azure](/ru-ru/downloads/).

Исходный код пакета Azure SDK для .NET, включая клиентские библиотеки, размещен по адресу [GitHub.com/Azure](https://github.com/azure/).

Справочную документацию клиентских библиотек Azure см. в разделе [Справочник по Azure .NET](/ru-ru/develop/net/reference/). 

<!--HONumber=46--> 

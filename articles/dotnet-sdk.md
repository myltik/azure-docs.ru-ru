<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Что такое «пакет Azure SDK для .NET»?

Пакет Azure SDK для .NET представляет собой набор средств для Visual Studio, средств командной строки, библиотек времени выполнения и клиентских библиотек для оказания помощи при разработке, тестировании и развертывании приложений, выполняющихся в Azure. Эта статья содержит подробные сведения о том, что вы получите после установки пакета SDK. Вы можете установить пакет SDK, загрузив его со страницы [Загрузки Azure][Загрузки Azure].

Пакет Azure SDK для .NET также имеет в составе клиентские библиотеки для использования служб Azure. Эти библиотеки устанавливаются отдельно с использованием [NuGet][NuGet].

## Оглавление

-   [Что входит в состав пакета Azure SDK для .NET?][Что входит в состав пакета Azure SDK для .NET?]
-   [Что не входит в состав пакета Azure SDK для .NET?][Что не входит в состав пакета Azure SDK для .NET?]
-   [Часто задаваемые вопросы][Часто задаваемые вопросы]
-   [Ресурсы][Ресурсы]

## <span id="included"></span></a>Что входит в состав пакета Azure SDK для .NET?

Пакет Azure SDK для .NET устанавливает следующие программы:

-   [Visual Studio Express для Web][Visual Studio Express для Web]
-   [Microsoft ASP.NET и пакет Web Tools для Visual Studio][Microsoft ASP.NET и пакет Web Tools для Visual Studio]
-   [Пакет Microsoft Azure Tools для Microsoft Visual Studio][Пакет Microsoft Azure Tools для Microsoft Visual Studio]
-   [Средства создания Microsoft Azure][Средства создания Microsoft Azure]
-   [Эмулятор Microsoft Azure][Эмулятор Microsoft Azure]
-   [Эмулятор хранилища Microsoft Azure][Эмулятор хранилища Microsoft Azure]
-   [Средства хранилища Microsoft Azure][Средства хранилища Microsoft Azure]
-   [Библиотеки Microsoft Azure для .NET][Библиотеки Microsoft Azure для .NET]
-   [Дополнение LightSwitch Azure Publishing для Visual Studio][Дополнение LightSwitch Azure Publishing для Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express для Web

Если на компьютере не установлен Visual Studio, пакет SDK установит [Visual Studio Express для Web][1].

### <span id="wte"></span></a>Microsoft ASP.NET и пакет Web Tools для Visual Studio

Это позволит вам работать с веб-сайтами Azure:

-   [Публиковать веб-проекты на веб-сайтах Azure][Публиковать веб-проекты на веб-сайтах Azure].
-   [Публикация проектов консольных приложений в веб-заданиях Azure][Публикация проектов консольных приложений в веб-заданиях Azure].
-   [Создание ресурсов веб-сайтов Azure Website и баз данных SQL при создании нового веб-проекта или при его публикации][Создание ресурсов веб-сайтов Azure Website и баз данных SQL при создании нового веб-проекта или при его публикации].
-   [Создание сценариев развертывания PowerShell при создании новых веб-сайтов][Создание сценариев развертывания PowerShell при создании новых веб-сайтов].
-   [Управление и устранение неполадок веб-сайтов Azure в обозревателе серверов][Управление и устранение неполадок веб-сайтов Azure в обозревателе серверов].
-   [Удаленный запуск веб-сайтов и веб-заданий в режиме отладки][Удаленный запуск веб-сайтов и веб-заданий в режиме отладки].

> [WACOM.NOTE] Вам не нужно устанавливать пакет Azure SDK для .NET для того, чтобы использовать эти возможности; они также входят в состав обновлений Visual Studio.

### <span id="tools"></span></a>Пакет Microsoft Azure Tools для Microsoft Visual Studio

Позволяет работать с облачными службами и виртуальными машинами Azure:

-   [создавать, открывать и публиковать проекты облачных служб][создавать, открывать и публиковать проекты облачных служб];
-   [создавать пакеты развертывания для проектов облачных служб][создавать пакеты развертывания для проектов облачных служб];
-   [создавать виртуальные машины Azure при создании новых веб-проектов][создавать виртуальные машины Azure при создании новых веб-проектов];
-   [создавать сценарии PowerShell при создании новых виртуальных машин][Создание сценариев развертывания PowerShell при создании новых веб-сайтов];
-   [просматривать настройки проекта облачных служб в окне свойств проекта Visual Studio и управлять ими;][просматривать настройки проекта облачных служб в окне свойств проекта Visual Studio и управлять ими;]
-   просматривать [облачные службы][облачные службы], [виртуальные машины][виртуальные машины] и [сервисную шину][сервисную шину] в обозревателе серверов и управлять ими;
-   [удаленно запускать в режиме отладки облачные службы и виртуальные машины][удаленно запускать в режиме отладки облачные службы и виртуальные машины].

### <span id="auth"></span></a>Средства создания Microsoft Azure

В их состав входят:

-   [Средство командной строки CSPack][Средство командной строки CSPack] для создания пакетов развертывания.
-   [Средство командной строки CSEncrypt][Средство командной строки CSEncrypt] для шифрования паролей, используемых для доступа к экземплярам ролей облачных служб через подключение к удаленному рабочему столу.
-   Исполнимые двоичные файлы, которые требуются проектам облачных служб для коммуникации со средой выполнения и диагностики. Эти файлы недоступны в пакетах NuGet.

### <span id="emulator"></span></a>Эмулятор Microsoft Azure

[Эмулятор Azure][Эмулятор Azure] имитирует среду облачной службы, чтобы вы могли тестировать проекты облачных служб на локальном компьютере перед их развертыванием в Azure.

### <span id="stgemulator"></span></a>Эмулятор хранилища Microsoft Azure

[Эмулятор хранилища Microsoft Azure][2] использует экземпляр SQL Server и локальную файловую систему для эмуляции хранилища Azure (запросы, таблицы, двоичные объекты), что позволяет проводить тестирование локально.

### <span id="stgtools"></span></a>Средства хранилища Microsoft Azure

Будет установлено [AzCopy][AzCopy], средство командной строки, которое можно использовать для переноса данных в учетную запись хранения Azure и из нее.

### <span id="libraries"></span></a>Библиотеки Microsoft Azure для .NET

В их состав входят:

-   Пакеты NuGet для хранилища Azure, сервисной шины и кэширования, которые сохраняются на компьютере, чтобы в Visual Studio можно было создать новые проекты облачных служб без доступа в Интернет.
-   Дополнение для Visual Studio, позволяющее запускать проекты [службы кэша][службы кэша] локально в Visual Studio.

### <span id="ls"></span></a>Дополнение LightSwitch Azure Publishing для Visual Studio

Оно позволяет [опубликовывать приложения LightSwitch на веб-сайтах Azure][опубликовывать приложения LightSwitch на веб-сайтах Azure]. Дополнение LightSwitch входит в состав обновлений Visual Studio, а также в состав пакета Azure SDK для .NET. Установка пакета SDK позволит быть уверенным, что у вас будет последняя версия дополнения.

## <span id="notincluded"></span></a>Что не входит в состав пакета Azure SDK для .NET?

Некоторые вещи, которые могут понадобиться при разработке для Azure, не будут включены в установку пакета SDK. Наиболее важные из них:

-   [Клиентские библиотеки][NuGet]

    Пакет SDK для Azure включает клиентские библиотеки для использования служб Azure, но не все они устанавливаются при установке SDK. Если приложению необходима клиентская библиотека, которая не устанавливается с SDK, ее можно получить на сайте [NuGet.org][NuGet]. Если приложение использует клиентскую библиотеку, которая устанавливается с SDK, ее лучше всего обновить текущей версией с сайта NuGet.org.

    **Локальные копии клиентских библиотек**Пакет Azure SDK для .NET копирует на компьютер пакеты NuGet для некоторых клиентских библиотек Azure, таких как Хранилище, Service Bus и Кэширование. Эти клиентские библиотеки автоматически добавляются в новые проекты облачных служб, поэтому локальные пакеты NuGet позволяют Visual Studio создавать проекты даже в случае отсутствия подключения к Интернету. В общем случае клиентские библиотеки обновляются чаще, чем выпускаются новые версии пакета SDK, поэтому клиентские библиотеки на сайте NuGet.org зачастую имеют более новые версии, чем те, которые входят в состав SDK.

    **Шаблоны проектов, включающие клиентские библиотекиТолько шаблоны проектов** [Облачная служба Azure][создавать, открывать и публиковать проекты облачных служб] и [Мобильная служба Azure][Мобильная служба Azure] автоматически включают некоторые клиентские библиотеки. Для того чтобы получить другие библиотеки или шаблоны, установите необходимые [пакеты клиентских библиотек NuGet][NuGet].

-   [Azure PowerShell][Azure PowerShell].

    Azure PowerShell позволяет [автоматизировать создание и развертывание среды Azure][автоматизировать создание и развертывание среды Azure].

-   [Шаблоны проектов мобильных служб Azure][Мобильная служба Azure]

    Шаблоны проектов мобильных служб Azure доступны только в Visual Studio 2013 с обновлением 2 и более поздних версиях. Они недоступны в Visual Studio 2012 и более ранних версиях, а также в Visual Studio 2013 с обновлением 1 или более ранних версиях, даже после установки Azure SDK для .NET.

## <span id="faq"></span></a>Часто задаваемые вопросы

-   [Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?][Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?]
-   [Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?][Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?]
-   [Где можно найти предыдущие версии пакета Azure SDK для .NET?][Где можно найти предыдущие версии пакета Azure SDK для .NET?]
-   [Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?][Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?]
-   [С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?][С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?]

### <span id="azinvs"></span></a>Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?

Рекомендуется устанавливать пакет SDK чтобы вести разработку для Azure с использованием современных средств. Если вы не хотите устанавливать пакет SDK, вы можете сделать это, если выполняются следующие условия:

-   установлено последнее [обновление для Visual Studio][обновление для Visual Studio];
-   вы разрабатываете только для веб-сайтов или мобильных служб Azure, а не для облачных служб или виртуальных машин;
-   приложение не использует службу хранилища либо оно ее использует, но у вас нет необходимости в эмуляторе хранилища или средстве AzCopy.

### <span id="clientlib"></span></a>Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?

Пакет SDK устанавливает только клиентские библиотеки, поэтому можно создать проект облачной службы, даже если вы не подключены к Интернету. Самые последние версии клиентских библиотек доступны в виде пакетов NuGet на сайте [NuGet.org][NuGet]. Дополнительные сведения см. в разделе [Что не входит в состав пакета Azure SDK для .NET?][Что не входит в состав пакета Azure SDK для .NET?] ранее в этом же документе.

### <span id="olderversions"></span></a>Где можно найти предыдущие версии пакета Azure SDK для .NET?

Предыдущие версии пакета размещены на странице загрузки [пакета Azure SDK для .NET][пакета Azure SDK для .NET].

### <span id="lifecycle"></span></a>Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?

См. [Политика поддержки жизненного цикла облачных служб Microsoft Azure][Политика поддержки жизненного цикла облачных служб Microsoft Azure].

### <span id="guestos"></span></a>С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?

См. [Таблицу совместимости версий гостевых ОС и Azure SDK][Таблицу совместимости версий гостевых ОС и Azure SDK].

## <span id="resources"></span></a>Ресурсы

Чтобы загрузить пакет Azure SDK для .NET или клиентскую библиотеку, см. [страницу загрузок Azure][Загрузки Azure].

Исходный код пакета Azure SDK для .NET, включая клиентские библиотеки, размещен по адресу [GitHub.com/Azure][GitHub.com/Azure].

Справочная документация клиентских библиотек Azure см. в разделе [Справочник по Azure для .NET][Справочник по Azure для .NET].

  [Загрузки Azure]: /ru-ru/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [Что входит в состав пакета Azure SDK для .NET?]: #included
  [Что не входит в состав пакета Azure SDK для .NET?]: #notincluded
  [Часто задаваемые вопросы]: #faq
  [Ресурсы]: #resources
  [Visual Studio Express для Web]: #vwd
  [Microsoft ASP.NET и пакет Web Tools для Visual Studio]: #wte
  [Пакет Microsoft Azure Tools для Microsoft Visual Studio]: #tools
  [Средства создания Microsoft Azure]: #auth
  [Эмулятор Microsoft Azure]: #emulator
  [Эмулятор хранилища Microsoft Azure]: #stgemulator
  [Средства хранилища Microsoft Azure]: #stgtools
  [Библиотеки Microsoft Azure для .NET]: #libraries
  [Дополнение LightSwitch Azure Publishing для Visual Studio]: #ls
  [1]: http://www.visualstudio.com/ru-ru/products/visual-studio-express-vs.aspx
  [Публиковать веб-проекты на веб-сайтах Azure]: ../web-sites-dotnet-get-started/
  [Публикация проектов консольных приложений в веб-заданиях Azure]: ../websites-dotnet-deploy-webjobs/
  [Создание ресурсов веб-сайтов Azure Website и баз данных SQL при создании нового веб-проекта или при его публикации]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Создание сценариев развертывания PowerShell при создании новых веб-сайтов]: http://msdn.microsoft.com/ru-ru/library/dn642480.aspx
  [Управление и устранение неполадок веб-сайтов Azure в обозревателе серверов]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Удаленный запуск веб-сайтов и веб-заданий в режиме отладки]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [создавать, открывать и публиковать проекты облачных служб]: ../cloud-services-dotnet-get-started/
  [создавать пакеты развертывания для проектов облачных служб]: http://msdn.microsoft.com/ru-ru/library/ff683672.aspx
  [создавать виртуальные машины Azure при создании новых веб-проектов]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [просматривать настройки проекта облачных служб в окне свойств проекта Visual Studio и управлять ими;]: http://msdn.microsoft.com/ru-ru/library/ee405486.aspx
  [облачные службы]: http://msdn.microsoft.com/ru-ru/library/ff683675.aspx
  [виртуальные машины]: http://msdn.microsoft.com/ru-ru/library/jj131259.aspx
  [сервисную шину]: http://msdn.microsoft.com/ru-ru/library/jj149828.aspx
  [удаленно запускать в режиме отладки облачные службы и виртуальные машины]: http://msdn.microsoft.com/ru-ru/library/ff683670.aspx
  [Средство командной строки CSPack]: http://msdn.microsoft.com/ru-ru/library/gg432988.aspx
  [Средство командной строки CSEncrypt]: http://msdn.microsoft.com/ru-ru/library/hh404001.aspx
  [Эмулятор Azure]: http://msdn.microsoft.com/ru-ru/library/dn339018.aspx
  [2]: http://msdn.microsoft.com/ru-ru/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [службы кэша]: http://msdn.microsoft.com/ru-ru/library/dn386103.aspx
  [опубликовывать приложения LightSwitch на веб-сайтах Azure]: http://msdn.microsoft.com/ru-ru/library/jj131261.aspx
  [Мобильная служба Azure]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [автоматизировать создание и развертывание среды Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Многие компоненты Azure уже входят в состав Visual Studio. Нужно ли мне установить пакет Azure SDK для .NET?]: #azinvs
  [Мне нужна клиентская библиотека. Нужно ли устанавливать пакет Azure SDK для .NET, чтобы получить ее?]: #clientlib
  [Где можно найти предыдущие версии пакета Azure SDK для .NET?]: #olderversions
  [Какова политика поддержки жизненного цикла пакета Azure SDK для .NET?]: #lifecycle
  [С какими версиями гостевых ОС совместим пакет Azure SDK для .NET?]: #guestos
  [обновление для Visual Studio]: http://www.visualstudio.com/ru-ru/downloads/download-visual-studio-vs#DownloadFamilies_5
  [пакета Azure SDK для .NET]: /ru-ru/downloads/archive-net-downloads/
  [Политика поддержки жизненного цикла облачных служб Microsoft Azure]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Таблицу совместимости версий гостевых ОС и Azure SDK]: http://msdn.microsoft.com/ru-ru/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Справочник по Azure для .NET]: /ru-ru/develop/net/reference/

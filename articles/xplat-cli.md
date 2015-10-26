<properties
	pageTitle="Интерфейс командной строки Azure для Mac, Linux и Windows"
	description="Установка и настройка интерфейса командной строки Azure для Mac, Linux и Windows для управления службами Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="squillace"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Установка и настройка CLI Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

Интерфейс командной строки Azure представляет собой набор межплатформенных команд с открытым кодом для работы с платформой Azure. CLI Azure предоставляет практически те же функции, которые содержатся на портале управления Azure, например возможность управлять веб-сайтами, виртуальными машинами, мобильными службами, базой данных SQL и другими службами, включенными в платформу Azure.

Интерфейс CLI Azure написан на языке JavaScript, и для его использования необходим файл Node.js. Он реализован с помощью Azure SDK для Node.js и выпущен по лицензии Apache 2.0. Репозиторий проекта расположен по адресу [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

В этом документе описывается, как установить и настроить CLI Azure для Mac, Linux и Windows, а также использовать его для выполнения основных задач с платформой Azure.

<a id="install"></a>
## Установка Azure CLI

Дополнительные сведения о шагах установки для CLI Azure см. на странице [Установка CLI Azure](xplat-cli-install.md).


<a id="configure"></a>
## Подключение к подписке Azure

Хотя некоторые команды CLI Azure будут работать без подписки Azure, большинство команд требуют подписки. Чтобы узнать, как настроить CLI Azure для работы с подпиской, см. раздел [Подключение к подписке Azure из CLI Azure](xplat-cli-connect.md).


<a id="use"></a>
## Использование CLI Azure

Интерфейс командной строки CLI Azure становится доступен после выполнения команды `azure`. Чтобы просмотреть список доступных команд, используйте команду `azure` без параметров. Вы должны видеть справочные сведения, аналогичные приведенным ниже:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

Перечисленные выше команды верхнего уровня содержат команды для работы с определенной областью Azure. Так, команда `azure account` содержит команды, которые связаны с подпиской Azure, например ранее использованные параметры `download` и `import`. Дополнительные сведения о доступных командах и параметрах см. в разделе [Использование CLI Azure для Windows, Linux и Mac].

Большинство команд имеют формат `azure <command> <operation> [parameters]` и выполняют операции, связанные со службой или объектом, например конфигурацией вашей учетной записи. Другие команды предоставляют вложенные команды и используют формат `azure <command> <subcommand> <operation> [parameters]`. Ниже приведены примеры команд, которые работают с конфигурацией учетной записи.

* Чтобы просмотреть подписки, которые вы импортировали, используйте:

		azure account list

* Если у вас есть импортированные подписки, используйте следующую команду для определения одной из них как подписки по умолчанию.

		azure account set <subscription>

Параметр `--help` или `-h` может использоваться для просмотра справки по определенным командам. Кроме того, формат `azure help [command] [options]` можно использовать для возвращения той же самой информации. Например, все следующие команды возвращают одинаковые сведения:

	azure account set --help

	azure account set -h

	azure help account set

Если вы сомневаетесь в том, какие параметры необходимы команде, обратитесь к справке, используя `--help`, `-h` или `azure help [command]`.

### Настройка режима конфигурации

Интерфейс CLI Azure позволяет вам выполнять операции по управлению отдельными _ресурсами_, которые являются управляемыми пользователем объектами, такими как сервер баз данных, база данных или веб-сайт. Это режим работы CLI Azure по умолчанию, который также называется** Управление службами Azure**. Однако управление сложным решением, включающим несколько ресурсов, рекомендуется осуществлять как единым объектом.

Чтобы обеспечить управление _группой ресурсов_ Azure как единым логическим объектом, была реализована предварительная версия **диспетчера ресурсов**.

>[AZURE.NOTE]Диспетчер ресурсов находится на стадии предварительной версии и не обеспечивает того уровня управления, который доступен в компоненте управления службами Azure.

Для переключения между этими режимами управления в CLI Azure используется команда `azure config mode`.

По умолчанию в CLI Azure устанавливается режим управления службами Azure. Чтобы переключиться в режим диспетчера ресурсов, выполните следующую команду:

	azure config mode arm

Чтобы вернуться в режим управления службами Azure, используйте следующую команду:

	azure config mode asm

>[AZURE.NOTE]Режим диспетчера ресурсов и режим управления службами Azure являются взаимоисключающими. То есть ресурсами, созданными в одном из режимов, нельзя управлять из другого режима.

Дополнительные сведения о работе с диспетчером ресурсов с помощью CLI Azure см. в разделе [Использование CLI Azure совместно с диспетчером ресурсов][cliarm].

### Работа со службами в режиме управления службами Azure

Интерфейс CLI Azure позволяет без труда управлять службами Azure. В этом примере вы узнаете, как использовать интерфейс CLI Azure для управления веб-сайтом Azure.

1. Используйте следующую команду для создания нового веб-сайта Azure. Замените **mywebsite** на уникальное имя.

		azure site create mywebsite

	Вам будет предложено указать регион, в котором будет создан веб-сайт. Выберите регион, который расположен географически недалеко от вас. После выполнения этой команды станет доступен веб-сайт по адресу http://mywebsite.azurewebsites.net (замените **mywebsite** на указанное вами имя.)

	> [AZURE.NOTE]При использовании Git для системы управления версиями проекта можно указать параметр `--git` для создания репозитория Git в Azure для этого веб-сайта. Это также приведет к инициализации репозитория GIt в каталоге, где будет запускаться команда, если такой репозиторий еще не существует. Это также приведет к созданию удаленного Git с именем __azure__, который можно использовать для передачи развертываний на веб-сайт Azure с помощью команды `git push azure master`.

	> [AZURE.NOTE]Если появляется сообщение о том, что site не является командой azure, интерфейс CLI Azure, скорее всего, находится в режиме группы ресурсов. Чтобы вернуться в режим ресурсов, используйте команду `azure config mode asm`.

2. Используйте следующую команду для отображения веб-сайтов, связанных с вашей подпиской.

		azure site list

	Этот список должен содержать сайт, созданный на предыдущем этапе.

2. Воспользуйтесь следующей командой для остановки веб-сайта. Замените **mywebsite** на уникальное имя сайта.

		azure site stop mywebsite

	После выполнения команды можно обновить браузер для проверки остановки сайта.

3. Используйте следующую команду для запуска веб-сайта. Замените **mywebsite** на уникальное имя сайта.

		azure site start mywebsite

	После выполнения команды можно обновить браузер для проверки запуска сайта.

4. Используйте следующую команду для удаления веб-сайта. Замените **mywebsite** на уникальное имя сайта.

		azure site delete mywebsite

	После выполнения команды используйте команду `azure site list`, чтобы убедиться, что веб-сайт больше не существует.

<a id="script"></a>
## Создание скрипта интерфейса командной строки Azure для Mac, Linux и Windows.

При использовании интерфейса CLI Azure для ручного ввода команд можно также создавать сложные процессы автоматизации благодаря возможностям интерпретатора и других средств командной строки, доступных в системе. Например, следующая команда остановит работу всех запущенных веб-сайтов Azure:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

В этом примере список веб-сайтов передается команде `grep`, которая проверяет каждую строку на наличие слова Running. Все строки, соответствующие этому критерию, передаются в команду `awk`, которая вызывает команду `azure site stop` и использует второй полученный столбец (имя работающего сайта) в качестве имени останавливаемого сайта.

Хотя этот пример демонстрирует, как можно связывать вместе команды, можно также создать более комплексные скрипты с использованием компонентов скриптов, предоставленных интерпретатором командной строки. Различные интерпретаторы командной строки имеют разные возможности создания скриптов и разный синтаксис. Bash, вероятно, является наиболее широко используемым интерпретатором командной строки в системах на основе UNIX, включая OS X и Linux.

Сведения о создании скриптов с помощью Bash см. в [расширенном руководстве по созданию скриптов Bash][advanced-bash].

Общие сведения о скриптах в системах OS X или Linux см. в разделе [Скрипты оболочки][script].

Сведения о составлении скриптов в системах на основе Windows с помощью пакетных файлов см. в разделе [Справочник по командной строке от А до Я][batch].

### Интерпретация результатов

При создании скриптов, как правило, требуется записать выходные данные команды и либо передать их в другую команду, либо выполнить операцию с этими выходными данными, например, для проверки наличия в них определенного значения. Интерфейс CLI Azure формирует выход на STDOUT и STDERR. Каждая строка имеет префикс `info:` в виде для информационных сообщений о состоянии или `data:` для данных, возвращенных о службе. Однако вы можете указать CLI Azure возвращать более подробную информацию, используя параметр `--verbose` или `-v`. Это приведет к возвращению дополнительной информации с префиксом строкой `verbose:`

Например, команда `azure site list` вернет следующие выходные данные:

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Если указан параметр `--verbose` или `-v`, возвращается информация, аналогичная приведенной ниже:

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Обратите внимание, что по префиксу информация `verbose:` отображается в формате JSON. Вы можете использовать параметр `--json` для возврата информации в формате JSON при работе в служебных программах, которые его поддерживают, например [jsawk](https://github.com/micha/jsawk) или [jq](http://stedolan.github.io/jq/). Например:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

Приведенная выше команда извлекает список веб-сайтов в формате JSON, а затем использует jsawk для получения имен сайтов и, наконец, использует xargs для выполнения команды удаления каждого сайта, передавая имя сайта в качестве параметра.

>[AZURE.NOTE]Параметр `--json` блокирует создание сведений о состоянии или данных (строки, которым предшествует `info:` и `data:`). Например, если параметр `--json` используется вместе с `azure site create`, выходные данные не возвращаются, так как эта команда не возвращает данные, отличные от `info:`.

### Работа с ошибками

Хотя интерфейс CLI Azure не регистрирует сведения об ошибках в STDERR, дополнительные сведения об ошибках могут регистрироваться в файле **azure.err** в каталоге, из которого выполнялся скрипт. Если в этот файл записываются сведения, в STDOUT будет выведено следующее:

	info:    Error information has been recorded to azure.err

### Состояние выхода

Некоторые команды CLI Azure не возвращают состояние выхода ненулевой длины, если отсутствуют требуемые параметры. Вместо этого они будут запрашивать ввод данных пользователем. Например, если при использовании команды `azure site create` для создания нового веб-сайта не указано имя сайта или параметр `--location`, появится запрос на ввод этих значений.

При написании скрипта, который основывается на состоянии выхода, убедитесь, что используемые команды CLI Azure не запрашивают ввод пользовательских данных.

<a id="additional-resources"></a>

## Дополнительные ресурсы

* [Управление службами с помощью интерфейса командной строки Azure][Using the Azure CLI]

* [Использование интерфейса командной строки Azure с диспетчером ресурсов][cliarm]

* Найти дополнительные сведения об интерфейсе Azure CLI, загрузить исходный код, сообщить о проблеме или внести свой вклад в проект можно на странице[репозитория GitHub для Azure CLI](https://github.com/azure/azure-xplat-cli).

* Если у вас возникли проблемы с использованием Azure CLI или Azure, посетите [форумы Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Дополнительную информацию об Azure см. на сайте [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=Oct15_HO3-->
<properties
	pageTitle="Установка Azure CLI для Mac, Linux и Windows"
	description="Установка интерфейса Azure CLI на компьютерах c Mac OS, Linux и Windows и работа со службам Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="danlep"/>

# Установка Azure CLI

В этом документе рассказывается, как установить интерфейс командной строки Azure (Azure CLI) В интерфейсе Azure CLI реализован набор консольных команд с открытым кодом для управления ресурсами среды Microsoft Azure.

> [AZURE.NOTE]Если вы уже установили Azure CLI, подключите этот интерфейс к своим ресурсам Azure. Дополнительные сведения см. в разделе [Подключение к подписке Azure](xplat-cli-connect.md#configure).

Интерфейс Azure CLI написан на языке JavaScript, и для его использования необходим файл [Node.js](https://nodejs.org). Он реализован с помощью [Azure SDK для Node.js ](https://github.com/azure/azure-sdk-for-node) и выпущен по лицензии Apache 2.0. Репозиторий проекта расположен по адресу [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

<a id="install"></a>
## Установка Azure CLI

Установить Azure CLI можно несколькими способами.

1. Использование установщика
2. Установка файла Node.js и выполнение команды **npm install**
3. Запуск Azure CLI как контейнера Docker

После установки Azure CLI для доступа к соответствующим функциям можно использовать команду **azure** в интерфейсе командной строки (Bash, терминал, командная строка).

## Использование установщика

Доступны следующие пакеты установщика:

* [Установщик Windows][windows-installer]

* [Установщик OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Установщик Linux][linux-installer]


## Установка файла Node.js и использование команды npm

Если файл уже Node.js установлен в вашей системе, используйте следующую команду для установки Azure CLI:

	npm install azure-cli -g

> [AZURE.NOTE]В системах Linux для выполнения команды __npm__ может потребоваться `sudo`.

### Установка файла node.js и использование команды npm в дистрибутивах Linux с системой управления пакетами [dpkg](http://en.wikipedia.org/wiki/Dpkg)
Большинство этих дистрибутивов используют либо [дополнительные средства упаковки (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool), либо другие средства на основе формата пакетов `.deb`. Примерами являются Ubuntu и Debian.

В большинстве более поздних версий указанных дистрибутивов необходимо установить **прежнюю версию nodejs** для правильной настройки средства **npm** для установки Azure CLI. В приведенном ниже коде показаны команды для правильной установки **npm** на Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Некоторые старые дистрибутивы, такие как Ubuntu 12.04, требуют установки текущего двоичного дистрибутива node.js. В приведенном ниже коде показано, как это сделать путем установки и использования средства **curl**.

>[AZURE.NOTE]Соответствующие команды взяты из инструкций по установке Joyent, которые можно найти [здесь](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Однако при использовании **sudo** в качестве целевого канала перед запуском устанавливаемых скриптов через **sudo** следует всегда проверять результаты их выполнения. Чем мощнее используемые средства, тем осторожнее следует с ними быть.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Установка файла node.js и использование команды npm в дистрибутивах Linux с системой управления пакетами [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

Установка node.js в дистрибутивах на основе RPM требует включения репозитория EPEL. В следующем коде показан рекомендуемый метод установки на CentOS 7. Обратите внимание на то, что в первой строке ниже очень важен символ дефиса (-)!

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Установка node.js и npm в Windows и Mac OS X

Для установки node.js и npm в Windows и OS X можно использовать установщики с сайта [Nodejs.org](https://nodejs.org/download/). Для завершения установки может потребоваться перезапустить компьютер. Чтобы проверить, правильно ли установлены файл node.js и средство npm, откройте командную строку и введите следующую команду:

	npm -v

Если отображается версия установленного файла npm, можно приступать к установке Azure CLI с помощью следующих команд:

	npm install -g azure-cli

Установив Azure CLI, с помощью команды **azure** вы сможете вызывать соответствующие команды в пользовательском интерфейсе командной строки. В конце установки экран будет выглядеть следующим образом:

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE]В системах Linux установить Azure CLI также можно путем компиляции [исходного кода](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). Дополнительные сведения о компиляции исходного кода см. в файле INSTALL, который включен в архив.

## Использование контейнера Docker

На узле Docker выполните следующую команду: 
```
	docker run -it microsoft/azure-cli
```

## Выполнение команд Azure CLI

После установки Azure CLI для доступа к соответствующим функциям можно использовать команду **azure** в интерфейсе командной строки (Bash, терминал, cmd.exe и т. п.). Например, чтобы выполнить команду справки (help) в Windows, запустите командную строку (cmd.exe) с правами администратора: 
```
	c:> azure help
```

Теперь все готово к работе. Вы можете [подключиться к среде Azure из интерфейса Azure CLI](xplat-cli-connect.md) и приступать к использованию команд **azure**.


<a id="additional-resources"></a>
## Дополнительные ресурсы

* [Использование межплатформенного интерфейса командной строки Microsoft Azure совместно с диспетчером служб][cliasm]

* [Использование межплатформенного интерфейса командной строки Microsoft Azure совместно с диспетчером ресурсов][cliarm]

* Найти дополнительные сведения об интерфейсе Azure CLI, загрузить исходный код, сообщить о проблеме или внести свой вклад в проект можно на странице[репозитория GitHub для Azure CLI](https://github.com/azure/azure-xplat-cli).

* Если у вас возникли проблемы с использованием Azure CLI или Azure, посетите [форумы Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Дополнительную информацию об Azure см. на сайте [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=58_postMigration-->
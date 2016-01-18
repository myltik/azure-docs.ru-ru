<properties
	pageTitle="Установка интерфейса командной строки Azure | Microsoft Azure"
	description="Установка Azure CLI на компьютерах c Mac OS, Linux и Windows и работа со службами Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Установка Azure CLI

Вы можете выполнить быструю установку интерфейса командной строки Azure (Azure CLI), чтобы использовать набор консольных команд с открытым кодом для управления ресурсами в среде Microsoft Azure. Выберите один из предоставленных пакетов установщика для установки Azure CLI в операционную систему, установите интерфейс командной строки с помощью Node.js и **npm** или в качестве контейнера на узле Docker. Дополнительные сведения и варианты установки см. в репозитории проектов на сайте [GitHub](https://github.com/azure/azure-xplat-cli).

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]

После установки Azure CLI вы сможете [подключить его в рамках своей подписки Azure](xplat-cli-connect.md) и выполнять команды **azure** из интерфейса командной строки (Bash, терминал, командная строка и т. д.) для работы с ресурсами Azure.




## Использование установщика

Доступны следующие пакеты установщика:

* [Установщик Windows][windows-installer]

* [Установщик OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Установщик Linux][linux-installer]


## Установка и использование Node.js и npm

Если файл уже Node.js установлен в вашей системе, используйте следующую команду для установки Azure CLI:

	npm install azure-cli -g

> [AZURE.NOTE]В системах Linux для выполнения команды __npm__ может потребоваться `sudo`.

### Установка Node.js и команды npm в дистрибутивах Linux с системой управления пакетами [dpkg](http://en.wikipedia.org/wiki/Dpkg)

Большинство этих дистрибутивов используют либо [дополнительные средства упаковки (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool), либо другие средства на основе формата пакетов `.deb`. Примерами являются Ubuntu и Debian.

В большинстве более поздних версий указанных дистрибутивов необходимо установить **прежнюю версию nodejs** для правильной настройки средства **npm** для установки Azure CLI. В приведенном ниже коде показаны команды для правильной установки **npm** на Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Некоторые старые дистрибутивы, такие как Ubuntu 12.04, требуют установки текущего двоичного дистрибутива Node.js. В приведенном ниже коде показано, как это сделать путем установки и использования средства **curl**.

>[AZURE.NOTE]Соответствующие команды взяты из инструкций по установке, которые можно найти [здесь](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Однако при использовании **sudo** в качестве целевого канала перед запуском устанавливаемых скриптов через **sudo** следует всегда проверять результаты их выполнения. Чем мощнее используемые средства, тем осторожнее следует с ними быть.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Установка Node.js и команды npm в дистрибутивах Linux с системой управления пакетами [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

Установка Node.js в дистрибутивах на основе RPM требует включения репозитория EPEL. В следующем коде показан рекомендуемый метод установки на CentOS 7. Обратите внимание на то, что в первой строке ниже очень важен символ дефиса (-)!

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Установка Node.js и команды npm в Windows и Mac OS X

Для установки Node.js и npm в Windows и OS X можно использовать установщики с сайта [Nodejs.org](https://nodejs.org/en/download/). Для завершения установки может потребоваться перезапустить компьютер. Чтобы проверить, правильно ли установлены Node.js и npm, в окне командной строки или программы Терминал введите следующую команду.

	npm -v

Если отображается версия установленного файла npm, можно приступать к установке Azure CLI с помощью следующих команд:

	npm install -g azure-cli

В конце установки экран будет выглядеть следующим образом:

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

>[AZURE.NOTE]В системах Linux установить Azure CLI также можно путем компиляции [исходного кода](http://go.microsoft.com/fwlink/?linkid=253472). Дополнительные сведения о компиляции исходного кода см. в файле INSTALL, который включен в архив.

## Использование контейнера Docker

На узле Docker выполните следующую команду:

```
docker run -it microsoft/azure-cli
```

## Выполнение команд Azure CLI
После установки Azure CLI можно использовать команду **azure** в пользовательском интерфейсе командной строки (Bash, терминал, командная строка и т. д.). Например, чтобы выполнить команду справки, введите следующее:

```
azure help
```

Чтобы просмотреть версию установленного интерфейса Azure CLI, введите следующую команду:

```
azure --version
```

Теперь все готово к работе. Для доступа ко всем командам интерфейса командной строки для работы с ресурсами [подключитесь к подписке Azure из Azure CLI](xplat-cli-connect.md).

## Обновление интерфейса командной строки

Корпорация Майкрософт часто выпускает обновленные версии Azure CLI. Переустановите интерфейс командной строки с помощью установщика операционной системы или, если установлены Node.js и npm, введите следующую команду (в системах Linux может потребоваться использовать режим **sudo**).

```
npm upgrade -g azure-cli
```

## Дополнительные ресурсы

* [Использование Azure CLI с диспетчером ресурсов Azure][cliarm]

* [Использование Azure CLI для управления службами Azure][cliasm]

* Дополнительные сведения об Azure CLI, а также о том, как загрузить исходный код, сообщить о проблемах или принять участие в проекте, см. в [репозитории GitHub для Azure CLI](https://github.com/azure/azure-xplat-cli).

* Если у вас возникли вопросы об использовании Azure CLI или Azure, посетите [форумы Azure](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0107_2016-->
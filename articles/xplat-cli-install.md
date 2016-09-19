<properties
	pageTitle="Установка интерфейса командной строки Azure | Microsoft Azure"
	description="Установка интерфейса командной строки (CLI) Azure на компьютерах c Mac OS, Linux и Windows и работа со службами Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="danlep"/>
    
# Установка Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Интерфейс командной строки Azure](xplat-cli-install.md)

Вы можете выполнить быструю установку интерфейса командной строки Azure (Azure CLI), чтобы использовать набор консольных команд с открытым кодом для управления ресурсами в среде Microsoft Azure. Установить последнюю версию можно несколькими способами:

* установка с помощью пакета NPM (требуется Node.js и NPM);
* установка с помощью пакета установщика (для разных операционных систем используются разные пакеты);
* установка интерфейса командной строки Azure в качестве контейнера на узле Docker.
    
Дополнительные сведения и варианты установки см. в репозитории проектов на сайте [GitHub](https://github.com/azure/azure-xplat-cli).

После установки интерфейса командной строки Azure вы сможете [подключить его к своей подписке Azure](xplat-cli-connect.md) и работать с ресурсами Azure c помощью команд **azure** из интерфейса командной строки (Bash, терминала, командной строки и т. д.).



## Вариант 1. Установка пакета npm

Для установки интерфейса командной строки из пакета NPM в системе должна быть установлена последняя версия Node.js и NPM. Затем установите пакет интерфейса командной строки Azure, выполнив следующую команду: (В системах Linux для выполнения команды __npm__ может потребоваться **sudo**.)

	npm install azure-cli -g

> [AZURE.NOTE]Если необходимо установить или обновить Node.js и NPM для вашей операционной системы, обратитесь к документации на сайте [Nodejs.org](https://nodejs.org/en/download/package-manager/). Рекомендуется установить последнюю версию Node.js LTS (4.x). Если вы используете более раннюю версию, возможны ошибки при установке. Дополнительные сведения об NPM см. на сайте [npmjs.com](https://www.npmjs.com/).

## Вариант 2. Использование установщика

Доступны также следующие пакеты установщика:


* [Установщик Mac OS X][mac-installer]

* [Установщик Windows][windows-installer]

* [TAR-файл Linux][linux-installer] \(требуется Node.js и NPM) — для установки выполните команду `sudo npm install -g <path to downloaded tar file>`


## Вариант 3. Использование контейнера Docker

Если вы настроили узел Docker, вы можете запустить интерфейс командной строки Azure в контейнере Docker. Выполните команду:

```
docker run -it microsoft/azure-cli
```

Дополнительные сведения о Docker см. на сайте [docker.com](https://docs.docker.com/engine/understanding-docker/).

## Выполнение команд Azure CLI
После установки интерфейса командной строки Azure можно использовать команду **azure** в любом пользовательском интерфейсе командной строки (Bash, терминале, командной строке и т. д.). Например, чтобы выполнить команду справки, введите следующее:

```
azure help
```
> [AZURE.NOTE]Для некоторых дистрибутивов Linux может появиться сообщение об ошибке `/usr/bin/env: ‘node’: No such file or directory`. Это связано с тем, что новые установки Node.js размещаются в папке /usr/bin/nodejs. Для устранения этой ошибки создайте символьную ссылку /usr/bin/node, выполнив следующую команду:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Чтобы просмотреть версию установленного интерфейса Azure CLI, введите следующую команду:

```
azure --version
```

Теперь все готово к работе. Чтобы получить доступ ко всем командам интерфейса командной строки для работы с ресурсами, [подключитесь к подписке Azure из интерфейса командной строки Azure](xplat-cli-connect.md).

>[AZURE.NOTE] При первом использовании интерфейса командной строки Azure появится сообщение с вопросом, следует ли разрешить Майкрософт собирать сведения об использовании интерфейса командной строки. Участие является добровольным. Если вы согласились участвовать в этой программе, сбор сведений можно остановить в любой момент, выполнив команду `azure telemetry --disable`. Чтобы включить участие в любой момент, выполните команду `azure telemetry --enable`.


## Обновление интерфейса командной строки

Корпорация Майкрософт часто выпускает обновленные версии Azure CLI. Переустановите интерфейс командной строки с помощью установщика для соответствующей операционной системы или запустите актуальную версию контейнера Docker. Если вы установили последние версии Node.js и NPM, введите следующую команду (в системах Linux может потребоваться использовать режим **sudo**).

```
npm update -g azure-cli
```

## Включение выполнения нажатием клавиши TAB

Выполнение команд интерфейса командной строки нажатием клавиши TAB поддерживается на компьютерах MAC и Linux.

Чтобы включить эту функцию в zsh, выполните следующую команду:

```
echo '. <(azure --completion)' >> .zshrc
```

Чтобы включить эту функцию в bash, выполните следующую команду:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## Дальнейшие действия 

* [Подключитесь к подписке Azure из интерфейса командной строки](xplat-cli-connect.md) для создания ресурсов Azure и управления ими.

* Дополнительные сведения об Azure CLI, а также о том, как загрузить исходный код, сообщить о проблемах или принять участие в проекте, см. в [репозитории GitHub для Azure CLI](https://github.com/azure/azure-xplat-cli).

* Если у вас возникли вопросы об использовании интерфейса командной строки Azure или платформы Azure, посетите [форумы Azure](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=azurescripting).

* В системах Linux установить Azure CLI также можно путем компиляции [исходного кода](http://aka.ms/linux-azure-cli). Дополнительные сведения о компиляции исходного кода см. в файле INSTALL, который включен в исходный архив.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0907_2016-->
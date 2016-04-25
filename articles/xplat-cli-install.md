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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Установка Azure CLI

Вы можете выполнить быструю установку интерфейса командной строки Azure \(Azure CLI\), чтобы использовать набор консольных команд с открытым кодом для управления ресурсами в среде Microsoft Azure. У вас есть выбор: используйте один из предоставленных пакетов установщика для разных операционных систем, используйте для установки пакет npm или установите Azure CLI в качестве контейнера на узле Docker. Дополнительные сведения и варианты установки см. в репозитории проектов на сайте [GitHub](https://github.com/azure/azure-xplat-cli).


После установки Azure CLI вы сможете [подключить его в рамках своей подписки Azure](xplat-cli-connect.md) и выполнять команды **azure** из интерфейса командной строки \(Bash, терминала, командной строки и т. д.\) для работы с ресурсами Azure.


## Использование установщика

Доступны следующие пакеты установщика:

* [Установщик Windows][windows-installer]

* [Установщик OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Установщик Linux][linux-installer]


## Установка пакета npm

В качестве альтернативы, если последние версии Node.js и npm уже установлены в вашей системе, используйте следующую команду для установки пакета Azure CLI. В системах Linux для выполнения команды __npm__ может потребоваться **sudo**.

	npm install azure-cli -g

> [AZURE.NOTE]Если необходимо установить или обновить Node.js и npm для вашей операционной системы, обратитесь к документации на сайте [Nodejs.org](https://nodejs.org/en/download/package-manager/). Рекомендуется установить последнюю версию Node.js LTS \(4.x\). Если вы используете более раннюю версию, возможны ошибки при установке.


## Использование контейнера Docker

На узле Docker выполните следующую команду:

```
docker run -it microsoft/azure-cli
```

## Выполнение команд Azure CLI
После установки Azure CLI можно использовать команду **azure** в пользовательском интерфейсе командной строки \(Bash, терминале, командной строке и т. д.\). Например, чтобы выполнить команду справки, введите следующее:

```
azure help
```

Чтобы просмотреть версию установленного интерфейса Azure CLI, введите следующую команду:

```
azure --version
```

Теперь все готово к работе. Чтобы получить доступ ко всем командам интерфейса командной строки для работы с ресурсами, [подключитесь к подписке Azure из Azure CLI](xplat-cli-connect.md).


## Обновление интерфейса командной строки

Корпорация Майкрософт часто выпускает обновленные версии Azure CLI. Переустановите CLI с помощью установщика для вашей операционной системы или, если у вас установлены последние версии Node.js и npm, введите следующую команду \(в системах Linux может потребоваться использовать режим **sudo**\).

```
npm update -g azure-cli
```

## Дальнейшие действия 

* [Подключение из CLI к подписке Azure](xplat-cli-connect.md) для создания ресурсов Azure и управления ими.

* Дополнительные сведения об Azure CLI, а также о том, как загрузить исходный код, сообщить о проблемах или принять участие в проекте, см. в [репозитории GitHub для Azure CLI](https://github.com/azure/azure-xplat-cli).

* Если у вас возникли вопросы об использовании Azure CLI или Azure, посетите [форумы Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* В системах Linux установить Azure CLI также можно путем компиляции [исходного кода](http://aka.ms/linux-azure-cli). Дополнительные сведения о компиляции исходного кода см. в файле INSTALL, который включен в исходный архив.

[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0413_2016-->
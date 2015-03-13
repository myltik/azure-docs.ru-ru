<properties 
	pageTitle="Загрузка пакета Azure SDK для PHP" 
	description="Узнайте, как скачать и установить пакет SDK для Azure для PHP." 
	documentationCenter="php" 
	services="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="2/5/2015" 
	ms.author="tomfitz"/>

#Загрузка пакета Azure SDK для PHP

Azure SDK для PHP включает компоненты для разработки, развертывания приложений PHP в Azure и управления ими. Пакет Azure SDK для PHP включает следующие средства:

* **Клиентские библиотеки PHP для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.  
* **Средства командной строки Azure для Mac и Linux**. Это программы командной строки для развертывания служб Azure, таких как веб-сайты Azure и виртуальные машины Azure, и управления ими. Эти средства работают на любой платформе, включая Windows, Linux и Mac.
* **Azure PowerShell (только для Windows)**. Это набор командлетов PowerShell для развертывания таких служб Azure, как облачные службы и виртуальные машины, и управления ими.
* **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения данных - это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

В приведенных ниже разделах описано, как загрузить и установить указанные выше компоненты. 

При работе с инструкциями, приведенными в этом разделе, предполагается, что установлена среда [PHP][install-php].

> [WACOM.NOTE] 
> Необходимо иметь PHP версии 5.3 и выше для использования клиентских библиотек PHP для Azure. 

##Клиентские библиотеки PHP для Azure

Клиентские библиотеки PHP для Azure предоставляют интерфейс для доступа к возможностям Azure, например службам управления данными и облачным службам, из любой операционной системы. Эти библиотеки могут быть установлены через Composer или диспетчеры пакетов PEAR, а также вручную.

Сведения об использовании клиентских библиотек PHP для Azure см. в разделах [Использование службы BLOB-объектов][blob-service], [Использование службы таблиц][table-service] и [Использование службы очередей][queue-service].

###Установка через компоновщик

1. [Установите Git][install-git]. 


	> [WACOM.NOTE] 
	> В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

2. Создайте файл с именем **composer.json** в корневом каталоге своего проекта и добавьте в него следующий код:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Загрузите **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните эту команду в корневом каталоге проекта.

		Установка php composer.phar

###Установка в качестве пакета PEAR

Чтобы установить клиентские библиотеки PHP для Azure в качестве пакета PEAR, выполните следующие действия.

1. [Установите PEAR][install-pear].
2. Настройте канал Azure PEAR:

		pear channel-discover pear.windowsazure.com
3. Установите пакет PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

После завершения установки можно ссылаться на библиотеки классов из приложения.

###Установка вручную

Чтобы вручную скачать и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1. Загрузите ZIP-архив с библиотеками из [GitHub][php-sdk-github]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

	> [WACOM.NOTE] 
> Клиентские библиотеки PHP Windows Azure зависят от пакетов PEAR [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime) и [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью [диспетчера пакетов PEAR](http://pear.php.net/manual/en/installation.php).

2. Скопируйте каталог "WindowsAzure" из загруженного архива в структуру каталога приложения, после чего можно ссылаться на классы из своего приложения.

##Azure PowerShell и эмуляторы Azure

Azure PowerShell - это набор командлетов PowerShell для развертывания служб Azure и управления ими, например облачных служб и виртуальных машин. Эмуляторы Azure - это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эти компоненты поддерживаются только в Windows.

Чтобы установить Azure PowerShell и эмуляторы Azure, рекомендуется использовать [установщик веб-платформы Майкрософт][download-wpi]. Обратите внимание, что можно также установить другие компоненты разработки, например PHP, SQL Server, драйверы Microsoft SQL Server для PHP и WebMatrix.

Дополнительные сведения о том, как использовать Azure PowerShell, см. в разделе [Использование Azure PowerShell][powershell-tools].

##Средства командной строки Azure для Mac и Linux

Программы командной строки Azure для Mac и Linux предназначены для развертывания служб Azure (например, веб-сайтов и виртуальных машин Azure) и управления ими. В следующем списке описано, как установить инструменты в зависимости от используемой операционной системы:

* **Mac**: загрузите установщик Azure SDK здесь: [http://go.microsoft.com/fwlink/?LinkId=252249][mac-installer]. Откройте загруженный PKG-файл и выполните установку в соответствии с указаниями.

* **Linux**: Установите последнюю версию [Node.js][nodejs-org] (см. раздел [Установка Node.js через диспетчер пакетов][install-node-linux]), затем выполните следующую команду:

		npm install azure-cli -g

	> [WACOM.NOTE] 
	> Возможно, для выполнения этой команды потребуются повышенные права:  "sudo npm install azure cli - g"


Сведения об использовании средств командной строки Azure для Mac и Linux см. в разделе [Использование средств командной строки для Linux и Mac][crossplat-tools].

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[crossplat-tools]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!--HONumber=46--> 

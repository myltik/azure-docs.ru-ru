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

## Обзор

Azure SDK для PHP включает компоненты для разработки, развертывания приложений PHP в Azure и управления ими. Пакет Azure SDK для PHP включает следующие средства:

* **Клиентские библиотеки PHP для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.  
* **Средства командной строки Azure для Mac и Linux**. Это программы командной строки для развертывания служб Azure, таких как веб-сайты Azure и виртуальные машины Azure, и управления ими. Эти средства работают на любой платформе, включая Windows, Linux и Mac.
* **Azure PowerShell (только Windows)**. Это набор командлетов PowerShell для развертывания служб Azure, таких как облачные службы и виртуальные машины, и управления ими.
* **Эмуляторы Azure (только Windows)**. Эмуляторы вычислений и хранения - это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

В приведенных ниже разделах описано, как загрузить и установить указанные выше компоненты. 

Инструкции в этом разделе предполагается, что [PHP][установки php] установлен.

> [AZURE.NOTE] 
> Необходимо иметь PHP версии 5.3 и выше для использования клиентских библиотек PHP для Azure. 

##Клиентские библиотеки PHP для Azure

Клиентские библиотеки PHP для Azure предоставляют интерфейс для доступа к возможностям Azure, например службам управления данными и облачным службам, из любой операционной системы. Эти библиотеки могут быть установлены через Composer или диспетчеры пакетов PEAR, а также вручную.

Сведения о том, как использовать клиентские библиотеки PHP для Azure см. в разделе [использование службы BLOB-объектов][службы BLOB-объектов], [использование службы таблиц][службы таблиц] и [использование службы очередей][службы очередей].

###Установка через компоновщик

1. [Установка Git][install-git]. 


	> [AZURE.NOTE] 
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

3. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните эту команду в корневом каталоге проекта.

		php composer.phar install

###Установка в качестве пакета PEAR

Чтобы установить клиентские библиотеки PHP для Azure в качестве пакета PEAR, выполните следующие действия.

1. [Установите PEAR][install груша].
2. Настройте канал Azure PEAR:

		pear channel-discover pear.windowsazure.com
3. Установите пакет PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

После завершения установки можно ссылаться на библиотеки классов из приложения.

###Установка вручную

Чтобы вручную скачать и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1. Скачайте ZIP-файл архива, содержащий библиотеки из [GitHub][php-sdk-github]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

	> [AZURE.NOTE] 
	> Клиентские библиотеки PHP для Azure имеют зависимость от [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime), и [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) пакетов PEAR. Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью [диспетчера пакетов PEAR](http://pear.php.net/manual/en/installation.php)

2. Копировать `WindowsAzure` каталог загруженного архива в классах структуры и ссылки каталога приложения из вашего приложения.

##Azure PowerShell и эмуляторы Azure

Azure PowerShell - это набор командлетов PowerShell для развертывания и управления службами Azure (например облачных служб и виртуальных машин). Эмуляторы Azure - эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эти компоненты поддерживаются только в Windows.

Чтобы установить Azure PowerShell и эмуляторы Azure рекомендуется использовать [Microsoft Web Platform Installer][загрузки-удостоверения рабочего процесса]. Обратите внимание, что также можно установить другие компоненты разработки, например PHP, SQL Server, драйверы Microsoft для SQL Server для PHP и WebMatrix.

Сведения о том, как использовать Azure PowerShell см. в разделе [использование Azure PowerShell][средства powershell].

##Средства командной строки Azure для Mac и Linux

Средства командной строки Azure для Mac и Linux - это набор средств командной строки для развертывания и управления службами Azure, например веб-сайтов Azure и Azure виртуальные машины. В следующем списке описано, как установить средства в зависимости от используемой операционной системы:

* **Mac**: загрузите установщик Azure SDK здесь: [http://go.Microsoft.com/fwlink/?LinkId=252249][установщика mac]. Откройте загруженный файл .pkg и выполните установку.

* **Linux**: установите последнюю версию [Node.js][nodejs-org] (см. раздел [Установка Node.js через диспетчер пакетов][install-node-linux]), затем выполните следующую команду:

		npm install azure-cli -g

	> [AZURE.NOTE] 
	> Возможно, возникнет необходимость выполнить эту команду с повышенными привилегиями:  `sudo npm install azure-cli -g`


Сведения об использовании средств командной строки Azure для Mac и Linux см. в разделе [использование средств командной строки для Mac и Linux][crossplat средств].

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

<!--HONumber=47-->

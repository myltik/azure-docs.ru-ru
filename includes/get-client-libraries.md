###Установка через компоновщик

1. [Установите Git][install-git]. 

	<div class="dev-callout"> 
	<b>Примечание</b> 
	<p>В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.</p>
	</div>

2. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:

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

		php composer.phar install

###Установка вручную

Чтобы вручную загрузить и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1. Загрузите содержащий библиотеки архив .zip из [GitHub][php-sdk-github]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

	<div class="dev-callout"> 
	<b>Примечание</b> 
	<p>Клиентские библиотеки PHP Azure имеют зависимость от пакетов PEAR <a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>, <a href="http://pear.php.net/package/Mail_mime">Mail_mime</a> и <a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a>. Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью <a href="http://pear.php.net/manual/en/installation.php">диспетчера пакетов PEAR</a>.</p> 
	</div>


2. Скопируйте каталог `WindowsAzure` загруженного архива в структуру каталога приложения.

Дополнительные сведения об установке клиентских библиотек PHP для Azure (включая сведения об установке как пакет PEAR) см. в разделе [Загрузить пакет SDK Azure для PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar


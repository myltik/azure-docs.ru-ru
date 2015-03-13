###Установка через компоновщик

1. [Установите Git][install-git] 

 
	> [AZURE.NOTE] В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

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

		php composer.phar install

###Установка вручную

Чтобы вручную скачать и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1. Загрузите содержащий библиотеки архив .zip из [GitHub][php-sdk-github]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

	
	> [AZURE.NOTE] Клиентские библиотеки PHP Azure имеют зависимость от пакетов PEAR [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime) и [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью [диспетчера пакетов PEAR](http://pear.php.net/manual/en/installation.php).


2. Скопируйте каталог `WindowsAzure` загруженного архива в структуру каталогов приложения.

Дополнительные сведения об установке клиентских библиотек PHP для Azure (включая сведения об установке как пакет PEAR) см. в разделе [Загрузка пакета SDK Azure для PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar

<!--HONumber=45--> 

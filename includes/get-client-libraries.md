### Установка через компоновщик

1. [Установите Git][install-git]. Обратите внимание, что в Windows необходимо также добавить исполняемый файл Git в переменную среды PATH. 

2. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:

	```
	{
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
	```

3. Загрузите **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните следующую команду в корневом каталоге проекта.

	```
	php composer.phar install
	```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=AcomDC_0601_2016-->
### Установка через компоновщик

1.  [Установите Git][Установите Git].

    <div class="dev-callout">

    **Примечание.**
    В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

    </div>

2.  Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:

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

3.  Загрузите **[composer.phar][composer.phar]** в корневой каталог проекта.

4.  Откройте командную строку и выполните эту команду в корневом каталоге проекта.

        php composer.phar install

### Установка вручную

Чтобы вручную скачать и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1.  Загрузите содержащий библиотеки архив .zip из [GitHub][GitHub]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

    <div class="dev-callout">

    **Примечание.**
    Клиентские библиотеки PHP Azure имеют зависимость от пакетов PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] и [Mail\_mimeDecode][Mail\_mimeDecode]. Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью [диспетчера пакетов PEAR][диспетчера пакетов PEAR].

    </div>

2.  Скопируйте каталог `WindowsAzure` скачанного архива в структуру каталога приложения.

Дополнительные сведения об установке клиентских библиотек PHP для Azure (включая сведения об установке как пакет PEAR) см. в разделе [Скачивание пакета SDK Azure для PHP][Скачивание пакета SDK Azure для PHP].

  [Установите Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [диспетчера пакетов PEAR]: http://pear.php.net/manual/en/installation.php
  [Скачивание пакета SDK Azure для PHP]: ../php-download-sdk/

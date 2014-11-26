<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Загрузка пакета Azure SDK для PHP

Azure SDK для PHP включает компоненты для разработки, развертывания приложений PHP в Azure и управления ими. Пакет Azure SDK для PHP включает следующие средства:

-   **Клиентские библиотеки PHP для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.
-   **Средства командной строки Azure для Mac и Linux**. Это программы командной строки для развертывания служб Azure, таких как веб-сайты Azure и виртуальные машины Azure, и управления ими. Эти средства работают на любой платформе, включая Windows, Linux и Mac.
-   **Azure PowerShell (только для Windows)**. Это набор командлетов PowerShell для развертывания служб Azure, таких как облачные службы и виртуальные машины, и управления ими.
-   **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения данных – это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

В приведенных ниже разделах описано, как загрузить и установить указанные выше компоненты.

При работе с инструкциями, приведенными в этом разделе, предполагается, что установлена среда [PHP][PHP].

> [WACOM.NOTE]
> Необходимо иметь PHP версии 5.3 и выше для использования клиентских библиотек PHP для Azure.

## Клиентские библиотеки PHP для Azure

Клиентские библиотеки PHP для Azure предоставляют интерфейс для доступа к возможностям Azure, например службам управления данными и облачным службам, из любой операционной системы. Эти библиотеки могут быть установлены через Composer или диспетчеры пакетов PEAR, а также вручную.

Сведения об использовании клиентских библиотек PHP для Azure см. в разделах [Использование службы BLOB-объектов][Использование службы BLOB-объектов], [Использование службы таблиц][Использование службы таблиц] и [Использование службы очередей][Использование службы очередей].

### Установка через компоновщик

1.  [Установите Git][Установите Git].

    > [WACOM.NOTE]
    > В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

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

### Установка в качестве пакета PEAR

Чтобы установить клиентские библиотеки PHP для Azure в качестве пакета PEAR, выполните следующие действия.

1.  [Установите PEAR][Установите PEAR].
2.  Настройте канал Azure PEAR:

        pear channel-discover pear.windowsazure.com

3.  Установите пакет PEAR:

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

После завершения установки можно ссылаться на библиотеки классов из приложения.

### Установка вручную

Чтобы вручную скачать и установить клиентские библиотеки PHP для Azure, выполните следующие действия:

1.  Загрузите содержащий библиотеки архив .zip из [GitHub][GitHub]. Или же скопируйте репозиторий и клонируйте его на свой локальный компьютер. (Для последнего варианта требуется учетная запись GitHub и наличие установленного локально Git.)

    > [WACOM.NOTE]
    > Клиентские библиотеки PHP для Azure имеют зависимость от пакетов PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] и [Mail\_mimeDecode][Mail\_mimeDecode]. Чтобы устранить эти зависимости рекомендуется установить эти пакеты с помощью [диспетчера пакетов PEAR][диспетчера пакетов PEAR].

2.  Скопируйте каталог `WindowsAzure` загруженного архива в структуру каталога приложения, после чего можно ссылаться на классы из своего приложения.

## Azure PowerShell и эмуляторы Azure

Azure PowerShell — это набор командлетов PowerShell для развертывания служб Azure и управления ими, например облачных служб и виртуальных машин. Эмуляторы Azure — это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эти компоненты поддерживаются только в Windows.

Чтобы установить Azure PowerShell и эмуляторы Azure, рекомендуется использовать [установщик веб-платформы Майкрософт][установщик веб-платформы Майкрософт]. Обратите внимание, что также можно установить другие компоненты разработки, например PHP, SQL Server, драйверы Microsoft для SQL Server для PHP и WebMatrix.

Дополнительные сведения о том, как использовать Azure PowerShell, см. в разделе [Использование Azure PowerShell][Использование Azure PowerShell].

## Средства командной строки Azure для Mac и Linux

Программы командной строки Azure для Mac и Linux — это программы командной строки для развертывания служб Azure (например, веб-сайтов и виртуальных машин Azure) и управления ими. В следующем списке описано, как установить средства в зависимости от используемой операционной системы:

-   **Mac**: загрузите установщик Azure SDK здесь: [][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>. Откройте загруженный файл .pkg и выполните установку.

-   **Linux**: установите последнюю версию [Node.js][Node.js] (см. раздел [Установка Node.js через диспетчер пакетов][Установка Node.js через диспетчер пакетов]), затем выполните следующую команду:

        npm install azure-cli -g

    > [WACOM.NOTE]
    > Возможно, возникнет необходимость выполнить эту команду с повышенными привилегиями. `sudo npm install azure-cli -g`

Сведения об использовании средств командной строки Azure для Mac и Linux см. в разделе [Использование средств командной строки для Linux и Mac][Использование средств командной строки для Linux и Mac].

  [PHP]: http://www.php.net/manual/en/install.php
  [Использование службы BLOB-объектов]: http://go.microsoft.com/fwlink/?LinkId=252714
  [Использование службы таблиц]: http://go.microsoft.com/fwlink/?LinkId=252715
  [Использование службы очередей]: http://go.microsoft.com/fwlink/?LinkId=252716
  [Установите Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [Установите PEAR]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [диспетчера пакетов PEAR]: http://pear.php.net/manual/en/installation.php
  [установщик веб-платформы Майкрософт]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Использование Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Установка Node.js через диспетчер пакетов]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Использование средств командной строки для Linux и Mac]: http://go.microsoft.com/fwlink/?LinkId=252717

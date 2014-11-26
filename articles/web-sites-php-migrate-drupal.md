<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Migrating Drupal to Azure Websites" pageTitle="Migrating Drupal to Azure Websites" metaKeywords="Drupal, PHP, Web Sites" description="Migrate a Drupal PHP site to Azure Websites." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrating Drupal to Azure Websites" authors="cephalin; jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin; jroth" />

# Миграция Drupal на веб-сайты Azure

Поскольку веб-сайты Azure поддерживают PHP и MySQL, миграция сайта Drupal на веб-сайты Azure не вызывает особых сложностей. И поскольку Drupal и PHP работают на любой платформе, данный процесс должен обеспечивать перемещение Drupal на веб-сайты Azure вне зависимости от текущей платформы. Учитывая это, установки Drupal могут значительно отличаться друг от друга, поэтому возможно наличие некоторых уникальных этапов миграции, которые не рассматриваются в приведенном ниже материале. Обратите внимание, что средство Drush не используется, так как оно не поддерживается на веб-сайтах Azure.

> [WACOM.NOTE]
> При перемещении большого и сложного приложения Drupal можно также рассмотреть использование облачных служб Azure. Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в разделе [Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?][Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?] Справку по перемещению Drupal в облачные службы см. в разделе [Перенос сайта Drupal из LAMP в Windows Azure][Перенос сайта Drupal из LAMP в Windows Azure].

## Оглавление

-   [Создание веб-сайта Azure][Создание веб-сайта Azure]
-   [Копирование базы данных][Копирование базы данных]
-   [Изменение Settings.php][Изменение Settings.php]
-   [Развертывание кода Drupal][Развертывание кода Drupal]
-   [Связанные сведения][Связанные сведения]

## <a name="create-siteanddb"></a><span class="short-header">Создание веб-сайта Azure и базы данных MySQL</span>1. Создание веб-сайта Azure и базы данных MySQL

Во-первых, изучите этот пошаговый учебник, чтобы узнать, как создать новый веб-сайт с MySQL: [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git][Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git]. Если вы планируете использовать Git для публикации сайта Drupal, то выполните действия из учебника, где объясняется, как настроить репозиторий Git. Обязательно следуйте инструкциям из раздела **Получение сведений об удаленном подключении к MySQL**, так как эта информация потребуется вам позже. Вы можете пропустить оставшуюся часть учебника, если интересуетесь исключительно развертыванием веб-сайта Drupal, но, если вы впервые столкнулись с веб-сайтами Azure (и Git), эта информация может оказаться полезной.

После установки нового веб-сайта с базой данных MySQL вы получаете данные о подключении к базе данных MySQL и (необязательно) репозиторий Git. Следующим шагом является копирование базы данных в MySQL на веб-сайтах Azure.

## <a name="copy-database"></a><span class="short-header">Копирование базы данных в MySQL на веб-сайтах Azure</span>2. Копирование базы данных в MySQL на веб-сайтах Azure

Существует множество способов переноса базы данных в Azure. Один из способов, который хорошо подходит для баз данных MySQL, заключается в использовании средства MySqlDump. Следующая команда предоставляет пример того, как осуществить копирование с локального компьютера на веб-сайты Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Конечно же, необходимо предоставить имя пользователя и пароль для существующей базы данных Drupal. Необходимо также предоставить имя узла, имя пользователя, пароль и имя базы данных для базы данных MySQL, созданной в первом шаге. Эта информация присутствует в собранных ранее сведениях строки подключения. Строка подключения должна иметь формат, аналогичный следующей строке:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

В зависимости от размера базы данных процесс копирования может занять несколько минут.

Теперь база данных Drupal находится на веб-сайтах Azure. Перед развертыванием кода Drupal необходимо изменить его, чтобы он мог подключиться к новой базе данных.

## <a name="modify-settingsphp"></a><span class="short-header">Изменение сведений о подключении к базе данных в settings.php</span>3. Изменение сведений о подключении к базе данных в settings.php

Здесь вам снова понадобятся сведения о подключении к новой базе данных. Откройте файл **/drupal/sites/default/setting.php** в текстовом редакторе и замените значения database, username, password и host в массиве **$databases** на правильные значения для новой базы данных. По завершении должно получиться следующее:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Сохраните файл **settings.php**. Теперь все готово к развертыванию.

## <a name="deploy-drupalcode"></a><span class="short-header">Развертывание кода Drupal с помощью Git или FTP</span>4. Развертывание кода Drupal с помощью Git или FTP

Последний шаг заключается в развертывании кода на веб-сайтах Azure с помощью Git или FTP.

При использовании FTP получите имя узла FTP и имя пользователя из панели мониторинга веб-сайта. Затем воспользуйтесь любым FTP-клиентом для передачи файлов Drupal в папку **/site/wwwroot** удаленного сайта.

При использовании Git в предыдущих шагах должен был настраиваться репозиторий Git. Необходимо установить Git на локальном компьютере. После создания репозитория следуйте приведенным инструкциям.

> [WACOM.NOTE]
> В зависимости от параметров Git может потребоваться изменить файл GITIGNORE (скрытый файл и элемент того же уровня для папки GIT, созданной в локальном корневом каталоге после выполнения git commit). Это позволяет указать файлы в приложении Drupal, которые можно проигнорировать. Если эти сведения содержат файлы, которые должны быть развернуты, удалите эти записи, чтобы такие файлы не игнорировались.

После развертывания Drupal на веб-сайтах Azure можно продолжить развертывание обновлений через Git или FTP.

## <a name="related-information"></a><span class="short-header">Связанные сведения</span>Связанные сведения

Дополнительные сведения см. в следующих записях и разделах:

-   [Веб-сайты Azure с точки зрения PHP][Веб-сайты Azure с точки зрения PHP]
-   [Веб-сайты Windows Azure, облачные службы и виртуальные машины: когда они используются?][Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?]
-   [Настройка PHP на веб-сайтах Azure с помощью файлов .user.ini][Настройка PHP на веб-сайтах Azure с помощью файлов .user.ini]
-   [Модуль интеграции Azure][Модуль интеграции Azure]
-   [Модуль хранилища BLOB-объектов Azure][Модуль хранилища BLOB-объектов Azure]

  [Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Перенос сайта Drupal из LAMP в Windows Azure]: http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx
  [Создание веб-сайта Azure]: #create-siteanddb
  [Копирование базы данных]: #copy-database
  [Изменение Settings.php]: #modify-settingsphp
  [Развертывание кода Drupal]: #deploy-drupalcode
  [Связанные сведения]: #related-information
  [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git]: http://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-git/
  [Веб-сайты Azure с точки зрения PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Настройка PHP на веб-сайтах Azure с помощью файлов .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Модуль интеграции Azure]: https://drupal.org/project/azure_auth
  [Модуль хранилища BLOB-объектов Azure]: https://drupal.org/project/azure_blob

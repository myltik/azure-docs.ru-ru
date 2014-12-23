<properties urlDisplayName="Migrating Drupal to Azure Websites" pageTitle="Миграция Drupal на веб-сайты Azure" metaKeywords ="Drupal, PHP, веб-сайты" description="Migrate a Drupal PHP site to Azure Websites." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrating Drupal to Azure Websites" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/11/2014" ms.author="tomfitz" />


# Миграция Drupal на веб-сайты Azure

Поскольку веб-сайты Azure поддерживают PHP и MySQL, миграция сайта Drupal на веб-сайты Azure не вызывает особых сложностей. И поскольку Drupal и PHP работают на любой платформе, данный процесс должен обеспечивать перемещение Drupal на веб-сайты Azure вне зависимости от текущей платформы. Учитывая это, установки Drupal могут значительно отличаться друг от друга, поэтому возможно наличие некоторых уникальных этапов миграции, которые не рассматриваются в приведенном ниже материале. Обратите внимание, что средство Drush не используется, так как оно не поддерживается на веб-сайтах Azure.

> [WACOM.NOTE]
>  При перемещении большого и сложного приложения Drupal можно также рассмотреть использование облачных служб Azure. Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?</a> Для получения справки по перемещению Drupal в облачные службы см. раздел<a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Миграция сайта Drupal из LAMP в Windows Azure</a>.

## Оглавление

- [Создание веб-сайта Azure][]
- [Копирование базы данных][]
- [Изменение Settings.php][]
- [Развертывание кода Drupal][]
- [Связанные сведения][]
 
##<a name="create-siteanddb"></a>1. Создание веб-сайта Azure и базы данных MySQL

Во-первых, изучите этот пошаговый учебник, чтобы узнать, как создать новый веб-сайт с MySQL: [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git][]. Если вы планируете использовать Git для публикации сайта Drupal, то выполните действия из учебника, где объясняется, как настроить репозиторий Git. Обязательно следуйте инструкциям из раздела **Получение сведений об удаленном подключении к MySQL**, так как эта информация потребуется вам позже. Вы можете пропустить оставшуюся часть учебника, если интересуетесь исключительно развертыванием веб-сайта Drupal, но если вы впервые столкнулись с веб-сайтами Azure (и Git), эти сведения могут оказаться полезными.

После установки нового веб-сайта с базой данных MySQL вы получаете данные о подключении к базе данных MySQL и (необязательно) репозиторию Git. Следующим шагом является копирование базы данных в MySQL в веб-сайт Azure.

##<a name="copy-database"></a>2. Скопируйте базу данных MySQL в "Веб-сайты Azure"

Есть множество способов миграции базы данных в Azure. Один из способов хорошо подходит для баз данных MySQL и заключается в использовании средства [MySqlDump][]. Следующая команда представляет собой пример того, как выполнить копирование с локального компьютера на веб-сайты Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Конечно, необходимо предоставить имя пользователя и пароль для существующей базы данных Drupal. Необходимо также предоставить имя узла, имя пользователя, пароль и имя базы данных для базы данных MySQL, созданной в первом шаге. Эта информация присутствует в собранных ранее сведениях строки подключения. Строка подключения должна иметь формат следующего вида:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

В зависимости от размера базы данных процесс копирования может занять несколько минут.

Теперь база данных Drupal находится на веб-сайтах Azure. Перед развертыванием кода Drupal необходимо изменить его, чтобы позволить ему подключаться к новой базе данных.

##<a name="modify-settingsphp"></a>3. Изменение сведений о подключении к базе данных в settings.php

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

##<a name="deploy-drupalcode"></a>4. Развертывание кода Drupal с помощью Git или FTP

Последний шаг заключается в развертывании кода на веб-сайтах Azure с помощью Git или FTP.

При использовании FTP получите имя узла FTP и имя пользователя из панели мониторинга веб-сайта. Затем воспользуйтесь любым FTP-клиентом для загрузки файлов Drupal в папку **/site/wwwroot** удаленного сайта.

Если вы использовали Git в предыдущих шагах, должна была осуществиться настройка репозитория Git. Необходимо установить Git на локальном компьютере. После создания репозитория следуйте приведенным инструкциям.

> [WACOM.NOTE]
>  В зависимости от параметров Git может потребоваться изменить файл GITIGNORE (скрытый файл и элемент того же уровня для папки GIT, созданной в локальном корневом каталоге после выполнения git commit). Это позволяет указать файлы в приложении Drupal, которые можно проигнорировать. Если эти сведения содержат файлы, которые должны быть развернуты, удалите эти записи, чтобы такие файлы не игнорировались.

После развертывания Drupal на веб-сайтах Azure можно продолжить развертывание обновлений через Git или FTP.

##<a name="related-information"></a>Связанные сведения

Дополнительные сведения см. в следующих записях и разделах:

- [Веб-сайты Azure с точки зрения PHP][]
- [Веб-сайты Windows Azure, облачные службы и виртуальные машины: когда они используются?][]
- [Настройка PHP на веб-сайтах Azure с помощью файлов .user.ini][]
- [Модуль интеграции Azure](https://drupal.org/project/azure_auth)
- [Модуль хранилища BLOB-объектов Azure](https://drupal.org/project/azure_blob)

  [Создание веб-сайта Azure]: #create-siteanddb
  [Копирование базы данных]: #copy-database
  [Изменение Settings.php]: #modify-settingsphp
  [Развертывание кода Drupal]: #deploy-drupalcode
  [Связанные сведения]: #related-information
  [Создание веб-сайта Azure на PHP-MySQL и его развертывание с помощью Git]: http://www.windowsazure.com/ru-ru/develop/php/tutorials/website-w-mysql-and-git/
  
  [Веб-сайты Azure с точки зрения PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Веб-сайты Windows Azure, облачные службы и виртуальные машины: когда они используются?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Настройка PHP на веб-сайтах Azure с помощью файлов .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Модуль интеграции Azure]: http://drupal.org/project/azure

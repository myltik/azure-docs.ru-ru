<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress корпоративного уровня на веб-сайтах Azure" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="01/01/1900" ms.author="cephalin" />

#WordPress корпоративного уровня на веб-сайтах Azure

Веб-сайты Azure предоставляют масштабируемую, безопасную и простую в использовании среду для критически важных, крупных масштабируемых сайтов [WordPress][wordpress]. Microsoft также располагает сайтами корпоративного класса, такими как блоги [Office][officeblog] и [Bing][bingblog]. В этом документе показано, как можно использовать веб-сайты Azure для установки и обслуживания корпоративного облачного сайта WordPress, который может справляться с большим количеством посетителей.

##Содержание 

* [Архитектура и планирование](#planning). Предварительные рекомендации по архитектуре, требованиям к сайту и производительности.

* [Пошаговые инструкции](#howto). Создание, развертывание и настройка сайта.

* [Дополнительные ресурсы](#resources). Дополнительные ресурсы и информация.

##<a id="plan"></a>Архитектура и планирование

К базовой установке WordPress предъявляются только два требования.

* **База данных MySQL** доступна в [ClearDB в Магазине Azure][cdbnstore]. Вы можете также управлять собственной установкой MySQL в виртуальных машинах Azure с помощью [Windows][mysqlwindows] или [Linux][mysqllinux].

    > [WACOM.NOTE] ClearDB обеспечивает несколько конфигураций MySQL, каждая из которых имеет разные характеристики производительности. Сведения о предложениях магазина Azure см. в [Магазине Azure][cdbnstore], предложения непосредственно из ClearDB см. в разделе [Цены ClearDB](http://www.cleardb.com/pricing.view).
    
* **PHP 5.2.4 или выше** - веб-сайты Azure в настоящее время включают [версии PHP 5.3, 5.4 и 5.5][phpwebsite].

	> [WACOM.NOTE] Рекомендуется всегда работать на последней версии PHP, чтобы вы были уверены в установке самых последних исправлений безопасности.

###Базовое развертывание

Используя просто базовые требования, можно создать основное решение в регионе Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Хотя это позволит вам масштабировать приложение, создавая несколько экземпляров веб-сайта, все будет размещено в центрах обработки данных на территории одного географического региона. При использовании сайта у посетителей из других регионов может увеличиться время отклика, а при сбое в центрах обработки данных этого региона также произойдет сбой работы вашего приложения.


###Развертывание в нескольких регионах

С помощью [диспетчера трафика][trafficmanager] Azure можно масштабировать сайт WordPress в нескольких географических регионах, предоставив посетителям только один URL-адрес. Все посетители проходят через диспетчер трафика, а затем маршрутизируются на регион в зависимости от конфигурации балансировки нагрузки.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

В каждом регионе сайт WordPress будет масштабироваться на нескольких экземплярах веб-сайта, но масштабирование зависит от региона. Масштабирование регионов с высоким трафиком отличается от масштабирования в регионах с низким трафиком.

Репликацию и маршрутизацию для нескольких баз данных MySQL можно выполнить с помощью [маршрутизатора высокой доступности CDBR High ][cleardbscale] ClearDB (показан слева) или [MySQL Cluster CGE][cge]. 

###Развертывание с хранилищем мультимедиа и кэшированием в нескольких регионах

Если сайт принимает переданные данные или хранит мультимедиа, пользуйтесь хранилищем больших двоичных объектов Azure. Если вам необходимо кэширование, подумайте над использованием [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/ru-ru/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/ru-ru/gallery/store/memcachier/memcachier/) или другим предложением для кэширования данных в [Azure Store](http://azure.microsoft.com/ru-ru/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

Хранилище больших двоичных объектов по умолчанию географически распределено по регионам, поэтому вам не придется беспокоиться о репликации файлов на всех сайтах. Вы можете также включить для хранения больших двоичных объектов сеть Azure [Content Distribution Network (CDN)][cdn], которая распределяет файлы по конечным узлам, находящимся ближе к посетителям.

###Планирование

####Дополнительные требования

Чтобы сделать... | Используйте...
------------------------|-----------
**Передача или хранение больших файлов ** | [Подключаемый модуль WordPress для использования хранилища больших двоичных объектов][storageplugin]
**Отправка электронной почты** | [SendGrid][storesendgrid] и [подключаемый модуль WordPress для использования SendGrid][sendgridplugin]
**Пользовательские имена доменов** | [Пользовательские имена доменов с веб-сайтами Azure][customdomain]
**HTTPS** | [Поддержка HTTPS на веб-сайтах Azure][httpscustomdomain]
**Проверка перед передачей в рабочую среду** | [Поддержка публикации в промежуточной среде для веб-сайтов Azure][staging] <p>Обратите внимание, что переключение сайта с промежуточной на рабочую среду также приведет к переносу конфигурации WordPress. Перед переключением с промежуточной среды в рабочую следует убедиться, что все параметры обновлены и соответствуют требованиям к рабочей среде.</p> 
**Мониторинг и устранение неполадок** | [Ведение журнала диагностики на веб-сайтах Azure][log] и [мониторинг веб-сайтов Azure][monitor]
**Развертывание сайта** | [Развертывание веб-сайтов Azure][deploy]

####Доступность и аварийное восстановление

Чтобы сделать... | Используйте...
------------------------|-----------
**Сайты балансировки нагрузки** или **сайты географического распространения** | [Маршрутизация трафика с помощью диспетчера трафика Azure][trafficmanager]
**Резервное копирование и восстановление ** | [Резервное копирование веб-сайтов Azure][backup] и [восстановление веб-сайтов Azure][restore]

####Производительность

Производительность в облаке достигается в основном через кэширование и развертывание, однако, память, пропускная способность и другие атрибуты веб-сайта размещения также следует учитывать.

Чтобы сделать... | Используйте...
------------------------|-----------
**Понимание возможностей экземпляра веб-сайта** |  [Подробные сведения о ценах и возможностях для разных размеров и режимов веб-сайтов][websitepricing]
**Ресурсы кэширования** | [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/ru-ru/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/ru-ru/gallery/store/memcachier/memcachier/) или другое предложение для кэширования в [Магазине Azure](http://azure.microsoft.com/ru-ru/gallery/store/)
**Масштабирование приложения** | [Масштабирование веб-сайта Azure][websitescale] и [маршрутизатор высокой доступности ClearDB High Availability Routing][cleardbscale]. Если вы выбрали размещение и управление собственной установкой MySQL, то при масштабировании вам необходимо учесть [MySQL Cluster CGE][cge]

####Миграция

Существует два метода миграции существующего сайта WordPress на веб-сайты Azure.

* **[Экспорт WordPress][export]**. Экспорт контента блога, который затем можно будет импортировать на новый сайт WordPress в Azure с помощью [подключаемого модуля экспорта WordPress][import]..

	> [WACOM.NOTE] Хотя этот процесс позволяет переносить контент, он не перемещает подключаемые модули, темы и другие настройки. Его следует установить повторно вручную.

* **Миграция вручную**. [Выполните резервное копирование сайта][wordpressbackup] и [базы данных][wordpressdbbackup], а затем вручную восстановите их на веб-сайте Azure и в связанной базе данных MySQL, чтобы перенести настраиваемые сайты и избежать долгой установки подключаемых модулей, тем и других настроек вручную.

##Практическое руководство

###<a id="create"></a>Создание нового сайта WordPress

1. Воспользуйтесь [Магазином Azure][cdbnstore], чтобы создать базу данных MySQL с размером, который вы определили в разделе [Архитектура и планирование](#planning), в регионах, где вы будете размещать свой сайт.

2. Выполните шаги раздела [Создание веб-сайта WordPress из коллекции в Azure][createwordpress], чтобы создать новый сайт WordPress. При создании сайта установите флажок **Использовать существующую базу данных MySQL**, а затем выберите базу данных, созданную на шаге 1.

Если вы переносите существующий сайт WordPress, после создания нового сайта см. раздел [Миграция существующего сайта WordPress](#migrate).

###<a id="migrate"></a>Миграция существующего сайта WordPress в Azure

Как уже говорилось в разделе [Архитектура и планирование](#planning), существует два способа миграции веб-сайта WordPress.

* **Экспорт и импорт** - для сайтов, не требующих многих настроек, или если вы хотите перенести только контент.

* **Архивация и восстановление** - для сайтов со множеством настроек, если вы хотите перенести все.

Воспользуйтесь одним из следующих разделов для миграции сайта.

####Метод экспорта и импорта

1. Воспользуйтесь [функцией экспорта WordPress][export] для экспорта существующего сайта.

2. Создайте новый веб-сайт, выполнив шаги, приведенные в разделе [Создание нового сайта WordPress](#create).

3. Выполните вход на свой сайт WordPress в веб-сайтах Azure и выберите **Подключаемые модули** -> **Добавить**. Найдите и установите подключаемый модуль **Импорт WordPress**.

4. После установки подключаемого модуля импорта выберите **Сервис** -> **Импорт**, а затем выберите **WordPress**, чтобы вызвать модуль импорта WordPress.

5. На странице **Импорт WordPress** нажмите **Выбрать файл**. Откройте WXR-файл, экспортированный с существующего сайта WordPress, и выберите **Передать файл и импортировать**.

6. Нажмите кнопку **Отправить**. Вы увидите сообщение о том, что импорт успешно выполнен.

8. После выполнения всех этих шагов перезапустите веб-сайт на панели мониторинга веб-сайта на [портале управления Azure][mgmtportal].

После импорта сайта может потребоваться выполнить следующие действия, чтобы включить параметры, которые не содержатся в файле импорта.

Если вы использовали... | Выполните следующие действия...
------------------ | ----------
**Постоянные ссылки** | С панели управления WordPress нового сайта нажмите **Настройки** -> **Постоянные ссылки** и внесите изменения в структуру постоянных ссылок
**Ссылки на изображения и мультимедиа** | Чтобы изменить ссылки, воспользуйтесь подключаемым модулем [Velvet Blues Update URLs][velvet], средством поиска и замены, либо вручную внесите изменения в базу данных
**Темы** | Перейдите в **Оформление** -> **Тема** и при необходимости измените тему веб-сайта
**Меню** | Если тема поддерживает меню, в ссылки на домашнюю страницу может быть все еще встроен старый подкаталог. Перейдите в меню **Оформление** -> **Меню** и внесите изменения

####Метод резервного копирования и восстановления

1. Сделайте резервную копию существующего сайта WordPress, используя сведения в разделе [Резервное копирование WordPress][wordpressbackup].

2. Создайте резервную копию существующей базы данных, используя сведения в разделе [Создание резервной копии базы данных][wordpressdbbackup].

3. Создайте новую базу данных и восстановите резервную копию.

	1. Приобретите новую базу данных в [Магазине Azure][cdbnstore] или установите базу данных MySQL на виртуальной машине [Windows][mysqlwindows] или [Linux][mysqllinux].

	2. С помощью клиента MySQL, например [MySQL Workbench][workbench], подключитесь к новой базе данных и импортируйте базу данных WordPress.

	3. Обновите базу данных, чтобы изменить записи домена на новом домене веб-сайта Azure. Например, mywordpress.azurewebsites.net. Используйте инструмент [поиска и замены скрипта баз данных WordPress][searchandreplace], чтобы безопасно изменить все экземпляры.

4. Создайте новый веб-сайт и опубликуйте резервную копию WordPress.

	1. Создайте новый веб-сайт на [портале управления Azure][mgmtportal] с базой данных, выбрав пункт **Создать** -> **Веб-сайт** -> **Пользовательское создание**. Будет создан пустой сайт.

	2. В резервной копии WordPress найдите файл **wp-config.php** и откройте его в текстовом редакторе. Замените следующие записи на сведения для новой базы данных MySQL.

		* **DB_NAME** - имя пользователя базы данных

		* **DB_USER** - имя пользователя, используемое для доступа к базе данных

		* **DB_PASSWORD** - пароль пользователя

		После внесения этих изменений сохраните и закройте файл **wp-config.php**.

	3. Согласно указаниям в разделе [Как развернуть веб-сайт Azure][deploy], выберите метод развертывания и разверните резервную копию WordPress на своем веб-сайте Azure.

5. После развертывания сайта WordPress вы сможете получить доступ к новому сайту, используя URL-адрес *.azurewebsite.net сайта.

###Настройка сайта

После создания или миграции сайта WordPress используйте следующие сведения, чтобы повысить производительность или включить дополнительные функции.

Чтобы сделать... | Используйте...
------------- | -----------
**Выбрать режим, размер и масштабирование веб-сайта** | [Как масштабировать веб-сайты][websitescale]
**Включить постоянные подключения к базе данных** <p>По умолчанию WordPress не использует постоянные подключения к базе данных, что может привести к тому, что может привести к регулированию подключения к базе данных через несколько подключений.</p>  | <ol><li><p>Edit the <strong>wp-includes/wp-db.php</strong> file.</p></li><li><p>Find the following line.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Replace the previous line with the following.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Find the following line.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Replace the above line with the following.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Save the file <strong>wp-includes/wp-db.php</strong> file and redeploy the site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>Примечание.</h5><p>Эти изменения могут быть перезаписаны при обновлении WordPress.</p><p>Параметры WordPress сбрасываются в значения по умолчанию в результате автоматического обновления, которое можно отключить в файле <strong>wp-config.php</strong>, добавив в него строку <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Другой способ решения этой проблемы - использование WebJob для мониторинга файла <strong>wp-db.php</strong> и внесения приведенного выше обновления при каждом его обновлении. Подробнее см. в разделе <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Введение в WebJobs</a>.</p></div>
**Повышение производительности** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Отключите cookie-файлы ARR</a> - это позволит повысить производительность работы WordPress на нескольких экземплярах веб-сайтов</p></li><li><p>Включите кэширование. <a href="http://msdn.microsoft.com/ru-ru/library/azure/dn690470.aspx">Redis cache</a> (предварительная версия) может быть использован в сочетании с <a href="https://wordpress.org/plugins/redis-object-cache/">подключаемым модулем кэширования объектов Redis для WordPress</a>, либо воспользуйтесь другим предложением из <a href="http://azure.microsoft.com/ru-ru/gallery/store/">Магазина Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Как ускорить работу WordPress с помощью Wincache</a> - Wincache по умолчанию включен для веб-сайтов</p></li><li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/">Масштабируйте свой веб-сайт Azure</a> и воспользуйтесь <a href="http://www.cleardb.com/developers/cdbr/introduction">маршрутизатором высокой доступности ClearDB</a> или <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Использование больших двоичных объектов для хранения данных** | <ol><li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/">Создайте учетную запись хранилища Azure</a></p></li><li><p>Как <a href="http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/">использовать сеть Content Distribution Network (CDN)</a> для географического распространения данных, хранящихся в больших двоичных объектах.</p></li><li><p>Установите и настройте <a href="https://wordpress.org/plugins/windows-azure-storage/">подключаемый модуль хранилища Azure для WordPress</a>.</p><p>Подробности об установке и настройке подключаемого модуля см. в <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">руководстве пользователя</a>.</p> </li></ol>
**Включение электронной почты** | <ol><li><p><a href="http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/">Включите SendGrid, </a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">установив подключаемый модуль SendGrid для WordPress</a></p></li></ol> из Магазина Azure
**Настройка пользовательского имени домена** | [Используйте пользовательское имя домена для веб-сайта Azure][customdomain]
**Включение HTTPS для пользовательского имени домена** | [Используйте HTTPS для веб-сайта Azure][httpscustomdomain]
**Балансировка нагрузи и географическое распространение сайта** | [Маршрутизируйте трафик с помощью диспетчера трафика Azure][trafficmanager]. Если вы используете пользовательский домен, сведения об использовании диспетчера трафика с пользовательскими доменными именами см. в разделе [Использование пользовательского имени домена для веб-сайта Azure][customdomain].
**Включение автоматического резервного копирования веб-сайта** | [Резервное копирование веб-сайтов Azure][backup]
**Включение ведение журнала диагностики** | [Включите ведение журнала диагностики для веб-сайтов][log]

##<a href="resources"></a>Дополнительные ресурсы

* [Оптимизация WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Преобразование сайта WordPress в мультисайт](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Мастер обновления ClearDB для Azure](http://www.cleardb.com/store/azure/upgrade)

* [Размещение WordPress в подпапке веб-сайта Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Пошаговое руководство. Создание сайта WordPress с помощью Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Размещение существующего блога WordPress в Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Включение постоянных ссылок в WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Миграция и запуск блога WordPress на веб-сайтах Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Бесплатный запуск WordPress на веб-сайтах Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress в Azure за 2 минуты или меньше](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Перемещение блога WordPress в Azure - часть 1: создание блога WordPress в Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Перемещение блога WordPress в Azure - часть 2: перемещение контента](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Перемещение блога WordPress в Azure - часть 3: настройка пользовательского домена](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Перемещение блога WordPress в Azure - часть 4: постоянные ссылки и правила переопределения URL-адресов](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Перемещение блога WordPress в Azure - часть 5: перемещение подпапки в корень](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Настройка веб-сайта WordPress в учетной записи Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Поддержка WordPress в Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Советы для WordPress в Azure](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/ru-ru/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/ru-ru/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/ru-ru/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/ru-ru/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[импорт]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: (http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-web-site-gallery/)
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/ru-ru/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/

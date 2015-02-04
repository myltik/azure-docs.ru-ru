<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress корпоративного уровня на веб-сайтах Azure" description="Узнайте, как разместить сайт WordPress корпоративного уровня на веб-сайтах Azure" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/11/2014" ms.author="tomfitz" />

#WordPress корпоративного уровня на веб-сайтах Azure

Веб-сайты Azure предоставляют масштабируемую, безопасную и простую в использовании среду для критически важных, крупных масштабируемых сайтов [WordPress][wordpress]. Корпорация Майкрософт запускает сайты корпоративного класса, такие как блоги [Office][officeblog] и [Bing][bingblog]. В этом документе показано, как можно использовать веб-сайты Azure для установки и обслуживания корпоративного облачного сайта WordPress, который может обрабатывать большой объем посетителей.

##Содержание 

* [Архитектура и планирование](#planning) - предварительные рекомендации по архитектуре, требованиям и производительности.

* [Практическое руководство.](#howto) Создание, развертывание и настройка сайта.

* [Дополнительные ресурсы](#resources) - информация о дополнительных ресурсах

##<a id="plan"></a>Архитектура и планирование

К базовой установке WordPress предъявляются только два требования.

* **База данных MySQL** - доступна в [ClearDB в Магазине Azure][cdbnstore]. Вы также можете управлять собственной установкой MySQL в виртуальных машинах Azure с помощью [Windows][mysqlwindows] или [Linux][mysqllinux].

    > [WACOM.NOTE] ClearDB предоставляет несколько конфигураций MySQL с разными характеристиками производительности для каждой конфигурации. Перейдите в [Магазин Azure][cdbnstore], чтобы получить информацию о доступных предложениях от магазина Azure или о [стоимости хранения для ClearDB](http://www.cleardb.com/pricing.view) непосредственно от ClearDB.
    
* **PHP 5.2.4 или выше** - веб-сайты Azure в настоящее время предоставляют [версии PHP 5.3, 5.4 и 5.5][phpwebsite].

	> [WACOM.NOTE] Рекомендуется всегда выполнять запуск на последней версии PHP, чтобы гарантированно получить все исправления безопасности.

###Базовое развертывание

Используя просто базовые требования, можно создать основное решение в регионе Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Хотя это позволит вам развернуть приложение, создав несколько экземпляров веб-сайта, все сведения размещается в центрах обработки данных в определенном географическом регионе. При использовании сайта у посетителей из других регионов может быть увеличен отклик, а при сбое центров обработки данных в этом регионе также произойдет сбой приложения.


###Развертывание в нескольких регионах

С помощью [диспетчера трафика][trafficmanager] Azure можно масштабировать сайт WordPress в нескольких географических регионах, указав только один URL-адрес для посетителей. Все посетители проходят через диспетчер трафика, а затем перенаправляются в регион в зависимости от конфигурации балансировки нагрузки.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

В каждом регионе сайт WordPress будет масштабироваться на нескольких экземплярах веб-сайта, но масштабирование зависит от региона. Масштабирование регионов с высоким трафиком отличается от масштабирования в регионах с низким трафиком.

Репликацию и маршрутизацию на несколько баз данных MySQL можно выполнить с помощью [маршрутизатора высокой доступности CDBR][cleardbscale] ClearDB (показан слева) или [MySQL Cluster CGE][cge]. 

###Развертывание с хранилищем мультимедиа и кэшированием в нескольких регионах

Если сайт принимает отправки или размещает файлы мультимедиа, используйте хранилище больших двоичных объектов Azure. Если вам необходимо кэширование, рассмотрите варианты использования [Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/ru-ru/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/ru-ru/gallery/store/memcachier/memcachier/) или другие предложения по кэшированию в [Магазине Azure](http://azure.microsoft.com/ru-ru/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

Хранилище больших двоичных объектов географически распределено по регионам по умолчанию, поэтому вам не нужно беспокоиться о репликации файлов на всех сайтах. Можно также включить Azure [Content Distribution Network (CDN)][cdn] для хранилища больших двоичных объектов, которое распределяет файлы на конечные узлы, находящиеся ближе к посетителям.

###Планирование

####Дополнительные требования

Для этого... | Используйте это...
------------------------|-----------
**Скачивание или хранение больших файлов** | [Подключаемый модуль WordPress для использования хранилища больших двоичных объектов][storageplugin]
**Отправка электронной почты** | [SendGrid][storesendgrid] и [подключаемый модуль WordPress для использования SendGrid][sendgridplugin]
**Настраиваемые имена доменов** | [Настраиваемые имена доменов на веб-сайтах Azure][customdomain]
**HTTPS** | [Поддержка HTTPS на веб-сайтах Azure][httpscustomdomain]
**Предварительная проверка** | [Предварительная публикация на веб-сайтах Azure][staging] <p>Обратите внимание, что переключение сайта с промежуточной на рабочую публикацию также перемещает конфигурацию WordPress. Перед переключением с промежуточного сайта на рабочий следует убедиться, что все параметры обновлены и соответствуют требованиям к рабочему сайту.</p> 
**Мониторинг и устранение неполадок** | [Сбор данных диагностики на веб-сайтах Azure][log] и [Мониторинг веб-сайтов Azure][monitor]
**Развертывание сайта** | [Развертывание веб-сайта Azure][deploy]

####Доступность и аварийное восстановление

Для этого... | Используйте это...
------------------------|-----------
**Балансировка нагрузки на сайт** или **геораспределение сайтов** | [Маршрутизация трафика с помощью диспетчера трафика Azure][trafficmanager]
**Резервное копирование и восстановление** | [Резервное копирование веб-сайтов Azure][backup] и [Восстановление веб-сайтов Azure][restore]

####Производительность

Производительность в облаке достигается в основном через кэширование и развертывание, однако, память, пропускная способность и другие атрибуты веб-сайта размещения также следует учитывать.

Для этого... | Используйте это...
------------------------|-----------
**Характеристики и возможности веб-сайта** |  [Информация о стоимости, включая характеристики размеров и режимов веб-сайта][websitepricing]
**Кэширование** | [Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/ru-ru/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/ru-ru/gallery/store/memcachier/memcachier/) или другие предложения по кэшированию в [Магазине Azure](http://azure.microsoft.com/ru-ru/gallery/store/)
**Масштабирование приложения** | [Масштабирование веб-сайта Azure][websitescale] и [Маршрутизация высокой доступности ClearDB][cleardbscale]. Если вы решите разместить собственную установку MySQL и управлять ей, следует рассмотреть [MySQL Cluster CGE][cge]

####Миграция

Существует два метода миграции существующего сайта WordPress на веб-сайты Azure.

* **[Экспорт WordPress][export]**. Экспорт содержимого блога, которое затем можно будет импортировать на новый сайт WordPress в Azure с помощью [подключаемого модуля импорта WordPress][import].

	> [WACOM.NOTE] Этот процесс позволяет переносить содержимое, но он не переносит подключаемые модули, темы или другие настройки. Его следует установить повторно вручную.

* **Миграция вручную**. [Выполните резервное копирование сайта][wordpressbackup] и [базы данных][wordpressdbbackup], затем вручную восстановите их на веб-сайте Azure и в связанной базе данных MySQL, чтобы перенести настраиваемые сайты и избежать долгой установки подключаемых модулей, тем и других настроек вручную.

##Практическое руководство

###<a id="create"></a>Создание нового сайта WordPress

1. Воспользуйтесь [Магазином Azure][cdbnstore], чтобы создать базу данных MySQL с указанным в разделе [Архитектура и планирование] размером(#planning) и в нужном регионе, в котором размещен ваш сайт.

2. Выполните инструкции в разделе [Создание веб-сайта WordPress из коллекции в Azure][createwordpress], чтобы создать новый сайт WordPress. При создании сайта установите флажок **Использовать существующую базу данных MySQL** и выберите базу данных, созданную в шаге 1.

Если вы переносите существующий сайт WordPress, после создания нового сайта см. раздел [Миграция существующего сайта WordPress](#migrate) .

###<a id="migrate"></a>Миграция существующего сайта WordPress в Azure

Как было отмечено в разделе [Архитектура и планирование](#planning) , существует два способа переноса веб-сайта WordPress.

* **Экспорт и импорт** - для сайтов, не требующих детальной настройки, или если нужно перенести только содержимое.

* **Архивация и восстановление** - для сайтов со множеством настроек, где требуется переместить все.

Воспользуйтесь одним из следующих разделов для миграции сайта.

####Метод экспорта и импорта

1. Используйте [экспорт WordPress][export], чтобы экспортировать существующий сайт.

2. Создайте новый веб-сайт, следуя шагам в разделе [Создание нового сайта WordPress](#create) .

3. Войдите на сайт WordPress в службе веб-сайтов Azure и выберите **Подключаемые модули** > **Создать**. Найдите и установите подключаемый модуль **Программа импорта WordPress**.

4. После установки подключаемого модуля программы импорта последовательно выберите пункты меню **Сервис** > **Импорт**, а затем выберите **WordPress**, чтобы использовать подключаемый модуль программы импорта WordPress.

5. На странице **Импорт WordPress** щелкните кнопку **Выбрать файл**. Откройте WXR-файл, экспортированный из существующего сайта WordPress, и нажмите кнопку **Отправить файл и импортировать**.

6. Нажмите кнопку **Отправить**. Появится сообщение, что импорт успешно выполнен.

8. После выполнения этих действий перезапустите веб-сайт на панели мониторинга веб-сайта на [портале управления Azure][mgmtportal].

После импорта сайта может потребоваться выполнить следующие действия, чтобы включить параметры, которые не содержатся в файле импорта.

Если вы использовали... | Для этого...
------------------ | ----------
**Постоянные ссылки** | На панели мониторинга WordPress нового сайта щелкните кнопку **Параметры** > **Постоянные ссылки** и обновите структуру постоянных ссылок
**Ссылки на изображения и файлы** | Чтобы обновить ссылки на новое расположение, используйте [подключаемый модуль обновления URL-адресов Velvet Blues][velvet], инструмент поиска и замены, или обновите их вручную в базе данных
**Темы** | Выберите **Внешний вид** > **Тема** и обновите тему веб-сайта
**Меню** | Если тема поддерживает меню, ссылки на домашнюю страницу могут содержат старый подкаталог. Выберите **Внешний вид** > **Меню** и обновите их

####Метод резервного копирования и восстановления

1. Создайте резервную копию существующего сайта WordPress, используя информацию из раздела [Резервное копирование WordPress][wordpressbackup].

2. Создайте резервную копию существующей базы данных, используя информацию из раздела [Резервное копирование базы данных][wordpressdbbackup].

3. Создайте новую базу данных и восстановите резервную копию.

	1. Приобретите новую базу данных в [Магазине Azure][cdbnstore] либо настройте базу данных MySQL в виртуальной машине [Windows][mysqlwindows] или [Linux][mysqllinux].

	2. С помощью клиента MySQL, например, [MySQL Workbench][workbench], подключитесь к новой базе данных и импортируйте базу данных WordPress.

	3. Обновите базу данных, чтобы изменить записи домена на новом домене веб-сайта Azure. Например, mywordpress.azurewebsites.net. Используйте инструмент [поиска и замены для сценария баз данных WordPress][searchandreplace], чтобы безопасно изменить все экземпляры.

4. Создайте новый веб-сайт и опубликуйте резервную копию WordPress.

	1. Создайте новый веб-сайт с базой данных на [портале управления Azure][mgmtportal], выбрав **Создать** > **Веб-сайт** > **Настраиваемое создание**. Будет создан пустой сайт.

	2. В резервной копии WordPress найдите файл **wp-config.php** и откройте его в редакторе. Замените следующие записи на сведения для новой базы данных MySQL.

		* **DB_NAME** - имя пользователя базы данных.

		* **DB_USER** - имя пользователя для доступа к базе данных.

		* **DB_PASSWORD** - пароль пользователя.

		После изменения этих записей сохраните и закройте файл **wp-config.php**.

	3. Используйте информацию из раздела [Развертывание веб-сайта Azure][deploy], чтобы включить метод развертывания, который вы хотите использовать, а затем разверните резервную копию WordPress на веб-сайте Azure.

5. После развертывания сайта WordPress вы сможете получить доступ к новому сайту, используя URL-адрес *.azurewebsite.net сайта.

###Настройка сайта

После создания или миграции сайта WordPress используйте следующие сведения, чтобы повысить производительность или включить дополнительные функции.

Для этого... | Используйте это...
------------- | -----------
**Настройка режима и размера веб-сайта, включение масштабирования** | [Как использовать масштабирование для веб-сайтов][websitescale]
**Включение постоянных подключений базы данных** <p>По умолчанию WordPress не использует постоянные подключения базы данных, что может вызвать замедление подключения к базе данных после нескольких подключений.</p>  | <ol><li><p>Отредактируйте файл <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Найдите следующую строку.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Замените предыдущую строку на приведенную ниже.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Найдите следующую строку.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Замените строку выше на приведенную ниже.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Сохраните файл<strong>wp-includes/wp-db.php</strong>и повторно разверните сайт.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>ПРИМЕЧАНИЕ.</h5><p>Эти изменения можно перезаписать после обновления WordPress.</p><p>По умолчанию WordPress обновляется автоматически; это можно отключить, изменив файл <strong>wp-config.php</strong> и добавив <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Другой способ настройки обновлений - использование инструмента WebJob, который отслеживает файл <strong>wp-db.php</strong> и вносит необходимые изменения при каждом обновлении файла. См. раздел <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Введение в WebJobs</a> для получения дополнительной информации.</p></div>
**Повышение производительности** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Отключение ARR-файла cookie</a> - может повысить производительность при запуске WordPress на нескольких экземплярах веб-сайта.</p></li><li><p>Включите кэширование. <a href="http://msdn.microsoft.com/ru-ru/library/azure/dn690470.aspx">Кэш Redis</a> (предварительная версия) может использоваться с <a href="https://wordpress.org/plugins/redis-object-cache/">подключаемым модулем WordPress для объекта кэширования Redis</a>или воспользуйтесь другими предложениями по кэшированию из <a href="http://azure.microsoft.com/ru-ru/gallery/store/">Магазин Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Как ускорить работу WordPress с помощью Wincache</a> - Wincache по умолчанию включен для веб-сайтов</p></li><li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/">Масштабирование веб-сайта Azure</a> и использует <a href="http://www.cleardb.com/developers/cdbr/introduction">маршрутизацию высокой доступности CDBR</a> или <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Использование больших двоичных объектов для хранилища** | <ol><li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/">Создание учетной записи хранения Azure</a></p></li><li><p>Узнайте, как <a href="http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/">использовать Content Distribution Network (CDN)</a> для геораспределения данных, хранимых в больших двоичных объектах.</p></li><li><p>Установите и настройте <a href="https://wordpress.org/plugins/windows-azure-storage/">подключаемый модуль WordPress для хранилища Azure</a>.</p><p>Детальную информацию о настройке и конфигурации подключаемого модуля см. в <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">руководстве пользователя</a>.</p> </li></ol>
**Включение электронной почты** | <ol><li><p><a href="http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/">Включение SendGrid с помощью Магазина Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Установка подключаемого модуля SendGrid для WordPress</a></p></li></ol>
**Настройка пользовательского доменного имени** | [Настройте пользовательское доменное имя с помощью веб-сайта Azure][customdomain]
**Включение HTTPS для пользовательского доменного имени** | [Используйте HTTPS с помощью веб-сайта Azure][httpscustomdomain]
**Балансировка нагрузки или настройка геораспределенного сайта** | [Настройте маршрутизацию трафика с помощью диспетчера трафика Azure][trafficmanager]. Если вы используете пользовательский домен, информация об использовании диспетчера трафика с настраиваемыми доменными именами см. в разделе [Использование пользовательского доменного имени с веб-сайтом Azure][customdomain]
**Включение автоматического резервного копирования веб-сайта** | [Резервное копирование веб-сайтов Azure][backup]
**Включение сбор данных диагностики** | [Включение сбора данных диагностики для веб-сайтов][log]

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

* [Перемещение блога WordPress в Azure - часть 1: Создание блога WordPress в Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Перемещение блога WordPress в Azure - часть 2: перемещение содержимого](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Перемещение блога WordPress в Azure - часть 3: Настройка пользовательского домена](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Перемещение блога WordPress в Azure - часть 4: Постоянные ссылки и правила переопределения URL-адресов](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Перемещение блога WordPress в Azure - часть 5: Перемещение подпапки в корень](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

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
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-web-site-gallery/
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

<!--HONumber=35.1-->

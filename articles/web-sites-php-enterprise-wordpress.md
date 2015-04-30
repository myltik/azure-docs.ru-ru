<properties 
	pageTitle="Сайт WordPress корпоративного класса в службе приложений Azure" 
	description="Информация о размещении сайта WordPress корпоративного класса в службе приложений Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

#Сайт WordPress корпоративного класса в службе приложений Azure

Служба приложений Azure предоставляет масштабируемую, безопасную и простую в использовании среду для критически важных, крупных масштабируемых сайтов [WordPress][wordpress]. Корпорация Microsoft также администрирует сайты корпоративного класса, такие как блоги [Office][officeblog] и [Bing][bingblog]. В этом документе показано, как можно использовать веб-приложения службы приложений Azure для установки и обслуживания корпоративного облачного сайта WordPress, который может принимать большое количество посетителей.

## Архитектура и планирование

К базовой установке WordPress предъявляются только два требования.

* **MySQL Database** - доступна в [ClearDB в Магазине Azure][cdbnstore]. Вы также можете управлять собственной установкой MySQL в виртуальных машинах Azure с помощью [Windows][mysqlwindows] или [Linux][mysqllinux].

    > [AZURE.NOTE] ClearDB предоставляет несколько конфигураций MySQL с разными характеристиками производительности для каждой конфигурации. Сведения о предложениях магазина Azure см. в [Магазине Azure][cdbnstore], предложения непосредственно из ClearDB см. в статье [Цены ClearDB](http://www.cleardb.com/pricing.view).
    
* **PHP 5.2.4 или выше** - служба приложений Azure в настоящее время предоставляет версии [PHP 5.3, 5.4, и 5.5][phpwebsite].

	> [AZURE.NOTE] Рекомендуется всегда выполнять запуск на последней версии PHP, чтобы гарантированно получить все исправления безопасности.

###Базовое развертывание

Используя просто базовые требования, можно создать основное решение в регионе Azure.

![Веб-приложение Azure и база данных MySQL в одном регионе Azure][basic-diagram]

Хотя это позволит вам развернуть приложение, создав несколько экземпляров веб-приложений сайта, все сведения размещается в центрах обработки данных в определенном географическом регионе. При использовании сайта у посетителей из других регионов может быть увеличен отклик, а при сбое центров обработки данных в этом регионе также произойдет сбой приложения.


###Развертывание в нескольких регионах

С помощью [диспетчера трафика][trafficmanager] Azure можно масштабировать сайт WordPress в нескольких географических регионах, предоставив посетителям только один URL-адрес. Все посетители заходят на сайт через диспетчер трафика, а затем перенаправляются в регион в зависимости от конфигурации балансировки нагрузки.

![Веб-приложение Azure, размещенное в нескольких регионах, которое использует маршрутизатор высокой доступности CDBR для маршрутизации MySQL в разных регионах][multi-region-diagram]

В каждом регионе сайт WordPress будет масштабироваться на нескольких экземплярах веб-приложений, но масштабирование зависит от региона. Масштабирование регионов с высоким трафиком отличается от масштабирования в регионах с низким трафиком.

Репликацию и маршрутизацию для нескольких баз данных MySQL можно выполнить с помощью [маршрутизатора высокой доступности CDBR High][cleardbscale] ClearDB (показан слева) или [MySQL Cluster CGE][cge]. 

###Развертывание с хранилищем мультимедиа и кэшированием в нескольких регионах

Если сайт принимает отправки или размещает файлы мультимедиа, используйте хранилище больших двоичных объектов Azure. Если необходимо кэширование, рассмотрите [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) или другие службы кэширования в [Магазине Azure](http://azure.microsoft.com/gallery/store/).

![Веб-приложение Azure, размещенное в нескольких регионах с помощью маршрутизатора высокой доступности CDBR для MySQL, с управляемой службой кэша, хранилищем больших двоичных объектов и CDN][performance-diagram]

Хранилище больших двоичных объектов географически распределено по регионам по умолчанию, поэтому вам не нужно беспокоиться о репликации файлов на всех сайтах. Вы можете также включить для хранения больших двоичных объектов сеть Azure [Content Distribution Network (CDN)][cdn], которая распределяет файлы по конечным узлам, находящимся ближе к посетителям.

###Планирование

####Дополнительные требования

Для этого... | Используйте это...
------------------------|-----------
**Передача или хранение больших файлов** | [Подключаемый модуль WordPress для использования хранилища больших двоичных объектов][storageplugin]
**Отправка электронной почты** | [SendGrid][storesendgrid] и [подключаемый модуль WordPress для использования SendGrid][sendgridplugin]
**Личные доменные имена** | [Настройка личного доменного имени в службе приложений Azure][customdomain]
**HTTPS** | [Включение протокола HTTPS для веб-приложения в службе приложений Azure][httpscustomdomain]
**Подготовительная проверка** | [Настройка промежуточных сред для веб-приложений в службе приложений Azure][staging] <p>Обратите внимание, что переключение веб-приложения с промежуточной на рабочую публикацию также перемещает конфигурацию WordPress. Перед переключением с промежуточного приложения на рабочее следует убедиться, что все параметры обновлены и соответствуют требованиям к рабочему приложению.</p> 
**Мониторинг и устранение неполадок** | [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure][log] и [Мониторинг веб-приложений в службе приложений Azure][monitor]
**Развертывание сайта** | [Развертывание веб-приложения в службе приложений Azure][deploy]

####Доступность и аварийное восстановление

Для этого... | Используйте это...
------------------------|-----------
**Балансировка нагрузки на сайт** или **геораспределение сайтов** | [Маршрутизация трафика с помощью диспетчера трафика Azure][trafficmanager]
**Резервное копирование и восстановление** | [Резервное копирование веб-приложения в службе приложений Azure][backup] и [Восстановление веб-приложения в службе приложений Azure][restore]

####Производительность

Производительность в облаке достигается в основном через кэширование и развертывание, однако, память, пропускная способность и другие атрибуты размещения веб-приложений также следует учитывать.

Для этого... | Используйте это...
------------------------|-----------
**Основная информация о возможностях экземпляра службы приложений** | [Сведения о ценах и возможности уровней службы приложений][websitepricing]
**Ресурсы кэша** | [Redis cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) или другие службы кэширования в [Магазине Azure](/gallery/store/)
**Масштабирование приложения** | [Масштабирование веб-приложения в службе приложений Azure][websitescale] и [Маршрутизация высокой доступности ClearDB][cleardbscale]. Если вы выбрали размещение и управление собственной установкой MySQL, то при развертывании вам необходимо учесть [MySQL Cluster CGE][cge].

####Миграция

Существует два метода миграции существующего сайта WordPress в веб-службу Azure.

* **[Экспорт WordPress][export]**. Экспорт содержимого блога, которое затем можно будет импортировать на новый сайт WordPress в службе приложений Azure с помощью [подключаемого модуля импорта WordPress][import].

	> [AZURE.NOTE] Этот процесс позволяет переносить содержимое, но он не переносит подключаемые модули, темы или другие настройки. Его следует установить повторно вручную.

* **Миграция вручную**. [Создайте резервную копию сайта][wordpressbackup] и [базы данных][wordpressdbbackup], затем вручную восстановите их в веб-приложении службы приложений Azure и в связанной базе данных MySQL, чтобы перенести настраиваемые сайты и избежать долгой установки подключаемых модулей, тем и других настроек.

## Пошаговые инструкции

### Создание нового сайта WordPress

1. Воспользуйтесь [Магазином Azure][cdbnstore], чтобы создать базу данных MySQL размером, который указан в разделе [Архитектура и планирование](#planning) и в нужном регионе, в котором размещен ваш сайт.

2. Следуйте инструкциям в статье [Создание веб-приложения WordPress в службе приложений Azure][createwordpress], чтобы создать новое веб-приложение WordPress. Во время создании веб-приложения установите флажок **Использовать существующую базу данных MySQL** и выберите базу данных, созданную в шаге 1.

Если вы переносите существующий сайт WordPress, см. раздел [Миграция существующего сайта WordPress в Azure](#Migrate-an-existing-WordPress-site-to-Azure) после создания нового веб-приложения.

### Миграция существующего сайта WordPress в Azure

Как было отмечено в разделе [Архитектура и планирование](#planning) , существует два способа переноса веб-сайта WordPress.

* **Экспорт и импорт** - для сайтов, не требующих детальной настройки, или если нужно перенести только содержимое.

* **Архивация и восстановление** - для сайтов со множеством настроек, где требуется переместить все.

Воспользуйтесь одним из следующих разделов для миграции сайта.

####Метод экспорта и импорта

1. Используйте [функцию экспорта WordPress][export], чтобы экспортировать существующий сайт.

2. Создайте новое веб-приложение, следуя инструкциям в статье [Создание нового сайта WordPress](#Create-a-new-WordPress-site) .

3. Войдите на сайт WordPress в службе веб-приложений и выберите **Подключаемые модули** -> **Создать**. Найдите и установите подключаемый модуль **Программа импорта WordPress**.

4. После установки подключаемого модуля программы импорта последовательно выберите пункты меню **Сервис** > **Импорт**, а затем выберите **WordPress**, чтобы использовать подключаемый модуль программы импорта WordPress.

5. На странице **Импорт WordPress** нажмите кнопку **Выбрать файл**. Откройте WXR-файл, экспортированный из существующего сайта WordPress, и нажмите кнопку **Отправить файл и импортировать**.

6. Нажмите кнопку **Submit** (Отправить). Появится сообщение, что импорт успешно выполнен.

8. После выполнения этих действий перезапустите сайт из колонки веб-приложений на [портале Azure][mgmtportal].

После импорта сайта может потребоваться выполнить следующие действия, чтобы включить параметры, которые не содержатся в файле импорта.

Если вы использовали... | Для этого...
------------------ | ----------
**Постоянные ссылки** | На панели мониторинга WordPress нового сайта нажмите кнопку **Параметры** > **Постоянные ссылки** и обновите структуру постоянных ссылок
**Ссылки на изображения и мультимедиа** | Чтобы изменить ссылки, воспользуйтесь подключаемым модулем [Velvet Blues Update URLs][velvet], средством поиска и замены, либо вручную внесите изменения в базу данных
**Теми** | Выберите **Внешний вид** -> **Тема** и обновите тему сайта
**Меню** | Если тема поддерживает меню, ссылки на домашнюю страницу могут содержат старый подкаталог. Выберите **Внешний вид** > **Меню** и обновите их

####Метод резервного копирования и восстановления

1. Создайте резервную копию существующего сайта WordPress, используя сведения в статье [Резервное копирование WordPress][wordpressbackup].

2. Создайте резервную копию существующей базы данных, используя сведения в статье [Резервное копирование базы данных][wordpressdbbackup].

3. Создайте новую базу данных и восстановите резервную копию.

	1. Купите новую базу данных в [Магазине Azure][cdbnstore] или настройте базу данных MySQL в виртуальной машине [Windows][mysqlwindows] или [Linux][mysqllinux].

	2. С помощью клиента MySQL, например [MySQL Workbench][workbench], подключитесь к новой базе данных и импортируйте базу данных WordPress.

	3. Обновите базу данных, чтобы изменить записи домена на новом домене службы приложений Azure. Например, mywordpress.azurewebsites.net. Используйте инструмент [поиска и замены для сценария баз данных WordPress][searchandreplace], чтобы безопасно изменить все экземпляры.

4. Создайте новое веб-приложение на портале Azure и опубликуйте резервную копию сайта WordPress.

	1. Создайте новое веб-приложение на [портале Azure][mgmtportal] с базой данных. Для этого щелкните **Создать** -> **Интернет и мобильные устройства** -> **Azure Marketplace** -> **Веб-приложения** -> **Веб-приложение + SQL** (или **Веб-приложение + MySQL**) -> **Создать**. Настройте все обязательные параметры, чтобы создать пустое веб-приложение.

	2. В резервной копии WordPress найдите файл **wp-config.php** и откройте его в редакторе. Замените следующие записи на сведения для новой базы данных MySQL.

		* **DB_NAME** - имя пользователя базы данных

		* **DB_USER** - имя пользователя для доступа к базе данных

		* **DB_PASSWORD** - пароль пользователя

		После изменения этих записей сохраните и закройте файл **wp-config.php**.

	3. Используйте информацию из раздела [Развертывание веб-приложения в службе приложений Azure][deploy], чтобы включить метод развертывания, который вы хотите использовать, а затем разверните резервную копию сайта WordPress в веб-приложении в службе приложений Azure.

5. После развертывания сайта WordPress вы сможете получить доступ к новому сайту (св виде веб-приложения службы приложений), используя URL-адрес *.azurewebsite.net сайта.

###Настройка сайта

После создания или миграции сайта WordPress используйте следующие сведения, чтобы повысить производительность или включить дополнительные функции.

Для этого... | Используйте это...
------------- | -----------
**Настройка режима и размера плана службы приложений, а также включение масштабирования** | [Масштабирование веб-приложения в службе приложений Azure][websitescale]
**Включение постоянных подключений базы данных** <p>По умолчанию WordPress не использует постоянные подключения базы данных, что может вызвать замедление подключения к базе данных после нескольких подключений.</p>  | <ol><li><p>Отредактируйте файл <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Найдите следующую строку.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Замените предыдущую строку на приведенную ниже.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Найдите следующую строку.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Замените строку выше на приведенную ниже.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Сохраните файл <strong>wp-includes/wp-db.php</strong> и повторно разверните сайт.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Эти изменения можно перезаписать после обновления WordPress.</p><p>По умолчанию WordPress обновляется автоматически. Эту настройку можно отключить, изменив файл <strong>wp-config.php</strong> file and adding <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Другой способ адресации обновления - использовать веб-задание, которое отслеживает файл <strong>wp-db.php</strong> и выполняет указанные выше изменения при каждом обновлении файла. Дополнительную информацию см. в разделе <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Общая информация о заданиях WebJob</a>.</p></div>
**Повышение производительности** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Отключение ARR-файла cookie</a> может повысить производительность при запуске WordPress на нескольких экземплярах веб-приложений.</p></li><li><p>Включите кэширование. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (предварительная версия) можно использовать с <a href="https://wordpress.org/plugins/redis-object-cache/">подключаемым модулем WordPress кэша объектов Redis</a> или можно выбрать другую службу кэширования в <a href="/gallery/store/">Магазине Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Ускорение работы WordPress с Wincache</a> - расширение Wincache включено для веб-приложений по умолчанию.</p></li><li><p><a href="../web-sites-scale/">Масштабируйте веб-приложение в службе приложений Azure</a> и используйте <a href="http://www.cleardb.com/developers/cdbr/introduction">маршрутизацию высокой доступности ClearDB</a> или <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Использование больших двоичных объектов для хранилища** | <ol><li><p><a href="../storage-create-storage-account/">Создание учетной записи хранения Azure</a></p></li><li><p><a href="../cdn-how-to-use/">Использование Content Distribution Network (CDN)</a> для геораспределения данных, сохраненных в больших двоичных объектах.</p></li><li><p>Установите и настройте <a href="https://wordpress.org/plugins/windows-azure-storage/">службу хранилища Azure для подключаемого модуля WordPress</a>.</p><p>Подробную информацию о настройке и конфигурации для подключаемого модуля см. в <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">руководстве пользователя</a>.</p> </li></ol>
**Включение почты** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Включение SendGrid с помощью Магазина Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Установка подключаемого модуля SendGrid для WordPress</a></p></li></ol>
**Настройка личного доменного имени** | [Настройка личного доменного имени в службе приложений Azure][customdomain]
**Включение протокола HTTPS для личного доменного имени** | [Включение протокола HTTPS для веб-приложения в службе приложений Azure][httpscustomdomain]
**Балансировка нагрузки или настройка геораспределенного сайта** | [Настройте маршрутизацию трафика с помощью диспетчера трафика Azure][trafficmanager]. Если вы используете личный домен, см. информацию об использовании диспетчера трафика с личными доменными именами в статье [Настройка личного доменного имени в службе приложений Azure][customdomain]
**Включение автоматического резервного копирования** | [Резервное копирование веб-приложения в службе приложений Azure][backup]
**Включение ведения журнала диагностики** | [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure][log]

## Дальнейшие действия

* [Оптимизация WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Преобразование WordPress в Мультисайт в службе приложений Azure](web-sites-php-convert-wordpress-multisite.md)

* [Мастер обновления ClearDB для Azure](http://www.cleardb.com/store/azure/upgrade)

* [Размещение WordPress в подпапке веб-приложения в службе приложений Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Пошаговый учебник. Создание сайта WordPress с помощью Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Размещение существующего блога WordPress в Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Включение постоянных ссылок в WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Миграция и запуск блога WordPress в службе приложений Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Бесплатный запуск блога WordPress в службе приложений Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [Блог WordPress в Azure за 2 минуты](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Перемещение блога WordPress в Azure - часть 1: Создание блога WordPress в Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Перемещение блога WordPress в Azure - часть 2: перемещение контента](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Перемещение блога WordPress в Azure - часть 3: настройка личного домена](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Перемещение блога WordPress в Azure - часть 4: постоянные ссылки и правила переопределения URL-адресов](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Перемещение блога WordPress в Azure - часть 5: перемещение подпапки в корень](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Настройка веб-приложения WordPress в учетной записи Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Поддержка WordPress в Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Советы для WordPress в Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: install-configure-powershell.md
[xplat-cli]: xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: cdn-how-to-use.md

<!--HONumber=52-->
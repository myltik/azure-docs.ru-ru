<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## WordPress корпоративного уровня на веб-сайтах Azure

Веб-сайты Azure предоставляют масштабируемую, безопасную и простую в использовании среду для критически важных, крупных масштабируемых сайтов [WordPress][WordPress]. Майкрософт запускает сайты корпоративного класса, такие как блоги [Office][Office] и [Bing][Bing]. В этом документе показано, как можно использовать веб-сайты Azure для установки и обслуживания корпоративного облачного сайта WordPress, который может обрабатывать большой объем посетителей.

## Содержание

-   [Архитектура и планирование][Архитектура и планирование]. Предварительные рекомендации по архитектуре, требованиям и производительности.

-   [Пошаговые инструкции][Пошаговые инструкции]. Создание, развертывание и настройка сайта.

-   [Дополнительные ресурсы][Дополнительные ресурсы]. Дополнительные ресурсы и сведения.

## <span id="plan"></span></a>Архитектура и планирование

К базовой установке WordPress предъявляются только два требования.

-   **База данных MySQL** — доступна в [ClearDB в Магазине Azure][ClearDB в Магазине Azure]. Вы также можете управлять собственной установкой MySQL в виртуальных машинах Azure с помощью [Windows][Windows] или [Linux][Linux].

    > [WACOM.NOTE] ClearDB предоставляет несколько конфигураций MySQL с разными характеристиками производительности для каждой конфигурации. Сведения о предложениях магазина Azure см. в [Магазине Azure][ClearDB в Магазине Azure], предложения непосредственно из ClearDB см. в разделе [Цены ClearDB][Цены ClearDB].

-   **PHP 5.2.4 или выше** — веб-сайты Azure в настоящее время предоставляют [версии PHP 5.3, 5.4 и 5.5][версии PHP 5.3, 5.4 и 5.5].

    > [WACOM.NOTE] Рекомендуется всегда выполнять запуск на последней версии PHP, чтобы гарантированно получить все исправления безопасности.

### Базовое развертывание

Используя просто базовые требования, можно создать основное решение в регионе Azure.

![Веб-сайт Azure и база данных MySQL в одном регионе Azure][Веб-сайт Azure и база данных MySQL в одном регионе Azure]

Хотя это позволит вам развернуть приложение, создав несколько экземпляров веб-сайта, все сведения размещается в центрах обработки данных в определенном географическом регионе. При использовании сайта у посетителей из других регионов может быть увеличен отклик, а при сбое центров обработки данных в этом регионе также произойдет сбой приложения.

### Развертывание в нескольких регионах

С помощью [диспетчера трафика][диспетчера трафика] Azure можно масштабировать сайт WordPress в нескольких географических регионах, указав только один URL-адрес для посетителей. Все посетители проходят через диспетчер трафика, а затем перенаправляются в регион в зависимости от конфигурации балансировки нагрузки.

![Веб-сайт Azure, размещенный в нескольких регионах, который использует маршрутизатор высокой доступности CDBR для маршрутизации MySQL в разных регионах][Веб-сайт Azure, размещенный в нескольких регионах, который использует маршрутизатор высокой доступности CDBR для маршрутизации MySQL в разных регионах]

В каждом регионе сайт WordPress будет масштабироваться на нескольких экземплярах веб-сайта, но масштабирование зависит от региона. Масштабирование регионов с высоким трафиком отличается от масштабирования в регионах с низким трафиком.

Репликацию и маршрутизацию на несколько баз данных MySQL можно выполнить с помощью [маршрутизатора высокой доступности CDBR][маршрутизатора высокой доступности CDBR] ClearDB (показан слева) или [MySQL Cluster CGE][MySQL Cluster CGE].

### Развертывание с хранилищем мультимедиа и кэшированием в нескольких регионах

Если сайт принимает отправки или размещает файлы мультимедиа, используйте хранилище больших двоичных объектов Azure. Если необходимо кэширование, рассмотрите [Кэш Redis][Кэш Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] или другое предложение кэша в [Магазине Azure][Магазине Azure].

![Веб-сайт Azure, размещенный в нескольких регионах с помощью маршрутизатора высокой доступности CDBR для MySQL, с управляемой службой кэша, хранилищем больших двоичных объектов и CDN][Веб-сайт Azure, размещенный в нескольких регионах с помощью маршрутизатора высокой доступности CDBR для MySQL, с управляемой службой кэша, хранилищем больших двоичных объектов и CDN]

Хранилище больших двоичных объектов географически распределено по регионам по умолчанию, поэтому вам не нужно беспокоиться о репликации файлов на всех сайтах. Можно также включить Azure [Content Distribution Network (CDN)][Content Distribution Network (CDN)] для хранилища больших двоичных объектов, которое распределяет файлы на конечные узлы, находящиеся ближе к посетителям.

### Планирование

#### Дополнительные требования

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Для этого...</th>
<th align="left">Используйте это...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Отправка или хранение больших файлов</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">Подключаемый модуль WordPress для использования хранилища больших двоичных объектов</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Отправка электронной почты</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> и <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Подключаемый модуль WordPress для использования SendGrid</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Имена пользовательских доменов</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/">Пользовательские доменные имена с веб-сайтами Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/">Поддержка HTTPS в веб-сайтах Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Предварительная проверка</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-staged-publishing/">Поддержка промежуточной публикации для веб-сайтов Azure</a>
<p>Обратите внимание, что переключение сайта с промежуточной на рабочую публикацию также перемещает конфигурацию WordPress. Перед переключением с промежуточного сайта на рабочий следует убедиться, что все параметры обновлены и соответствуют требованиям к рабочему сайту.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>Мониторинг и устранение неполадок</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/">Диагностика входа с помощью веб-сайтов Azure</a> и <a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-monitor/">Мониторинг веб-сайтов Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Развертывание сайта</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-deploy/">Развертывание веб-сайта Azure</a></td>
</tr>
</tbody>
</table>

#### Доступность и аварийное восстановление

| Для этого...                                                    | Используйте это...                                                             |
|-----------------------------------------------------------------|--------------------------------------------------------------------------------|
| **Сайты балансировки нагрузки** или **геораспределенные сайты** | [Маршрутизация трафика с помощью диспетчера трафика Azure][диспетчера трафика] |
| **Архивация и восстановление**                                  | [Архивация веб-сайтов Azure][Архивация веб-сайтов Azure] и [Восстановление веб-сайта Azure][Восстановление веб-сайта Azure]            |

#### Производительность

Производительность в облаке достигается в основном через кэширование и развертывание, однако, память, пропускная способность и другие атрибуты веб-сайта размещения также следует учитывать.

| Для этого...                                            | Используйте это...                                                                                                                                                                                                                          |
|---------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Общие сведения о возможностях экземпляров веб-сайта** | [Сведения о ценах, включая варианты размеров и режимов веб-сайта][Сведения о ценах, включая варианты размеров и режимов веб-сайта]                                                                                                                                                                         |
| **Ресурсы кэша**                                        | [Кэш Redis][Кэш Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] или другое предложение кэширования в [Магазине Azure][Магазине Azure]                                                                                                                                   |
| **Масштабирование приложения**                          | [Масштабирование веб-сайта Azure][Масштабирование веб-сайта Azure] и [Маршрутизация высокой доступности ClearDB][маршрутизатора высокой доступности CDBR]. Если вы решите разместить собственную установку MySQL и управлять ей, следует рассмотреть [MySQL Cluster CGE][MySQL Cluster CGE] |

#### Миграция

Существует два метода миграции существующего сайта WordPress на веб-сайты Azure.

-   **[Экспорт WordPress][Экспорт WordPress]**. Экспорт контента блога, который затем можно будет импортировать на новый сайт WordPress в Azure с помощью [подключаемого модуля программы для импорта WordPress][подключаемого модуля программы для импорта WordPress].

    > [WACOM.NOTE] Хотя этот процесс позволяет переносить контент, он не перемещает подключаемые модули, темы или другие настройки. Его следует установить повторно вручную.

-   **Миграция вручную**. [Выполните резервное копирование сайта][Выполните резервное копирование сайта] и [базы данных][базы данных], затем вручную восстановите их на веб-сайте Azure и в связанной базе данных MySQL, чтобы перенести настраиваемые сайты и избежать долгой установки подключаемых модулей, тем и других настроек вручную.

## Практическое руководство

### <span id="create"></span></a>Создание нового сайта WordPress

1.  Используйте [Магазин Azure][ClearDB в Магазине Azure], чтобы создать базу данных MySQL с размером, который определен в разделе [Архитектура и планирование][Архитектура и планирование] в регионах, где будет размещаться сайт.

2.  Выполните инструкции в разделе [Создание веб-сайта WordPress из коллекции в Azure][Создание веб-сайта WordPress из коллекции в Azure], чтобы создать новый сайт WordPress. При создании сайта установите флажок **Использовать существующую базу данных MySQL** и выберите базу данных, созданную в шаге 1.

Если вы переносите существующий сайт WordPress, после создания нового сайта см. раздел [Миграция существующего сайта WordPress][Миграция существующего сайта WordPress].

### <span id="migrate"></span></a>Миграция существующего сайта WordPress в Azure

Как было упомянуто в разделе [Архитектура и планирование][Архитектура и планирование], существует два способа миграции веб-сайта WordPress.

-   **Экспорт и импорт** — для сайтов, не требующих многих настроек, или если нужно перенести только контент.

-   **Архивация и восстановление** — для сайтов со множеством настроек, где требуется переместить все.

Воспользуйтесь одним из следующих разделов для миграции сайта.

#### Метод экспорта и импорта

1.  Используйте [экспорт WordPress][Экспорт WordPress], чтобы экспортировать существующий сайт.

2.  Создайте новый веб-сайт, следуя шагам в разделе [Создание нового сайта WordPress][Создание нового сайта WordPress].

3.  Войдите на сайт WordPress в службе веб-сайтов Azure и щелкните кнопки **Подключаемые модули** -\> **Создать**. Найдите и установите подключаемый модуль **Программа импорта WordPress**.

4.  После установки подключаемого модуля программы импорта щелкните пункты меню **Сервис** -\> **Импорт**, а затем выберите **WordPress**, чтобы использовать подключаемый модуль программы импорта WordPress.

5.  На странице **Импорт WordPress** щелкните кнопку **Выбрать файл**. Откройте WXR-файл, экспортированный из существующего сайта WordPress, и нажмите кнопку **Отправить файл и импортировать**.

6.  Нажмите кнопку **Submit** (Отправить). Появится сообщение, что импорт успешно выполнен.

7.  После выполнения этих действий перезапустите веб-сайт на панели мониторинга веб-сайта на [портале управления Azure][портале управления Azure].

После импорта сайта может потребоваться выполнить следующие действия, чтобы включить параметры, которые не содержатся в файле импорта.

| Если вы использовали...                 | Выполните следующие действия...                                                                                                                                                          |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Постоянные ссылки**                   | На панели мониторинга WordPress нового сайта щелкните кнопку **Параметры** -\> **Постоянные ссылки** и обновите структуру постоянных ссылок.                                             |
| **ссылки на изображения и мультимедиа** | Чтобы обновить ссылки на новое расположение, используйте [подключаемый модуль обновления URL-адресов Velvet Blues][подключаемый модуль обновления URL-адресов Velvet Blues], инструмент поиска и замены, или обновите их вручную в базе данных. |
| **Темы**                                | Перейдите в меню **Оформление** -\> **Тема** и при необходимости обновите тему веб-сайта.                                                                                                |
| **Меню**                                | Если тема поддерживает меню, в ссылки на домашнюю страницу может быть все еще встроен старый подкаталог. Перейдите в меню **Оформление** -\> **Меню** и обновите их.                     |

#### Метод резервного копирования и восстановления

1.  Сделайте резервную копию существующего сайта WordPress, используя сведения в разделе [Резервные копии WordPress][Выполните резервное копирование сайта].

2.  Сделайте резервную копию существующей базы данных, используя сведения в разделе [Резервное копирование базы данных][базы данных].

3.  Создайте новую базу данных и восстановите резервную копию.

    1.  Купите новую базу данных в [Магазине Azure][ClearDB в Магазине Azure] или настройте базу данных MySQL в виртуальной машине [Windows][Windows] или [Linux][Linux].

    2.  С помощью клиента MySQL, например, [MySQL Workbench][MySQL Workbench], подключитесь к новой базе данных и импортируйте базу данных WordPress.

    3.  Обновите базу данных, чтобы изменить записи домена на новом домене веб-сайта Azure. Например, mywordpress.azurewebsites.net. Используйте инструмент [поиска и замены скрипта баз данных WordPress][поиска и замены скрипта баз данных WordPress], чтобы безопасно изменить все экземпляры.

4.  Создайте новый веб-сайт и опубликуйте резервную копию WordPress.

    1.  Создайте новый веб-сайт на [портале управления Azure][портале управления Azure] с базой данных, используя меню **Создать** -\> **Веб-сайт** -\> **Пользовательское создание**. Будет создан пустой сайт.

    2.  В резервной копии WordPress найдите файл **wp-config.php** и откройте его в редакторе. Замените следующие записи на сведения для новой базы данных MySQL.

        -   **DB\_NAME** — имя пользователя базы данных.

        -   **DB\_USER** — имя пользователя для доступа к базе данных.

        -   **DB\_PASSWORD** — пароль пользователя.

        После изменения этих записей сохраните и закройте файл **wp-config.php**.

    3.  Используйте сведения в разделе [Развертывание веб-сайта Azure][Развертывание веб-сайта Azure], чтобы включить метод развертывания, который вы хотите использовать, а затем разверните резервную копию WordPress на веб-сайте Azure.

5.  После развертывания сайта WordPress вы сможете получить доступ к новому сайту, используя URL-адрес \*.azurewebsite.net сайта.

### Настройка сайта

После создания или миграции сайта WordPress используйте следующие сведения, чтобы повысить производительность или включить дополнительные функции.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Для этого...</th>
<th align="left">Используйте это...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Установка режима веб-сайта и включение масштабирования</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/">Инструкции по масштабированию веб-сайта</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Включение постоянных подключений базы данных</strong>
<p>По умолчанию WordPress не использует постоянные подключения базы данных, что может вызвать замедление подключения к базе данных после нескольких подключений.</p></td>
<td align="left"><ol>
<li><p>Отредактируйте файл <strong>wp-includes/wp-db.php</strong>.</p></li>
<li><p>Найдите следующую строку.</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>Замените предыдущую строку на приведенную ниже.</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>Найдите следующую строку.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>Замените строку выше на приведенную ниже.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p>Сохраните файл <strong>wp-includes/wp-db.php</strong> и повторно разверните сайт.</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>ПРИМЕЧАНИЕ.</h5><p>Эти изменения можно перезаписать после обновления WordPress.</p><p>По умолчанию WordPress обновляется автоматически; это можно отключить, изменив файл <strong>wp-config.php</strong> и добавив <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Другой способ адресации обновления — использовать веб-задание, которое отслеживает файл <strong>wp-db.php</strong> и выполняет указанные выше изменения при каждом обновлении файла. Дополнительные сведения см. в разделе <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Общие сведения о веб-заданиях</a>.</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>Повышение производительности</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Отключение ARR-файла cookie</a> — может повысить производительность при запуске WordPress на нескольких экземплярах веб-сайта.</p></li>
<li><p>Включите кэширование. <a href="http://msdn.microsoft.com/ru-ru/library/azure/dn690470.aspx">Кэш Redis</a> (предварительная версия) можно использовать с <a href="https://wordpress.org/plugins/redis-object-cache/">подключаемым модулем WordPress кэша объектов Redis</a> или можно выбрать другое предложение кэширования в <a href="http://azure.microsoft.com/ru-ru/gallery/store/">Магазине Azure</a>.</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Ускорение работы WordPress с Wincache</a> — для веб-сайтов Wincache включен по умолчанию.</p></li>
<li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/">Масштабируйте веб-сайт Azure</a> и используйте <a href="http://www.cleardb.com/developers/cdbr/introduction">маршрутизатор высокой доступности ClearDB</a> или <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>Использование больших двоичных объектов для хранилища</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/">Создание учетной записи хранения Azure</a></p></li>
<li><p><a href="http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/">Использование Content Distribution Network (CDN)</a> для геораспределения данных, сохраненных в больших двоичных объектах.</p></li>
<li><p>Установите и настройте <a href="https://wordpress.org/plugins/windows-azure-storage/">хранилище Azure для подключаемого модуля WordPress</a>.</p>
<p>Подробные сведения о настройке и конфигурации для подключаемого модуля см. в <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">руководстве пользователя</a>.</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>Включение электронной почты</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/">Включение SendGrid с помощью Магазина Azure</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Установка подключаемого модуля SendGrid для WordPress</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>Настройка пользовательского имени домена</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/">Использование пользовательского доменного имени с веб-сайтом Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Включение HTTPS для пользовательского доменного имени</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/">Использование HTTPS с веб-сайтом Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Балансировка нагрузки или геораспределение сайта</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Маршрутизация трафика с помощью диспетчера трафика Azure</a>. Если вы используете пользовательский домен, сведения об использовании диспетчера трафика с пользовательскими доменными именами см. в разделе <a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/">Использование пользовательского доменного имени с веб-сайтом Azure</a>.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Включение автоматически создаваемых резервных копий веб-сайта</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/">Резервное копирование веб-сайтов Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Включение ведения журналов диагностики</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/">Включение ведения журналов диагностики для веб-сайтов</a></td>
</tr>
</tbody>
</table>

## [][]Дополнительные ресурсы

-   [Оптимизация WordPress][Оптимизация WordPress]

-   [Преобразование сайта WordPress в мультисайт][Преобразование сайта WordPress в мультисайт]

-   [Мастер обновления ClearDB для Azure][Мастер обновления ClearDB для Azure]

-   [Размещение WordPress в подпапке веб-сайта Azure][Размещение WordPress в подпапке веб-сайта Azure]

-   [Пошаговое руководство. Создание сайта WordPress с помощью Azure][Пошаговое руководство. Создание сайта WordPress с помощью Azure]

-   [Размещение существующего блога WordPress в Azure][Размещение существующего блога WordPress в Azure]

-   [Включение постоянных ссылок в WordPress][Включение постоянных ссылок в WordPress]

-   [Миграция и запуск блога WordPress на веб-сайтах Azure][Миграция и запуск блога WordPress на веб-сайтах Azure]

-   [Бесплатный запуск WordPress на веб-сайтах Azure][Бесплатный запуск WordPress на веб-сайтах Azure]

-   [WordPress в Azure за 2 минуты или меньше][WordPress в Azure за 2 минуты или меньше]

-   [Перемещение блога WordPress в Azure — часть 1: создание блога WordPress в Azure][Перемещение блога WordPress в Azure — часть 1: создание блога WordPress в Azure]

-   [Перемещение блога WordPress в Azure — часть 2: перемещение контента][Перемещение блога WordPress в Azure — часть 2: перемещение контента]

-   [Перемещение блога WordPress в Azure — часть 3: настройка пользовательского домена][Перемещение блога WordPress в Azure — часть 3: настройка пользовательского домена]

-   [Перемещение блога WordPress в Azure — часть 4: постоянные ссылки и правила переопределения URL-адресов][Перемещение блога WordPress в Azure — часть 4: постоянные ссылки и правила переопределения URL-адресов]

-   [Перемещение блога WordPress в Azure — часть 5: перемещение подпапки в корень][Перемещение блога WordPress в Azure — часть 5: перемещение подпапки в корень]

-   [Настройка веб-сайта WordPress в учетной записи Azure][Настройка веб-сайта WordPress в учетной записи Azure]

-   [Поддержка WordPress в Azure][Поддержка WordPress в Azure]

-   [Советы для WordPress в Azure][Советы для WordPress в Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Архитектура и планирование]: #planning
  [Пошаговые инструкции]: #howto
  [Дополнительные ресурсы]: #resources
  [ClearDB в Магазине Azure]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [Цены ClearDB]: http://www.cleardb.com/pricing.view
  [версии PHP 5.3, 5.4 и 5.5]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-configure/
  [Веб-сайт Azure и база данных MySQL в одном регионе Azure]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [диспетчера трафика]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [Веб-сайт Azure, размещенный в нескольких регионах, который использует маршрутизатор высокой доступности CDBR для маршрутизации MySQL в разных регионах]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [маршрутизатора высокой доступности CDBR]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL Cluster CGE]: http://www.mysql.com/products/cluster/
  [Кэш Redis]: http://msdn.microsoft.com/ru-ru/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/ru-ru/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/ru-ru/gallery/store/memcachier/memcachier/
  [Магазине Azure]: http://azure.microsoft.com/ru-ru/gallery/store/
  [Веб-сайт Azure, размещенный в нескольких регионах с помощью маршрутизатора высокой доступности CDBR для MySQL, с управляемой службой кэша, хранилищем больших двоичных объектов и CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Content Distribution Network (CDN)]: http://azure.microsoft.com/ru-ru/documentation/articles/cdn-how-to-use/
  [Подключаемый модуль WordPress для использования хранилища больших двоичных объектов]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/ru-ru/gallery/store/sendgrid/sendgrid-azure/
  [Подключаемый модуль WordPress для использования SendGrid]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Пользовательские доменные имена с веб-сайтами Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-custom-domain-name/
  [Поддержка HTTPS в веб-сайтах Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-configure-ssl-certificate/
  [Поддержка промежуточной публикации для веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-staged-publishing/
  [Диагностика входа с помощью веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-enable-diagnostic-log/
  [Мониторинг веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-monitor/
  [Развертывание веб-сайта Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-deploy/
  [Архивация веб-сайтов Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-backup/
  [Восстановление веб-сайта Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-restore/
  [Сведения о ценах, включая варианты размеров и режимов веб-сайта]: https://azure.microsoft.com/ru-ru/pricing/details/web-sites/
  [Масштабирование веб-сайта Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-scale/
  [Экспорт WordPress]: http://en.support.wordpress.com/export/
  [подключаемого модуля программы для импорта WordPress]: http://wordpress.org/plugins/wordpress-importer/
  [Выполните резервное копирование сайта]: http://codex.wordpress.org/WordPress_Backups
  [базы данных]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Создание веб-сайта WordPress из коллекции в Azure]:  "http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-web-site-gallery/"
  [Миграция существующего сайта WordPress]: #migrate
  [Создание нового сайта WordPress]: #create
  [портале управления Azure]: https://manage.windowsazure.com/
  [подключаемый модуль обновления URL-адресов Velvet Blues]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [поиска и замены скрипта баз данных WordPress]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [Общие сведения о веб-заданиях]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [Отключение ARR-файла cookie]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [подключаемым модулем WordPress кэша объектов Redis]: https://wordpress.org/plugins/redis-object-cache/
  [Ускорение работы WordPress с Wincache]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Создание учетной записи хранения Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/
  [руководстве пользователя]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [Оптимизация WordPress]: http://codex.wordpress.org/WordPress_Optimization
  [Преобразование сайта WordPress в мультисайт]: http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Мастер обновления ClearDB для Azure]: http://www.cleardb.com/store/azure/upgrade
  [Размещение WordPress в подпапке веб-сайта Azure]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Пошаговое руководство. Создание сайта WordPress с помощью Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Размещение существующего блога WordPress в Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Включение постоянных ссылок в WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Миграция и запуск блога WordPress на веб-сайтах Azure]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Бесплатный запуск WordPress на веб-сайтах Azure]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress в Azure за 2 минуты или меньше]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Перемещение блога WordPress в Azure — часть 1: создание блога WordPress в Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Перемещение блога WordPress в Azure — часть 2: перемещение контента]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Перемещение блога WordPress в Azure — часть 3: настройка пользовательского домена]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Перемещение блога WordPress в Azure — часть 4: постоянные ссылки и правила переопределения URL-адресов]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Перемещение блога WordPress в Azure — часть 5: перемещение подпапки в корень]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Настройка веб-сайта WordPress в учетной записи Azure]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Поддержка WordPress в Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Советы для WordPress в Azure]: http://www.johnpapa.net/azurecleardbmysql/

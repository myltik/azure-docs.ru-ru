---
title: "Сайт WordPress корпоративного класса в Azure | Документация Майкрософт"
description: "Информация о размещении сайта WordPress корпоративного класса в службе приложений Azure"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 541dd7bed5a4a9e306642b2c36dd7c9d45aec690
ms.openlocfilehash: fcaff51b2897b1b0bce8b1c503adfd9425c9fbb4
ms.lasthandoff: 01/24/2017


---
# <a name="enterprise-class-wordpress-on-azure"></a>WordPress корпоративного класса в Azure
Служба приложений Azure предоставляет масштабируемую, безопасную и простую в использовании среду для критически важных, крупномасштабных сайтов [WordPress][wordpress]. Корпорация Майкрософт сама запускает сайты корпоративного класса, такие как блоги [Office][officeblog] и [Bing][bingblog]. В этой статье показано, как можно использовать веб-приложения службы приложений Microsoft Azure для установки и обслуживания корпоративного облачного сайта WordPress, который может принимать большое количество посетителей.

## <a name="architecture-and-planning"></a>Архитектура и планирование
К базовой установке WordPress предъявляются только два требования:

* **База данных MySQL** — доступна через [ClearDB в Azure Marketplace][cdbnstore]. Вы также можете управлять собственной установленной системой MySQL в виртуальных машинах Azure с использованием [Windows][mysqlwindows] или [Linux][mysqllinux].

  > [!NOTE]
  > ClearDB обеспечивает несколько конфигураций MySQL, каждая из которых имеет разные характеристики производительности. Сведения о предложениях Магазина Azure см. [здесь][cdbnstore] или непосредственно, как показано на [сайте ClearDB](http://www.cleardb.com/pricing.view).
  >
  >
* **PHP 5.2.4 или более поздней версии** — служба приложений Azure в настоящее время предоставляет версии [PHP 5.4, 5.5 и 5.6][phpwebsite].

  > [!NOTE]
  > Рекомендуется всегда выполнять запуск на последней версии PHP, чтобы гарантированно получить все исправления безопасности.
  >
  >

### <a name="basic-deployment"></a>Базовое развертывание
Используя просто базовые требования, можно создать основное решение в регионе Azure.

![Веб-приложение Azure и база данных MySQL в одном регионе Azure][basic-diagram]

Хотя это позволит вам развернуть приложение, создав несколько экземпляров веб-приложений сайта, все сведения размещаются в центрах обработки данных в определенном географическом регионе. При использовании сайта у посетителей из других регионов может быть увеличен отклик, а при сбое центров обработки данных в этом регионе также произойдет сбой приложения.

### <a name="multi-region-deployment"></a>Развертывание в нескольких регионах
С помощью [диспетчера трафика][trafficmanager] Azure можно масштабировать сайт WordPress в нескольких географических регионах, указав тот же URL-адрес для всех посетителей. Все посетители проходят через диспетчер трафика, а затем маршрутизируются в регион в зависимости от конфигурации балансировки нагрузки.

![Веб-приложение Azure, размещенное в нескольких регионах, которое использует маршрутизатор высокой доступности CDBR для маршрутизации MySQL в разных регионах][multi-region-diagram]

В каждом регионе сайт WordPress будет масштабироваться на нескольких экземплярах веб-приложений, но масштабирование зависит от региона. Масштабирование регионов с высоким трафиком отличается от масштабирования в регионах с низким трафиком.

Для репликации и маршрутизации трафика в несколько баз данных MySQL можно использовать [маршрутизаторы высокой доступности ClearDB (CDBR)][cleardbscale], показанные слева, или [MySQL Cluster Carrier Grade Edition (CGE)][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Развертывание с хранилищем мультимедиа и кэшированием в нескольких регионах
Если сайт принимает отправки или размещает файлы мультимедиа, используйте хранилище BLOB-объектов Azure. Если необходимо кэширование, рассмотрите [Кэш Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) или другое предложение кэша в [Магазине Azure](http://azure.microsoft.com/gallery/store/).

![Веб-приложение Azure, размещенное в нескольких регионах с помощью маршрутизатора высокой доступности CDBR для MySQL, с управляемой службой кэша, хранилищем BLOB-объектов и сетью доставки содержимого][performance-diagram]

Хранилище больших двоичных объектов географически распределено по регионам по умолчанию, поэтому вам не нужно беспокоиться о репликации файлов на всех сайтах. Вы можете также включить для хранилища BLOB-объектов [сеть доставки содержимого Azure][cdn], которая распределяет файлы по конечным узлам, находящимся ближе к посетителям.

### <a name="planning"></a>Планирование
#### <a name="additional-requirements"></a>Дополнительные требования
| Для этого... | Используйте это... |
| --- | --- |
| **Отправка или хранение больших файлов** |[Подключаемый модуль WordPress для использования хранилища BLOB-объектов][storageplugin] |
| **Отправка электронной почты** |[SendGrid][storesendgrid] и [подключаемый модуль WordPress для использования SendGrid][sendgridplugin] |
| **Личные доменные имена** |[Сопоставление имени личного домена с приложением Azure][customdomain] |
| **HTTPS** |[Защита личного домена приложения с помощью протокола HTTPS][httpscustomdomain] |
| **Предварительная проверка** |[Настройка промежуточных сред для веб-приложений в службе приложений Azure][staging] <p>При перемещении веб-приложения с промежуточной на рабочую публикацию также перемещается конфигурация WordPress. Перед перемещением с промежуточного приложения на рабочее следует убедиться, что все параметры обновлены и соответствуют требованиям к рабочему приложению.</p> |
| **Мониторинг и устранение неполадок** |[Включение ведения журнала диагностики для веб-приложений в службе приложений Azure][log] и [Мониторинг приложений в службе приложений Azure][monitor] |
| **Развертывание сайта** |[Развертывание приложения в службе приложений Azure][deploy] |

#### <a name="availability-and-disaster-recovery"></a>Доступность и аварийное восстановление
| Для этого... | Используйте это... |
| --- | --- |
| **Сайты балансировки нагрузки** или **геораспределенные сайты** |[Что такое диспетчер трафика][trafficmanager] |
| **Архивация и восстановление** |[Архивация приложения в Azure][backup] и [Восстановление приложения в Azure][restore] |

#### <a name="performance"></a>Производительность
Производительность в облаке достигается в основном через кэширование и масштабирование, но следует также учитывать память, пропускную способность и другие атрибуты размещения веб-приложений.

| Для этого... | Используйте это... |
| --- | --- |
| **Общая информация о возможностях экземпляра службы приложений** |[Информация о ценах, в том числе о возможностях уровней служб приложений][websitepricing] |
| **Ресурсы кэша** |[Кэш Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) или другое предложение кэширования в [Магазине Azure](/gallery/store/) |
| **Масштабирование приложения** |[Масштабируйте веб-приложение в службе приложений Azure][websitescale] и [используйте высокодоступную маршрутизацию ClearDB][cleardbscale]. Если вы решите разместить собственную установку MySQL и управлять ею, то при масштабировании вам следует учесть [MySQL Cluster CGE][cge]. |

#### <a name="migration"></a>Миграция
Существует два метода миграции существующего сайта WordPress в службу приложений Azure:

* **[Экспорт WordPress.][export]** Выполняет экспорт содержимого блога. Затем содержимое можно импортировать на новый сайт WordPress в службе приложений Azure с помощью [подключаемого модуля программы импорта WordPress][import].

  > [!NOTE]
  > Хотя этот процесс позволяет переносить содержимое, но подключаемые модули, темы или другие настройки не переносятся. Необходимо установить эти компоненты повторно вручную.
  >
  >
* **Миграция вручную.** Выполните [резервное копирование сайта][wordpressbackup] и [базы данных][wordpressdbbackup], а затем вручную восстановите их в веб-приложении в службе приложений Azure и связанной базе данных MySQL. Этот метод удобен для переноса настраиваемых сайтов, так как позволяет избежать долгой установки подключаемых модулей, тем и других настроек вручную.

## <a name="step-by-step-instructions"></a>Пошаговые инструкции
### <a name="create-a-wordpress-site"></a>Создание сайта WordPress
1. Используйте [Azure Marketplace][cdbnstore], чтобы создать базу данных MySQL с размером, который определен в разделе [Архитектура и планирование](#planning), в регионе или регионах, где будет размещаться сайт.
2. Следуйте указаниям в статье [Создание веб-приложения WordPress в службе приложений Azure][createwordpress], чтобы создать веб-приложение WordPress. Во время создания веб-приложения установите флажок **Использовать существующую базу данных MySQL** и выберите базу данных, созданную на шаге 1.

Если вы выполняете миграцию имеющегося сайта WordPress после создания веб-приложения, см. раздел [Миграция существующего сайта WordPress в Azure](#Migrate-an-existing-WordPress-site-to-Azure).

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Миграция существующего сайта WordPress в Azure
Как уже упоминалось в разделе [Архитектура и планирование](#planning), существует два способа миграции веб-сайта WordPress:

* **Экспорт и импорт** — для сайтов, не требующих многих настроек, или если нужно перенести только содержимое.
* **Резервное копирование и восстановление ** — для сайтов со множеством настроек, где требуется переместить все.

Воспользуйтесь одним из следующих разделов для миграции сайта.

#### <a name="the-export-and-import-method"></a>Метод экспорта и импорта
1. Используйте [экспорт WordPress][export], чтобы экспортировать существующий сайт.
2. Создайте веб-приложение, следуя указаниям в разделе [Создание сайта WordPress](#Create-a-new-WordPress-site).
3. Войдите на сайт WordPress на [портале Azure][mgmtportal] и щелкните **Подключаемые модули** > **Добавить новый**. Найдите и установите подключаемый модуль **Программа импорта WordPress**.
4. После установки подключаемого модуля программы импорта WordPress щелкните пункты меню **Инструменты** > **Импорт**, а затем выберите **WordPress**, чтобы использовать этот подключаемый модуль.
5. На странице **Import WordPress** (Импорт WordPress) щелкните кнопку **Выбрать файл**. Откройте WXR-файл, экспортированный из существующего сайта WordPress, и щелкните **Upload file and import** (Отправить файл и импортировать).
6. Нажмите кнопку **Submit**(Отправить). Появится сообщение, что импорт успешно выполнен.
7. После выполнения этих действий перезапустите свой сайт из колонки **Службы приложений** на [портале Azure][mgmtportal].

После импорта сайта может потребоваться выполнить следующие действия, чтобы включить параметры, которые не содержатся в файле импорта.

| Если вы использовали... | Процедура |
| --- | --- |
| **Постоянные ссылки** |На панели мониторинга WordPress нового сайта выберите **Параметры** > **Permalinks** (Постоянные ссылки) и обновите структуру постоянных ссылок. |
| **Ссылки на изображения и мультимедиа** |Чтобы обновить ссылки на новое расположение, используйте [подключаемый модуль обновления URL-адресов Velvet Blues][velvet], инструмент поиска и замены или обновите ссылки вручную в базе данных. |
| **Темы** |Выберите **Внешний вид** > **Тема** и обновите тему сайта, если необходимо. |
| **Меню** |Если тема поддерживает меню, в ссылки на домашнюю страницу может быть все еще встроен старый подкаталог. Выберите **Внешний вид** > **Меню** и обновите их. |

#### <a name="the-backup-and-restore-method"></a>Метод резервного копирования и восстановления
1. Сделайте резервную копию существующего сайта WordPress, используя сведения из статьи [WordPress backups][wordpressbackup] (Резервные копии WordPress).
2. Сделайте резервную копию существующей базы данных, используя сведения из статьи [Backing Up Your Database][wordpressdbbackup] (Резервное копирование базы данных).
3. Создайте базу данных и восстановите резервную копию.

   1. Приобретите новую базу данных в [Azure Marketplace][cdbnstore] либо настройте базу данных MySQL в виртуальной машине [Windows][mysqlwindows] или [Linux][mysqllinux].
   2. С помощью клиента MySQL, например [MySQL Workbench][workbench], подключитесь к новой базе данных и импортируйте базу данных WordPress.
   3. Обновите базу данных, чтобы изменить записи домена на новом домене службы приложений Azure, например mywordpress.azurewebsites.net. Используйте инструмент [поиска и замены для скрипта баз данных WordPres][searchandreplace], чтобы безопасно изменить все экземпляры.
4. Создайте веб-приложение на портале Azure и опубликуйте резервную копию сайта WordPress.

   1. Создайте веб-приложение с базой данных на [портале Azure][mgmtportal]. Для этого щелкните **Создать** > **Интернет и мобильные устройства** > **Azure Marketplace** > **Веб-приложения** > **Веб-приложение+SQL** (или **Веб-приложение+MySQL**) -> **Создать**. Настройте все обязательные параметры, чтобы создать пустое веб-приложение.
   2. В резервной копии WordPress найдите файл **wp-config.php** и откройте его в редакторе. Замените следующие записи на сведения для новой базы данных MySQL:

      * **DB_NAME** — имя пользователя базы данных.
      * **DB_USER** — имя пользователя для доступа к базе данных.
      * **DB_PASSWORD** — пароль пользователя.

        После изменения этих записей сохраните и закройте файл **wp-config.php**.
   3. Используйте сведения из статьи [Развертывание приложения в службе приложений Azure][deploy], чтобы включить метод развертывания, который вы хотите использовать, а затем разверните резервную копию сайта WordPress в веб-приложении в службе приложений Azure.
5. После развертывания сайта WordPress вы сможете получить доступ к новому сайту (в виде веб-приложения службы приложений), используя URL-адрес *.azurewebsite.net сайта.

### <a name="configure-your-site"></a>Настройка сайта
После создания или миграции сайта WordPress используйте следующие сведения, чтобы повысить производительность или включить дополнительные функции.

| Для этого... | Используйте это... |
| --- | --- |
| **Установка режима плана службы приложений, размера и включение масштабирования** |[Увеличение масштаба приложения в Azure][websitescale] |
| **Включение постоянных подключений базы данных** |По умолчанию WordPress не использует постоянные подключения базы данных, что может привести к регулированию подключения к базе данных после нескольких подключений. Чтобы включить постоянные подключения, установите [подключаемый модуль адаптера постоянных подключений](https://wordpress.org/plugins/persistent-database-connection-updater/installation/). |
| **Повышение производительности** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">Отключение ARR-файла cookie</a> может повысить производительность при запуске WordPress на нескольких экземплярах веб-приложений.</p></li><li><p>Включите кэширование. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Кэш Redis</a> (предварительная версия) можно использовать с <a href="https://wordpress.org/plugins/redis-object-cache/">подключаемым модулем WordPress кэша объектов Redis</a> или можно выбрать другое предложение кэширования в <a href="/gallery/store/">Магазине Azure</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Ускорение работы WordPress с помощью Wincache.</a> Wincache по умолчанию включен для веб-приложений.</p></li><li><p>[Масштабируйте веб-приложение в службе приложений Azure][websitescale] и используйте <a href="http://www.cleardb.com/developers/cdbr/introduction">маршрутизацию высокой доступности ClearDB</a> или <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li></ul> |
| **Использование больших двоичных объектов для хранения данных** |<ol><li><p>[Создайте учетную запись хранения Azure](../storage/storage-create-storage-account.md).</p></li><li><p>Узнайте, как [использовать CDN](../cdn/cdn-create-new-endpoint.md) для геораспределения данных, хранящихся в больших двоичных объектах.</p></li><li><p>Установите и настройте <a href="https://wordpress.org/plugins/windows-azure-storage/">хранилище Azure для подключаемого модуля WordPress</a>.</p><p>Подробные сведения о настройке и конфигурации для подключаемого модуля см. в <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">руководстве пользователя</a>.</p> </li></ol> |
| **Включение электронной почты** |Включите <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> с помощью Магазина Azure. Установите <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">подключаемый модуль SendGrid</a> для WordPress. |
| **Настройка пользовательского имени домена** |[Сопоставление имени личного домена с приложением Azure][customdomain]. |
| **Включение HTTPS для личного доменного имени** |[Защита личного домена приложения с помощью протокола HTTPS][httpscustomdomain]. |
| **Распределение нагрузки или географическое распределение сайта** |[Что такое диспетчер трафика][trafficmanager]. Если вы используете личный домен, см. сведения об использовании диспетчера трафика с личными доменными именами в статье [Сопоставление имени личного домена с приложением Azure][customdomain]. |
| **Включение автоматически создаваемых резервных копий** |[Архивация приложения в Azure][backup] |
| **Включение ведения журналов диагностики** |[Включение ведения журнала диагностики для веб-приложений в службе приложений Azure][log] |

## <a name="next-steps"></a>Дальнейшие действия
* [Оптимизация WordPress](http://codex.wordpress.org/WordPress_Optimization)
* [Convert WordPress to Multisite in Azure App Service](web-sites-php-convert-wordpress-multisite.md) (Преобразование сайта WordPress в мультисайт с помощью службы приложений Azure)
* [Мастер обновления ClearDB для Azure](http://www.cleardb.com/store/azure/upgrade)
* [Размещение WordPress в подпапке веб-приложения в службе приложений Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [Step-By-Step: Create a WordPress Site using Windows Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx) (Пошаговое руководство. Создание сайта WordPress с помощью Azure)
* [Размещение существующего блога WordPress в Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [Включение постоянных ссылок в WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [Как мигрировать и запустить блог WordPress в службе приложений Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [Как бесплатно запустить блог WordPress в службе приложений Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [WordPress on Windows Azure in&2; Minutes or Less](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/) (WordPress в Azure за&2; минуты или меньше)
* [Перемещение блога WordPress в Azure. Часть 1. Создание блога WordPress в Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [Перемещение блога WordPress в Azure. Часть 2. Перенос содержимого](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [Перемещение блога WordPress в Azure. Часть 3. Настройка личного домена](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [Перемещение блога WordPress в Azure. Часть 4. Постоянные ссылки и правила переопределения URL-адресов](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [Перемещение блога WordPress в Azure. Часть 5. Перемещение вложенной папки в корень](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Как настроить веб-приложение WordPress в учетной записи Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Поддержка WordPress в Azure](http://www.johnpapa.net/wordpress-on-azure/)
* [Советы по WordPress в Azure](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Create your Azure App Service app](https://azure.microsoft.com/try/app-service/) (Создать приложение службы приложений Azure), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Не требуется никаких кредитных карт и обязательств.
>
>

## <a name="whats-changed"></a>Изменения
Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
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
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md



### <a name="wordpress-and-azure-app-service"></a>WordPress и служба приложений Azure
* [Что такое WordPress? ](https://wordpress.org/)
* [Сайт WordPress корпоративного класса в службе приложений Azure](../articles/app-service-web/web-sites-php-enterprise-wordpress.md)
* [How to purchase ClearDB Shared MySQL hosting for your WordPress app](http://blog.syntaxc4.net/post/2012/12/03/provisioning-a-mysql-database-from-the-windows-azure-store.aspx)
* [How to Purchase ClearDB dedicated MySQL cluster for your WordPress app ](https://azure.microsoft.com/blog/announcing-new-mysql-premium-tiers-from-cleardb/)
* [Deploy a WordPress web app backend with MySQL replication cluster](https://azure.microsoft.com/documentation/templates/wordpress-mysql-replication/)
* [Build your own Master-Master MySQL Cluster using Percona Cluster](https://azure.microsoft.com/documentation/templates/mysql-ha-pxc/) (Создание собственного кластера "ведущий — ведущий" в MySQL с помощью кластера Percona) и [дополнительные сведения об управлении кластером](https://github.com/fanjeffrey/axiom.articles/tree/master/pxc)
* [Deploy WordPress backend by MySQL replication cluster with master-slave configuration](https://azure.microsoft.com/documentation/templates/mysql-replication/)
* [Deploy a WordPress app backend by SQL Azure DB managed by ProjectNami (Развертывание серверной части приложения WordPress с базой данных SQL Azure под управлением ProjectNami)](https://azure.microsoft.com/marketplace/partners/projectnami/projectnami/)
* [Convert WordPress to Multisite in Azure App Service](../articles/app-service-web/web-sites-php-convert-wordpress-multisite.md)

### <a name="porting-your-application-to-app-service"></a>Перенос приложения в службу приложений
* [How to plan your migration to Azure Websites](https://azure.microsoft.com/blog/how-to-plan-your-migration-to-azure-websites/)
* [Migrate your existing WordPress site to Windows Azure](https://sunithamk.wordpress.com/2013/11/06/migrate-your-existing-wordpress-site-to-windows-azure/)
* [Linux Site Migration Tool](https://www.movemetothecloud.net/LinuxMigration)
* [Azure Web Apps – Basic Operations Checklist](https://sunithamk.wordpress.com/2015/10/27/azure-web-apps-basic-operations-checklist/)
* [Configure PHP in Azure App Service Web Apps](../articles/app-service-web/web-sites-php-configure.md)
* [Use DevOps environments effectively for your web apps](../articles/app-service-web/app-service-web-staged-publishing-realworld-scenarios.md)

### <a name="troubleshooting-wordpress-application"></a>Устранение неполадок в приложении WordPress
* [Способы устранения неполадок в приложении WordPress](https://sunithamk.wordpress.com/2014/09/04/wordpress-troubleshooting-techniques-on-azure-websites/)
* [Determine your WordPress web app usage with Application Insights (Определение использования веб-приложения WordPress с помощью службы Application Insights)](https://azure.microsoft.com/blog/usage-analytics-for-wordpress-with-azure-app-insights/)
* [Запуск профилировщика Zend Zray в веб-приложении для диагностики проблем и производительности](https://sunithamk.wordpress.com/2015/08/04/profiling-php-application-on-azure-web-apps/)
* [Использование портала поддержки Kudu для диагностики и устранения проблем в режиме реального времени](https://sunithamk.wordpress.com/2015/11/04/diagnose-and-mitigate-issues-with-azure-web-apps-support-portal/)
* [Использование различных правил автоисправления для автоматического устранения инцидентов в режиме реального времени](http://microsoftazurewebsitescheatsheet.info/#auto-heal)
* [Архивация приложения в Azure](../articles/app-service-web/web-sites-backup.md) и [Восстановление приложения в Azure](../articles/app-service-web/web-sites-restore.md)

### <a name="performance"></a>Производительность
* [Ускорение работы веб-приложения WordPress](https://sunithamk.wordpress.com/2014/08/01/10-ways-to-speed-up-your-wordpress-site-on-azure-websites/)
* [Как использовать кэш Redis для Azure](../articles/redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) с помощью [плагина кэша Redis](https://wordpress.org/plugins/wp-redis/)
* [Присоединение веб-приложения в службе приложений Azure к кэшу Redis при помощи протокола Memcache](../articles/app-service-web/web-sites-connect-to-redis-using-memcache-protocol.md) с использованием [подключаемого модуля Memcache](https://wordpress.org/plugins/memcached/)
* [Включение Wincache с помощью подключаемого модуля общего кэша W3](https://wordpress.org/plugins/w3-total-cache/)
* [Использование подключаемого модуля Supercache для ускорения работы приложения WordPress](http://ruslany.net/2008/12/speed-up-wordpress-on-iis-70/)
* [Кэширование сервера с помощью кэширования вывода IIS](http://blogs.msdn.com/b/brian_swan/archive/2011/06/08/performance-tuning-php-apps-on-windows-iis-with-output-caching.aspx)
* [Включение кэширования браузера для статического содержимого](http://www.iis.net/configreference/system.webserver/staticcontent)



<!--HONumber=Jan17_HO3-->



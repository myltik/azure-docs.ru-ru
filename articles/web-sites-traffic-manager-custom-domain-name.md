<properties title="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование имени пользовательского домена" pageTitle="Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager" metaKeywords="Windows Azure, веб-сайты Windows Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен" class="current">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом" class="current">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Эта статья содержит общие инструкции по использованию имени пользовательского домена с веб-сайтами Azure, использующими Traffic Manager для балансировки нагрузки. Просмотрите вкладки в начале этой статьи, чтобы проверить, указан ли ваш регистратор доменных имен. Если указан, выберите эту вкладку с описанием действий для соответствующего регистратора.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для пользовательского домена](#bkmk_configurecname)
-   [Включение Traffic Manager для веб-сайта](#enabledomain)

<h2><a name="understanding-records"></a>Общие сведения о записях DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых регистратором доменных имен, у которого вы приобрели доменное имя. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход в систему регистратора доменных имен с использованием своей учетной записи и найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку на эту страницу можно найти, открыв сведения своей учетной записи и найдя такую ссылку, как **Мои домены**.

4. Когда найдена страница управления для вашего доменного имени, найдите ссылку, позволяющую изменять записи DNS. Она может иметь название **Файл зоны**, **Записи DNS** или **Дополнительно**.

	* Страница, скорее всего, уже будет содержать несколько созданных записей, например запись, сопоставляющую "**@**" или "\*" странице "парковки домена". Она также может содержать записи для распространенных поддоменов, таких как **www**.
	* На этой странице будут упоминаться **записи CNAME** или будет предоставлен раскрывающийся список для выбора типа записи. На ней также могут упоминаться другие записи, такие как **записи A** и **записи MX**. В некоторых случаях записи CNAME могут называться по-другому, например **записью псевдонима**.
	* Страница также будет содержать поля, позволяющие **сопоставить** значения из поля **Имя узла** или **Доменное имя** другому доменному имени.

5. Несмотря на особенности каждого регистратора, в общем случае вы выполняете сопоставление *из* своего имени пользовательского домена (например, **contoso.com**), *в* доменное имя Traffic Manager (**contoso.trafficmanager.net**), используемое для вашего веб-сайта Azure.

6. По завершении добавления или изменения записей DNS у своего регистратора сохраните эти изменения.

<h2><a name="enabledomain"></a>Включение Traffic Manager для веб-сайта</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


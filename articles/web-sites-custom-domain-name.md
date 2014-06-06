<properties title="Узнайте, как настроить для веб-сайта Azure пользовательское имя домена" pageTitle="Настройка пользовательского имени домена для веб-сайта Azure" metaKeywords="Azure, веб-сайты Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Настройка пользовательского имени домена для веб-сайта Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен" class="current">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся общие инструкции по применению пользовательского имени для веб-сайтов Azure. Просмотрите вкладки в начале этой статьи, чтобы проверить, указан ли ваш регистратор доменных имен. Если указан, выберите эту вкладку с описанием действий для соответствующего регистратора.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для пользовательского домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общие сведения о записях DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых регистратором доменных имен, у которого вы приобрели доменное имя. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход в систему регистратора доменных имен с использованием своей учетной записи и найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку на эту страницу можно найти, открыв сведения своей учетной записи и найдя такую ссылку, как **Мои домены**.

4. Когда найдена страница управления для вашего доменного имени, найдите ссылку, позволяющую изменять записи DNS. Она может иметь название **Файл зоны**, **Записи DNS** или **Дополнительно**.

	* Страница, скорее всего, уже будет содержать несколько созданных записей, например запись, сопоставляющую "**@**" или "\*" странице "парковки домена". Она также может содержать записи для распространенных поддоменов, таких как **www**.
	* На этой странице будут упоминаться **записи A** и **CNAME** или будет предоставлен раскрывающийся список для выбора типа записи. На ней также могут упоминаться другие записи, такие как **записи MX**. В некоторых случаях записи CNAME могут называться по-другому, например **записи IP-адреса** вместо записей A или **записи псевдонимов** вместо записей CNAME.
	* Страница также будет содержать поля, позволяющие **сопоставить** значения из поля **Имя узла** или **Доменное имя** с **IP-адресом** или другим доменным именем.

5. Несмотря на особенности каждого регистратора, в общем случае вы выполняете сопоставление *из* своего пользовательского имени домена (например, **contoso.com**), *в* доменное имя веб-сайта Azure (**contoso.azurewebsites.net**) или виртуальный IP-адрес веб-сайта Azure.

	* Записи CNAME всегда сопоставляются с доменом веб-сайтов Azure - **contoso.azurewebsites.net**. Таким образом, вы будете выполнять сопоставление *из* домена, такого как **www**, *с* адресом **&lt;имя_веб_сайта&gt;. azurewebsites.net**.
	
		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Сопоставление *из* домена **www** *с* адресом **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> ИЛИ
		> 
		> * Сопоставление *из* **awverify.www** *с* адресом **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

	* Записи A всегда сопоставляются с виртуальным IP-адресом веб-сайтов Azure. Таким образом, выполняется сопоставление *из* домена, такого как **www**, *с* виртуальным IP-адресом веб-сайта.
	
		> [WACOM.NOTE] Для сопоставления корневого домена (например, **contoso.com**) с веб-сайтом Azure часто будет выполняться сопоставление из "**@**" или пустой записи с виртуальным IP-адресом. Для создания группового сопоставления, которое сопоставляет все поддомены с виртуальным IP-адресом, обычно используется сопоставление из "*" с виртуальным IP-адресом.
		> 
		> Особенности сопоставления корневого или группового домена зависят от регистратора. Дополнительные сведения см. в документации, предоставляемой регистратором.

6. По завершении добавления или изменения записей DNS у своего регистратора сохраните эти изменения.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<properties title="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Dotster" pageTitle="Настройка доменного имени Dotster для веб-сайта Azure" metaKeywords="Azure, веб-сайты Azure, Dotster" description="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Dotster.com](https://dotster.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Dotster. Выполните следующие действия, чтобы найти средства DNS для Dotster.com.

1. Выполните вход на Dotster.com с использованием своей учетной записи. В меню **Домен** выберите **DomainCentral**.

    ![Меню центрального домена Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. Выберите домен для открытия списка параметров. Выберите ссылку **Серверы имен**.

    ![Параметры конфигурации домена Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. Выберите **Использовать другие серверы имен**. Чтобы воспользоваться преимуществами служб DNS на Dotster, необходимо указать следующие серверы имен: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com и ns4.nameresolve.com.

    ![Параметры конфигурации домена Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] Чтобы изменения серверов имен вступили в силу, может потребоваться 24-48 часов. Остальная часть приведенных в этой статье действий недоступна до вступления указанных изменений в силу.

4. В DomainCentral выберите домен, а затем выберите **DNS**. В списке **Изменить** выберите тип добавляемой записи DNS (**Псевдоним CNAME** или **Запись A**). 

    ![Параметры конфигурации домена Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. Затем укажите для записи значения в полях **Узел** и **Указывает на**. Закончив, нажмите кнопку **Добавить**.

    ![Параметры конфигурации домена Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * При добавлении записи CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    * При добавлении записи A необходимо задать в поле **Узел** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). Необходимо задать в поле **Указывает на** IP-адрес вашего веб-сайта Azure.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Узел** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Указывает на**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Узел** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Указывает на**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.


<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<properties title="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Directnic" pageTitle="Настройка доменного имени Directnic для веб-сайта Azure" metaKeywords="Azure, веб-сайты Azure, Directnic" description="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [DirectNic.com](https://directnic.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Directnic. Выполните следующие действия, чтобы найти средства DNS для Directnic.com.

1. Выполните вход на Directnic.com с использованием своей учетной записи и выберите **Мои службы** и **Домены**. 

    ![Меню служб Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Щелкните имя домена, которое вы хотите использовать для веб-сайта Azure.

2. На странице управления для вашего домена нажмите кнопку **Управление** для **DNS** в области **Службы**.

    ![Панель служб](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Добавьте записи DNS, заполнив поля **Тип**, **Имя** и **Данные**. Закончив, нажмите кнопку **Добавить запись**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * При добавлении записи CNAME необходимо задать в поле **Имя** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Данные** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    * При добавлении записи A необходимо задать в поле **Имя** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). Необходимо задать в поле **Данные** IP-адрес вашего веб-сайта Azure.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Имя** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Данные**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Имя** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Данные**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.


<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


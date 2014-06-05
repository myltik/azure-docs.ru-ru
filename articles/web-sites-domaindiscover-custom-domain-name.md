<properties title="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в DomainDiscover — TierraNet" pageTitle="Настройка доменного имени DomainDiscover для веб-сайта Azure" metaKeywords="Azure, веб-сайты Azure, DomainDiscover, TierraNet" description="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [DomainDiscover.com](https://domaindiscover.com), для веб-сайтов Azure. DomainDiscover.com является частью TierraNet.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых DomainDiscover. Выполните следующие действия, чтобы найти средства DNS для DomainDiscover.com.

1. Выполните вход на DomainDiscover.com (TierraNet) с использованием своей учетной записи, выбрав **Панель управления** в меню **Вход**.

    ![Меню "Вход" DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. На странице **Доменные службы** выберите домен, который требуется использовать для веб-сайта Azure.

    ![Страница управления доменом](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. В разделе "Параметры домена" нажмите кнопку **Изменить** для элемента **Служба DNS**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. В окне **Управление DNS** выберите тип записи DNS для добавления в список **Добавление записей**. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. На следующей странице введите значения записи DNS. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * При добавлении записи CNAME необходимо сначала выбрать элемент **CNAME (псевдоним)** на странице **Управление DNS**. Затем задайте в поле **Узел** поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Имя узла псевдонима** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Затем укажите значение срока жизни (TTL), например 1800 секунд.

    * При добавлении записи A необходимо сначала выбрать элемент **A** на странице **Управление DNS**. Затем задайте в поле **Узел** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**.) Необходимо задать в поле **IP-адрес** IP-адрес вашего веб-сайта Azure. Затем укажите значение срока жизни (TTL), например 1800 секунд.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Узел** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Имя узла псевдонима**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Узел** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Имя узла псевдонима**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.


5. При добавлении записи A может появиться предупреждение о том, что существующая запись A для вашего домена не является неактивной. Он использует последнюю измененную запись, и DomainDirect уже имеет запись A по умолчанию для имени корневого домена. Можно либо положиться на этот приоритет, либо удалить запись A по умолчанию, нажав кнопку **УДАЛИТЬ**.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


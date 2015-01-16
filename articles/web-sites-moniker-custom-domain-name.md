<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Настройка имени домена Moniker для веб-сайта Azure website" metaKeywords="Azure, веб-сайты Azure, имя домена" description="Узнайте, как настроить веб-сайт Azure для использования доменного имени, зарегистрированного в Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Настройка пользовательского имени домена для веб-сайта Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Веб-сайт с использованием диспетчера трафика</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Moniker.com](https://moniker.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых компанией Moniker. Выполните следующие действия, чтобы найти средства DNS для Moniker.com.

1. Войдите на веб-сайт Moniker.com с использованием своей учетной записи, выберите **My Domains** (Мои домены), затем **Manage Templates** (Управление шаблонами).

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. На странице **Zone Template Management** (Управление шаблонами зоны) выберите **Create New Template** (Создать новый шаблон).

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Заполните поле **Имя шаблона**. 

5. Затем создайте запись DNS путем выбора элемента **Тип записи**. Затем заполните поля **Имя узла** и **Адрес**. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    *  При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    *  При добавлении записи A необходимо задать в поле **Имя узла** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). В поле **Адрес** необходимо задать IP-адрес вашего веб-сайта Azure.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **Имя_узла** поддомена **www** со значением **Адрес** поддомена **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> ИЛИ
		> 
		> * Значение **Имя_узла** поддомена **www** со значением **Адрес** поддомена **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> Запись CNAME позволяет Azure удостовериться, что именно вы являетесь владельцем домена, указанного в записи A.

7. Нажмите кнопку **Добавить**, чтобы добавить запись.

8. Когда все записи будут добавлены, нажмите кнопку **Сохранить**.

5. Выберите элемент **Диспетчер доменов**, чтобы вернуться к списку доменов.

6. Установите флажок для конечного домена, а затем щелкните элемент **Управление шаблонами** еще раз.

7. Найдите новый шаблон, созданный в предыдущих действиях. Выберите ссылку **поместить выбранные домены (1) в этот шаблон**.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

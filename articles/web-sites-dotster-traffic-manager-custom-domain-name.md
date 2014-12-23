<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Настройка доменного имени Dotster для веб-сайта Azure, использующего Traffic Manager" metaKeywords ="Windows Azure, веб-сайты Windows Azure, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Windows Azure, использующего Traffic Manager (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Веб-сайт с использованием диспетчера трафика</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Dotster.com](https://dotster.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Dotster. Выполните следующие действия, чтобы найти средства DNS для Dotster.com.

1. Выполните вход на Dotster.com с использованием своей учетной записи. В меню **Домен** выберите пункт **DomainCentral**.

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. Выберите домен для открытия списка параметров. Выберите ссылку **Серверы имен**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. Выберите **Использовать другие серверы имен**. Чтобы воспользоваться преимуществами служб DNS на Dotster, необходимо указать следующие серверы имен: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com и ns4.nameresolve.com.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] Чтобы изменения серверов имен вступили в силу, может потребоваться 24-48 часов. Остальная часть приведенных в этой статье действий недоступна до вступления указанных изменений в силу.

4. В DomainCentral выберите домен, а затем выберите **DNS**. В списке **Изменить** выберите тип добавляемой записи DNS (**Псевдоним CNAME** или **Запись A**). 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. Затем укажите для записи значения в полях **Узел** и **Указывает на**. Закончив, нажмите кнопку **Добавить**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)
 
    *  При добавлении записи CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, нагрузка на который выравнивается с помощью диспетчера трафика.

<h2><a name="enabledomain"></a>Включение веб-сайта диспетчера трафика</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

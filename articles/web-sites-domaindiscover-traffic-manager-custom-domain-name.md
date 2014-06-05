<properties title="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование доменного имени, зарегистрированного в DomainDiscover — TierraNet" pageTitle="Настройка доменного имени DomainDiscover для веб-сайта Azure, использующего Traffic Manager" metaKeywords="Windows Azure, веб-сайты Windows Azure, DomainDiscover, TierraNet, Traffic Manager" description="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование доменного имени, зарегистрированного в DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Windows Azure, использующего Traffic Manager (DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом" class="current">Использование Traffic Manager веб-сайтом</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [DomainDiscover.com](https://domaindiscover.com), для веб-сайтов Azure. DomainDiscover.com является частью [TierraNet](https://www.tierra.net/).

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

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    * При добавлении записи CNAME необходимо сначала выбрать элемент **CNAME (псевдоним)** на странице **Управление DNS**. Затем задайте в поле **Узел** поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Имя узла псевдонима** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**. Затем укажите значение срока жизни (TTL), например 1800 секунд.

	    > [WACOM.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

<h2><a name="enabledomain"></a>Включение Traffic Manager для веб-сайта</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


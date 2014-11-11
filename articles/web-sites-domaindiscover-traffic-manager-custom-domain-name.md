<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Настройка имени пользовательского домена для веб-сайта Windows Azure, использующего Traffic Manager (DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [введение](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [DomainDiscover.com][DomainDiscover.com], для веб-сайтов Azure. DomainDiscover.com является частью [TierraNet][TierraNet].

[WACOM.INCLUDE [нижний-колонтитул-веб-сайта-tm](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [нижний-колонтитул-введения](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для стандартного режима][Настройка веб-сайтов для стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение Traffic Manager для веб-сайта][Включение Traffic Manager для веб-сайта]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима

[WACOM.INCLUDE [режимы](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для настраиваемого домена с помощью средств, предоставляемых DomainDiscover. Выполните следующие действия, чтобы найти средства DNS для DomainDiscover.com.

1.  Выполните вход на DomainDiscover.com (TierraNet) с использованием своей учетной записи, выбрав **Панель управления** в меню **Вход**.

    ![Меню "Вход" DomainDiscover][Меню "Вход" DomainDiscover]

2.  На странице **Доменные службы** выберите домен, который требуется использовать для веб-сайта Azure.

    ![Страница управления доменом][Страница управления доменом]

3.  В разделе "Параметры домена" нажмите кнопку **Изменить** для элемента **Служба DNS**.

    ![Кнопка изменения DNS][Кнопка изменения DNS]

4.  В окне **Управление DNS** выберите тип записи DNS для добавления в список **Добавление записей**. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS][1]

5.  На следующей странице введите значения записи DNS. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS][2]

    -   При добавлении записи CNAME необходимо сначала выбрать элемент **CNAME (псевдоним)** на странице **Управление DNS**. Затем задайте в поле **Узел** поддомен, который вы хотите использовать. Например, **www**. В поле **Имя узла псевдонима** задайте доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**. Затем укажите значение срока жизни (TTL), например 1800 секунд.

        > [WACOM.ПРИМЕЧАНИЕ] Записи CNAME необходимо использовать только для связи имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

## <a name="enabledomain"></a>Включение веб-сайта Traffic Manager

[WACOM.INCLUDE [режимы][3]]

  [пошаговое-css-руководство-по-облачным-службам-веб-сайтов]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [введение]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [DomainDiscover.com]: https://domaindiscover.com
  [TierraNet]: https://www.tierra.net/
  [нижний-колонтитул-веб-сайта-tm]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [нижний-колонтитул-введения]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [общие-сведения-о-dns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [режимы]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Меню "Вход" DomainDiscover]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Страница управления доменом]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [Кнопка изменения DNS]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png
  [3]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

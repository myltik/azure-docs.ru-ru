<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Настройка имени пользовательского домена для веб-сайта Windows Azure, использующего Traffic Manager (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов][пошаговое-css-руководство-по-облачным-службам-веб-сайтов]]

[WACOM.INCLUDE [введение][введение]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Dotster.com][Dotster.com], для веб-сайтов Azure.

[WACOM.INCLUDE [нижний-колонтитул-веб-сайта-tm][нижний-колонтитул-веб-сайта-tm]]

[WACOM.INCLUDE [нижний-колонтитул-введения][нижний-колонтитул-введения]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для стандартного режима][Настройка веб-сайтов для стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение Traffic Manager для веб-сайта][Включение Traffic Manager для веб-сайта]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns][общие-сведения-о-dns]]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима

[WACOM.INCLUDE [режимы][режимы]]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Dotster. Выполните следующие действия, чтобы найти средства DNS для Dotster.com.

1.  Выполните вход на Dotster.com с использованием своей учетной записи. В меню **Домен** выберите пункт **DomainCentral**.

    ![Меню центрального домена Dotster][Меню центрального домена Dotster]

2.  Выберите домен для открытия списка параметров. Выберите ссылку **Серверы имен**.

    ![Параметры конфигурации домена Dotster][Параметры конфигурации домена Dotster]

3.  Выберите пункт **Использовать другие серверы имен**. Чтобы воспользоваться преимуществами служб DNS на Dotster, необходимо указать следующие серверы имен: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com и ns4.nameresolve.com.

    ![Параметры конфигурации домена Dotster][1]

    > [WACOM.ПРИМЕЧАНИЕ] Для вступления в силу изменений серверов имен может потребоваться от 24 до 48 часов. Остальная часть приведенных в этой статье действий недоступна до вступления указанных изменений в силу.

4.  В DomainCentral сначала выберите домен, а затем **DNS**. В списке **Изменить** выберите тип добавляемой записи DNS (**Псевдоним CNAME** или **Запись A**).

    ![Параметры конфигурации домена Dotster][2]

5.  Затем укажите для записи значения в полях **Узел** и **Указывает на**. Закончив, нажмите кнопку **Добавить**.

    ![Параметры конфигурации домена Dotster][3]

    -   Добавляя запись CNAME, задайте в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

        > [WACOM.ПРИМЕЧАНИЕ] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

## <a name="enabledomain"></a>Включение веб-сайта Traffic Manager

[WACOM.INCLUDE [режимы][4]]

  [Пользовательский домен]: /ru-ru/documentation/articles/web-sites-custom-domain-name "Пользовательский домен"
  [GoDaddy]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ru-ru/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [пошаговое-css-руководство-по-облачным-службам-веб-сайтов]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [введение]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Dotster.com]: https://dotster.com
  [нижний-колонтитул-веб-сайта-tm]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [нижний-колонтитул-введения]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [общие-сведения-о-dns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [режимы]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Меню центрального домена Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Параметры конфигурации домена Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
  [4]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

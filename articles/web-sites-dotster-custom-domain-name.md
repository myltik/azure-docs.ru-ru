<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure website to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Настройка пользовательского имени домена для веб-сайта Azure (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Dotster.com][Dotster.com], для веб-сайтов Azure.

[WACOM.INCLUDE [introfooter][introfooter]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима][Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Dotster. Выполните следующие действия, чтобы найти средства DNS для Dotster.com.

1.  Выполните вход в вашу учетную запись Dotster.com. В меню **Domain**, выберите **DomainCentral**.

    ![Меню центрального домена Dotster][Меню центрального домена Dotster]

2.  Выберите домен для открытия списка параметров. Выберите ссылку **Серверы имен**.

    ![Параметры конфигурации домена Dotster][Параметры конфигурации домена Dotster]

3.  Выберите **Использовать другие серверы имен**. Чтобы воспользоваться преимуществами служб DNS на Dotster, необходимо указать следующие серверы имен: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com и ns4.nameresolve.com.

    ![Параметры конфигурации домена Dotster][1]

    > [WACOM.NOTE] Чтобы изменения серверов имен вступили в силу, может потребоваться 24–48 часов. Остальная часть приведенных в этой статье действий недоступна до вступления указанных изменений в силу.

4.  В DomainCentral выберите домен, а затем **DNS**. В списке **Изменить** выберите тип добавляемой записи DNS (**Псевдоним CNAME** или **Запись A**).

    ![Параметры конфигурации домена Dotster][2]

5.  Затем укажите для записи значения в полях **Узел** и **Указывает на**. Закончив, нажмите кнопку **Добавить**.

    ![Параметры конфигурации домена Dotster][3]

    -   При добавлении записи CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    -   При добавлении записи A необходимо задать в поле **Имя узла** либо символ <**@*>\* (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). Необходимо задать в поле **Указывает на** IP-адрес вашего веб-сайта Azure.

        > [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
        >
        > -   Значение **хоста** **www**, которое **указывает на** значение **\<yourwebsitename\>.azurewebsites.net**.
        >
        > ИЛИ
        >
        > -   Значение **хоста** **www**, которое **указывает на** значение **\<yourwebsitename\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [modes][4]]

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
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Dotster.com]: https://dotster.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Меню центрального домена Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Параметры конфигурации домена Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png
  [4]: ../includes/custom-dns-web-site-enable-on-web-site.md

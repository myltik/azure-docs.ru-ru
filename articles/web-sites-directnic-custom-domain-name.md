<properties title="Learn how to configure an Azure website to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure website to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Настройка пользовательского имени домена для веб-сайта Azure (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [введение](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [DirectNic.com][DirectNic.com], для веб-сайтов Azure.

[WACOM.INCLUDE [нижний-колонтитул-введения](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима][Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима

[WACOM.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для настраиваемого домена с помощью средств, предоставляемых Directnic. Выполните следующие действия, чтобы найти средства DNS для Directnic.com.

1.  Выполните вход на Directnic.com с использованием своей учетной записи и выберите **Мои службы** и **Домены**.

    ![Меню служб Directnic][Меню служб Directnic]

2.  Выберите имя домена, которое вы хотите использовать для веб-сайта Azure.

3.  На странице управления вашим доменом нажмите кнопку **Управление** для **DNS** в области **Службы**.

    ![Панель служб][Панель служб]

4.  Добавьте записи DNS, заполнив поля **Тип**, **Имя** и **Данные**. Закончив, нажмите кнопку **Добавить запись**.

    ![][0]

    -   При добавлении записи CNAME необходимо задать в поле **Имя** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Данные** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    -   При добавлении записи A необходимо задать в поле **Имя** символ **@** (представляющий имя корневого домена, например **contoso.com**) либо поддомен, который вы хотите использовать (например, **www**). В поле **Данные** необходимо задать IP-адрес вашего веб-сайта Azure.

        > [WACOM.ПРИМЕЧАНИЕ] Если вы будете использовать запись A, необходимо также добавить запись CNAME в одну из следующих конфигураций:
        >
        > -   Значение **Имя** поддомена **www** со значением **Данные** поддомена **\<yourwebsitename\>.azurewebsites.net**.
        >
        > ИЛИ
        >
        > -   Значение **Имя** поддомена **awverify.www** со значением **Данные** поддомена **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

  [DirectNic.com]: https://directnic.com
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [Меню служб Directnic]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Панель служб]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  [0]: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png

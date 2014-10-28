<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Настройка пользовательского имени домена для веб-сайта Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов][пошаговое-css-руководство-по-облачным-службам-веб-сайтов]]

[WACOM.INCLUDE [введение][введение]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Moniker.com][Moniker.com], для веб-сайтов Azure.

[WACOM.INCLUDE [нижний-колонтитул-введения][нижний-колонтитул-введения]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима][Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns][общие-сведения-о-dns]]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима

[WACOM.INCLUDE [режимы][режимы]]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Moniker. Выполните следующие действия, чтобы найти средства DNS для Moniker.com.

1.  Выполните вход на Moniker.com с использованием своей учетной записи и выберите **Мои домены** и **Управление шаблонами**.

    ![Страница моих доменов для Moniker][Страница моих доменов для Moniker]

2.  На странице **Управление шаблонами зоны** выберите **Создать новый шаблон**.

    ![Управление шаблонами зоны Moniker][Управление шаблонами зоны Moniker]

3.  Заполните поле **Имя шаблона**.

4.  Затем создайте запись DNS путем выбора элемента **Тип записи**. Затем заполните поля **Имя узла** и **Адрес**.

    ![Создание шаблона зоны Moniker][Создание шаблона зоны Moniker]

    -   При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** имя домена **.azurewebsites.net** веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    -   При добавлении записи A необходимо задать в поле **Имя узла** либо символ <**@*>\* (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который необходимо использовать (например, **www**). В поле **Адрес** необходимо задать IP-адрес веб-сайта Azure.

        > [WACOM.ПРИМЕЧАНИЕ] Если вы будете использовать запись A, необходимо также добавить запись CNAME из одной из следующих конфигураций:
        >
        > -   Значение **Имя узла** поддомена **www** со значением **Адрес** поддомена **\<имя\_вашего\_веб-сайта\>.azurewebsites.net**.
        >
        > ИЛИ
        >
        > -   Значение **Имя узла** поддомена **awverify.www** со значением **Адрес** поддомена **awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

5.  Нажмите кнопку **Добавить**, чтобы добавить запись.

6.  Когда все записи будут добавлены, нажмите кнопку **Сохранить**.

7.  Выберите элемент **Диспетчер доменов**, чтобы вернуться в список доменов.

8.  Установите флажок для конечного домена, а затем нажмите элемент **Управление шаблонами** еще раз.

9.  Найдите новый шаблон, созданный в предыдущих действиях. Выберите ссылку **поместить выбранные домены (1) в этот шаблон**.

    ![Создание шаблона зоны Moniker][1]

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [режимы][2]]

  [Пользовательский домен]: /ru-ru/documentation/articles/web-sites-custom-domain-name "Пользовательский домен"
  [GoDaddy]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ru-ru/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [пошаговое-css-руководство-по-облачным-службам-веб-сайтов]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [введение]: ../includes/custom-dns-web-site-intro.md
  [Moniker.com]: https://moniker.com
  [нижний-колонтитул-введения]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [общие-сведения-о-dns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [режимы]: ../includes/custom-dns-web-site-modes.md
  [Страница моих доменов для Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Управление шаблонами зоны Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Создание шаблона зоны Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png
  [1]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md

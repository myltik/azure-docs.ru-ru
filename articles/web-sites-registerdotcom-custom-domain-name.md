<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Настройка имени пользовательского домена для веб-сайта Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Register.com][1], для веб-сайтов Azure.

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
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Register.com. Выполните следующие действия, чтобы найти и использовать средства DNS.

1.  Выполните вход на register.com с использованием своей учетной записи и выберите **Ваша учетная запись** в правом верхнем углу для просмотра доменов, а затем выберите имя пользовательского домена.

    ![страница моей учетной записи][страница моей учетной записи]

2.  Выполните прокрутку вниз до элемента **Дополнительные технические параметры**. Ссылки в этом разделе позволяют управлять записями для вашего домена.

    -   Для записей A используйте ссылку **Изменить записи IP-адресов**.
    -   Для записей CNAME используйте ссылку **Изменить записи псевдонимов доменов**.

    ![Дополнительные технические параметры][Дополнительные технические параметры]

3.  При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые. Эта форма одинакова для записей CNAME и A.

    -   При добавлении записи CNAME необходимо задать в поле **.mydomainname.com** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.

        ![форма CNAME][форма CNAME]

        > [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
        >
        > -   Значение **www** в поле **Псевдоним** и значение **\<имя\_вашего\_веб-сайта\>.azurewebsites.net** в поле **Другой узел**.
        >
        > ИЛИ
        >
        > -   Значение **awverify.www** в поле **Псевдоним** и значение **awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net** в поле **Другой узел**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

    -   При добавлении записи A необходимо задать в поле **.mydomainname.com** тот поддомен, который вы хотите использовать (например, **www**). Оставьте это поле пустым, чтобы задать корневой домен, или используйте и **\*\* для создания сопоставления по подстановочным знакам. Необходимо задать в поле** "указывает на\*\*" IP-адрес вашего веб-сайта Azure.

        ![форма записи А][форма записи А]

4.  По завершении добавления или изменения записей нажмите кнопку **Продолжить** для просмотра изменений. Выберите **Продолжить** еще раз, чтобы сохранить изменения.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [modes][2]]

  [Пользовательский домен]: /ru-ru/documentation/articles/web-sites-custom-domain-name "Пользовательский домен"
  [GoDaddy]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ru-ru/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [1]: https://www.register.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [страница моей учетной записи]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Дополнительные технические параметры]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [форма CNAME]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [форма записи А]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md

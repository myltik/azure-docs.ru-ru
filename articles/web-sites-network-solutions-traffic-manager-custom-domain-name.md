<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Network Solutions][1], для веб-сайтов Azure.

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для стандартного режима][Настройка веб-сайтов для стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение Traffic Manager для веб-сайта][Включение Traffic Manager для веб-сайта]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Network Solutions. Выполните следующие действия, чтобы найти и использовать средства DNS.

1.  Выполните вход на networksolutions.com с использованием своей учетной записи и выберите **Моя учетная запись** в правом верхнем углу.

2.  На вкладке **Мои продукты и услуги** выберите **Изменить DNS**.

    ![страница изменения DNS][страница изменения DNS]

3.  В разделе **Управление <yourdomainname>** страницы **Доменные имена** выберите **Изменить дополнительные записи DNS**.

    ![страница доменных имен с выбранным элементом изменения дополнительных записей DNS][страница доменных имен с выбранным элементом изменения дополнительных записей DNS]

4.  **Страница** Обновление дополнительных записей DNS **содержит раздел для каждого типа записи с расположенной ниже кнопкой** Изменить</strong>. Для записей CNAME используйте раздел **Псевдоним узла (записи CNAME)**.

    ![страница обновления дополнительных записей DNS][страница обновления дополнительных записей DNS]

5.  При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые.

    При добавлении записи CNAME необходимо задать в поле **Псевдоним** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо выбрать круглое поле рядом с полем **Другой узел**, а также задать в поле **Другой узел** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого с вашим веб-сайтом Azure. Например, **contoso.trafficmanager.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.

    ![форма CNAME][форма CNAME]

6.  По завершении добавления или изменения записей нажмите кнопку **Продолжить**, чтобы просмотреть изменения. Выберите **Сохранить только изменения** для сохранения изменений.

## <a name="enabledomain"></a>Включение Traffic Manager на веб-сайте

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
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.networksolutions.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [страница изменения DNS]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [страница доменных имен с выбранным элементом изменения дополнительных записей DNS]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [страница обновления дополнительных записей DNS]: ./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png
  [форма CNAME]: ./media/web-sites-custom-domain-name/ns-cnametm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

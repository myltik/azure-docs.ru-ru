<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Moniker][1], для веб-сайтов Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для стандартного режима][Настройка веб-сайтов для стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение Traffic Manager для веб-сайта][Включение Traffic Manager для веб-сайта]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

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

    -   При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

5.  Нажмите кнопку **Добавить**, чтобы добавить запись.

6.  Когда все записи будут добавлены, нажмите кнопку **Сохранить**.

7.  Выберите элемент **Диспетчер доменов**, чтобы вернуться в список доменов.

8.  Установите флажок для конечного домена, а затем щелкните по элементу **Управление шаблонами** еще раз.

9.  Найдите новый шаблон, созданный в предыдущих действиях. Выберите ссылку **поместить выбранные домены (1) в этот шаблон**.

    ![Создание шаблона зоны Moniker][2]

## <a name="enabledomain"></a>Включение Traffic Manager для веб-сайта

[WACOM.INCLUDE [modes][3]]

  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://moniker.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Страница моих доменов для Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Управление шаблонами зоны Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Создание шаблона зоны Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [3]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

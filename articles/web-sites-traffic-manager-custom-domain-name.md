<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a custom domain name" pageTitle="Настройка пользовательского домена для веб-сайта Azure, который использует Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name/" title="Websites">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Веб-сайт с использованием диспетчера трафика</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В этой статье содержатся общие инструкции по использованию имени пользовательского домена с веб-сайтами Azure, использующими диспетчер трафика для балансировки нагрузки. Просмотрите вкладки в начале этой статьи, чтобы проверить, указан ли ваш регистратор доменных имен. Если указан, выберите эту вкладку с описанием действий для соответствующего регистратора.

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

Для сопоставления пользовательского домена с веб-сайтом Azure необходимо добавить новую запись в таблицу DNS об этом домене с помощью средств регистратора, у которого вы приобрели доменное имя. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход в систему регистратора доменных имен с использованием своей учетной записи и найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку на эту страницу можно найти, открыв сведения своей учетной записи, а затем отыскав такую ссылку, как **Мои домены**.

4. Когда найдена страница управления для вашего доменного имени, найдите ссылку, позволяющую изменять записи DNS. Это может быть **Файл зоны**, **Записи DNS** или ссылка на **Дополнительные настройки**.

	* Страница, скорее всего, уже будет содержать несколько созданных записей, например сопоставляющую запись "**@**" или "\*" со страницей парковки домена. Она также может содержать записи для распространенных поддоменов, таких как **www**.
	*  На этой странице будут упоминаться **записи CNAME** или будет предоставлен раскрывающийся список для выбора типа записи. На ней также могут упоминаться другие записи, такие как **записи A** и **записи MX**. В некоторых случаях записи CNAME могут называться по-другому, например **записью псевдонима**.
	* На странице также есть поля, с помощью которых можно определить **соответствие** **имени узла** или **доменного имени** с другим доменным именем.

5. Отдельные настройки у различных регистраторов могут отличаться, но в целом сопоставление вашего пользовательского домена (например, **contoso.com**) с доменным именем Traffic Manager (**contoso.trafficmanager.net**), который применяется на вашем веб-сайте Azure, одинаково везде.

6. По завершении добавления или изменения записей DNS у своего регистратора сохраните эти изменения.

<h2><a name="enabledomain"></a>Включение веб-сайта диспетчера трафика</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

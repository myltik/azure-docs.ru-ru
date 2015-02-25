<properties 
	pageTitle="Настройка доменного имени Network Solutions для веб-сайта Azure, использующего Traffic Manager" 
	description="Информация об использовании доменного имени из GoDaddy для веб-сайтов Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Личный домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Сетевые решения</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Веб-сайт с использованием диспетчера Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся указания по использованию личного доменного имени, приобретенного у [Network Solutions](https://www.networksolutions.com), для веб-сайтов Azure.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общая информация о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для личного домена](#bkmk_configurecname)
-   [Включение Traffic Manager для веб-сайта](#enabledomain)

<h2><a name="understanding-records"></a>Общая информация о записях DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Network Solutions. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Войдите на networksolutions.com с использованием своей учетной записи и выберите **Моя учетная запись** в правом верхнем углу.

3. На вкладке **Мои продукты и услуги** выберите **Изменить DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. В разделе **Управление <ваше_доменное_имя>** страницы **Доменные имена** выберите **Изменить дополнительные записи DNS**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. Страница **Обновление дополнительных записей DNS** содержит раздел для каждого типа записи с расположенной ниже кнопкой **Изменить**. Для записей CNAME используйте раздел **Псевдоним узла (записи CNAME)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5. При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые. 

	При добавлении записи CNAME необходимо задать в поле **Псевдоним** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо выбрать круглое поле рядом с полем **Другой узел**, а также задать в поле **Другой узел** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого с вашим веб-сайтом Azure. Например, **contoso.trafficmanager.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.
	
	![cname form](./media/web-sites-custom-domain-name/ns-cnametm.png)

5. По завершении добавления или изменения записей нажмите кнопку **Продолжить** для просмотра изменений. Выберите **Сохранить только изменения** для сохранения изменений.

<h2><a name="enabledomain"></a>домена</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->

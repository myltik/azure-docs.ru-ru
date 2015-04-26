<properties 
	pageTitle="Настройка доменного имени DomainDiscover для веб-сайта Azure" 
	description="Узнайте, как настроить веб-сайт Azure для использования доменного имени, зарегистрированного в DomainDiscover - TierraNet" 
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
	ms.author="larryfr,jroth"/>

#Настройка личного доменного имени для веб-сайта Azure (DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Личный домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Сетевые решения</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Веб-сайт с использованием диспетчера Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся указания по использованию личного доменного имени, приобретенного у [DomainDiscover.com](https://domaindiscover.com), для веб-сайтов Azure. DomainDiscover.com является частью TierraNet.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общая информация о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для личного домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общая информация о записях DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых DomainDiscover. Выполните следующие действия, чтобы найти средства DNS для DomainDiscover.com.

1. Войдите на DomainDiscover.com (TierraNet) с использованием своей учетной записи, выбрав **Панель управления** в меню **Вход**.

    ![Меню "Вход" DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. На странице **Доменные службы** выберите домен, который требуется использовать для веб-сайта Azure.

    ![Страница управления доменом](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. В разделе "Параметры домена" нажмите кнопку **Изменить** для элемента **Служба DNS**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. В окне **Управление DNS** выберите тип записи DNS для добавления в список **Добавление записей**. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. На следующей странице введите значения записи DNS. Затем нажмите кнопку **Добавить**.

    ![Кнопка изменения DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * При добавлении записи CNAME необходимо сначала выбрать элемент **CNAME (псевдоним)** на странице **Управление DNS**. Затем задайте в поле **Узел** поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Имя узла псевдонима** доменное имя **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Затем укажите срок жизни (TTL), например 1800 секунд.

    * При добавлении записи A необходимо сначала выбрать элемент **A** на странице **Управление DNS**. Затем задайте в поле **Узел** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). В поле **IP-адрес** необходимо задать IP-адрес веб-сайта Azure. Затем укажите срок жизни (TTL), например 1800 секунд.

		> [AZURE.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Узел** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Имя узла псевдонима**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Узел** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Имя узла псевдонима**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.


5. При добавлении записи A может появиться предупреждение о том, что существующая запись A для вашего домена не является неактивной. Он использует последнюю измененную запись, и DomainDirect уже имеет запись A по умолчанию для имени корневого домена. Можно либо положиться на этот приоритет, либо удалить запись A по умолчанию, нажав кнопку **УДАЛИТЬ**.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->

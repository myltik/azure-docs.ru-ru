<properties 
	pageTitle="Настройка доменного имени Directnic для веб-сайта Azure, использующего Traffic Manager" 
	description="Узнайте, как настроить веб-сайт Azure, использующий диспетчер трафика, для использования доменного имени, зарегистрированного в Directnic" 
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

#Настройка имени пользовательского домена для веб-сайта Windows Azure, использующего Traffic Manager (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Личный домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Сетевые решения</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Веб-сайт с использованием диспетчера Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся указания по использованию личного доменного имени, приобретенного у [DirectNic.com](https://directnic.com), для веб-сайтов Azure.

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

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, вам необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Directnic. Выполните следующие шаги, чтобы найти инструменты DNS для Directnic.com.

1. Войдите на Directnic.com с использованием своей учетной записи и выберите **Мои службы** и **Домены**. 

    ![Меню служб Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Щелкните по имени домена, которое вы хотите использовать для веб-сайта Azure.

2. На странице управления для вашего домена нажмите кнопку **Управление** для **DNS** в области **Службы**.

    ![Панель служб](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Добавьте записи DNS, заполнив поля **Тип**, **Имя** и **Данные**. По завершении нажмите кнопку **Добавить запись**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    * При добавлении записи CNAME необходимо задать в поле **Имя** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Данные** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, нагрузка на который выравнивается с помощью диспетчера трафика.

<h2><a name="enabledomain"></a>домена</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->

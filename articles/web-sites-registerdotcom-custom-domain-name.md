<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Настройка доменного имени Register.com для веб-сайта Azure" metaKeywords ="Azure, веб-сайты Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Веб-сайт с использованием диспетчера трафика</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В этой статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Register.com](https://www.register.com), для веб-сайтов Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для пользовательского домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общие сведения о записях DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Register.com. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход на register.com с использованием своей учетной записи и выберите **Ваша учетная запись** в правом верхнем углу для просмотра доменов, а затем выберите имя пользовательского домена.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Выполните прокрутку вниз до элемента **Дополнительные технические параметры**. Ссылки в этом разделе позволяют управлять записями для вашего домена.

	* Для записей A используйте ссылку **Изменить записи IP-адресов**.
	* Для записей CNAME используйте ссылку **Изменить записи псевдонимов доменов**.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые. Эта форма одинакова для записей CNAME и A.

	*  При добавлении записи CNAME необходимо задать в поле **.mydomainname.com** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **Псевдоним** **www** со значением **Другой узел** **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> ИЛИ
		> 
		> * Значение **Псевдоним** **awverify.www** со значением **Другой узел** **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**.
		> 
		> Эта запись CNAME позволяет Azure удостовериться, что именно вы являетесь владельцем домена, описанного в записи A.

	*  При добавлении записи A необходимо задать в поле **.mydomainname.com** тот поддомен, который вы хотите использовать (например, **www**). Оставьте это поле пустым, чтобы задать корневой домен, или используйте **\** для создания сопоставления по подстановочным знакам. Вам необходимо задать в поле **Указывает на** IP-адрес вашего веб-сайта Azure.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. По завершении добавления или изменения записей нажмите кнопку **Продолжить** для просмотра изменений. Выберите **Продолжить** еще раз, чтобы сохранить изменения.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

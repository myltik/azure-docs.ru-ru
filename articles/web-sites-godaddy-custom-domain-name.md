<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Настройка доменного имени GoDaddy для веб-сайта Azure"metaKeywords ="Azure, веб-сайты Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Настройка пользовательского имени домена для веб-сайта Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Веб-сайт с использованием диспетчера трафика</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Go Daddy](https://godaddy.com), для веб-сайтов Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS](#understanding-records)
-   [Добавление записи DNS для пользовательского домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общие сведения о записях DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Добавление записи DNS для пользовательского домена</h2>

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых GoDaddy. Выполните следующие действия, чтобы найти средства DNS для GoDaddy.com.

1. Выполните вход на GoDaddy.com с использованием своей учетной записи и выберите **Моя учетная запись** и **Управление доменами**. Выберите имя домена, которое вы хотите использовать для веб-сайта Azure, и выберите **Управление DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. На странице **Сведения о домене** прокрутите до пункта **Файл зоны DNS**. Этот раздел используется в целях добавления и изменения записей DNS для доменного имени. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Выберите **Добавить запись**, чтобы добавить существующую запись.

	Чтобы **отредактировать** существующую запись, выберите значок с карандашом и бумагой рядом с записью.

	> [WACOM.NOTE] Перед добавлением новых записей обратите внимание, что GoDaddy уже создал записи DNS для таких популярных поддоменов (**Узел** в редакторе), как **email**, **files**, **mail** и др. Если имя, которое вы хотите использовать, уже существует, измените существующую запись вместо того, чтобы создавать новую.

4. При добавлении записи необходимо сначала выбрать тип записи.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Далее вы должны ввести **Узел** (пользовательский домен или поддомен) и то, что будет значиться в поле **Указывает на**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* При добавлении **записи A (узел)** вам необходимо задать в поле **Узел** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), * (подстановочный знак для сопоставления с несколькими поддоменами), либо поддомен, который вы хотите использовать (например, **www**). Вам необходимо задать в поле **Указывает на** IP-адрес вашего веб-сайта Azure.
	
		> [WACOM.NOTE] При добавлении записи A (узла) вам необходимо также добавить запись CNAME со следующей конфигурацией:
		> 
		> * Значение **Узел** **awverify**, которое **указывает** на значение **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Запись CNAME позволяет Azure удостовериться, что именно вы являетесь владельцем домена, указанного в записи A.

	* При добавлении записи **(псевдонима) CNAME** необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.


5. По завершении добавления или изменения записей нажмите кнопку **Завершить** для сохранения изменений.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


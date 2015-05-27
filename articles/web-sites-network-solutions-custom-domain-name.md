<properties 
	pageTitle="Настройка доменного имени Network Solutions для веб-сайта Azure" 
	description="Информация об использовании доменного имени из Network Solutions для веб-сайтов Azure" 
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

#Настройка имени пользовательского домена для веб-сайта Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/web-sites-custom-domain-name" title="Настраиваемый домен">Настраиваемый домен</a><a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий диспетчер трафика ">Веб-сайт, использующий диспетчер трафика</a></div>

[AZURE.INCLUDE [пошаговые инструкции по работе с облачными службами и css](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [общие сведения](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Network Solutions](https://www.networksolutions.com), для веб-сайтов Azure.

[AZURE.INCLUDE [нижний колонтитул](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS](#understanding-records)
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима](#bkmk_configsharedmode)
-   [Добавление записи DNS для пользовательского домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общие сведения о записях DNS</h2>

[AZURE.INCLUDE [общие сведения о dns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Добавление записи DNS для пользовательского домена</h2>

Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Network Solutions. Выполните следующие действия, чтобы найти и использовать средства DNS для networksolutions.com.

1. Выполните вход на networksolutions.com с использованием своей учетной записи и выберите **Моя учетная запись** в правом верхнем углу.

3. На вкладке **Мои продукты и услуги** выберите **Изменить DNS**.

	![страница изменения DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

2. В разделе **Управление <yourdomainname>** страницы **Доменные имена** выберите **Изменить дополнительные записи DNS**.

	![страница доменных имен с выбранным элементом изменения дополнительных записей DNS](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. **Страница **Обновление дополнительных записей DNS** содержит раздел для каждого типа записи с расположенной ниже кнопкой **Изменить.
	
	* Для записей A используйте раздел **IP-адрес (записи A)**.
	* Для записей CNAME используйте раздел **Псевдоним узла (записи CNAME)**.

	![страница обновления дополнительных записей DNS](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые.

	> [AZURE.NOTE]Перед добавлением новых записей обратите внимание на то, что Network Solutions уже создала записи DNS по умолчанию для таких параметров, как корневой домен ("@") и запись подстановочных знаков ("*") для субдоменов. Если нужная вам запись уже существует, измените ее, а не создавайте новую.

	* При добавлении записи CNAME необходимо задать в поле **Псевдоним** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо выбрать круглое поле рядом с полем **Другой узел**, а также задать в поле **Другой узел** доменное имя **.azurewebsites.net** своего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.
	
		![форма CNAME](./media/web-sites-custom-domain-name/ns-cname.png)

		> [AZURE.NOTE]Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **Псевдоним** **www** со значением **Другой узел** **<имя_вашего_веб-сайта>.azurewebsites.net**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Псевдоним** и значение **awverify.<имя_вашего_веб-сайта>.azurewebsites.net** в поле **Другой узел**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

	* При добавлении записи A необходимо задать значение поля **Хост** в виде **@** (представляет собой имя корневого домена, например **contoso.com**), * (подстановочный знак для сопоставления нескольких субдоменов) или субдомена, который вы хотите использовать (например, **www**). Необходимо задать в поле **Числовой IP-адрес** IP-адрес вашего веб-сайта Azure.

		![форма записи А](./media/web-sites-custom-domain-name/ns-arecord.png)

5. По завершении добавления или изменения записей нажмите кнопку **Продолжить** для просмотра изменений. Выберите **Сохранить только изменения** для сохранения изменений.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-enable-on-web-site.md)]

<!--HONumber=54-->
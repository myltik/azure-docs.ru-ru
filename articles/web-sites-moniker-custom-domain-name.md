<properties title="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Moniker" pageTitle="Настройка доменного имени Moniker для веб-сайта Azure" metaKeywords="Azure, веб-сайты Azure, доменное имя" description="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Moniker.com](https://moniker.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Moniker. Выполните следующие действия, чтобы найти средства DNS для Moniker.com.

1. Выполните вход на Moniker.com с использованием своей учетной записи и выберите **Мои домены** и **Управление шаблонами**.

    ![Страница моих доменов для Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. На странице **Управление шаблонами зоны** выберите **Создать новый шаблон**.

    ![Управление шаблонами зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Заполните поле **Имя шаблона**. 

5. Затем создайте запись DNS путем выбора элемента **Тип записи**. Затем заполните поля **Имя узла** и **Адрес**. 

    ![Создание шаблона зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    * При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

    * При добавлении записи A необходимо задать в поле **Имя узла** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), либо поддомен, который вы хотите использовать (например, **www**). Необходимо задать в поле **Адрес** IP-адрес вашего веб-сайта Azure.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Имя узла** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Адрес**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Имя узла** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Адрес**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

7. Нажмите кнопку **Добавить**, чтобы добавить запись.

8. Когда все записи будут добавлены, нажмите кнопку **Сохранить**.

5. Выберите элемент **Диспетчер доменов**, чтобы вернуться в список доменов.

6. Установите флажок для конечного домена, а затем щелкните элемент **Управление шаблонами** еще раз.

7. Найдите новый шаблон, созданный в предыдущих действиях. Выберите ссылку **поместить выбранные домены (1) в этот шаблон**.

    ![Создание шаблона зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<properties title="Узнайте, как настроить веб-сайт Azure на использование доменного имени, зарегистрированного в eNom" pageTitle="Настройка доменного имени eNom для веб-сайта Azure" metaKeywords="Windows Azure, веб-сайты Windows Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом">Использование Traffic Manager веб-сайтом</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [eNom](https://enom.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых eNom. Выполните следующие действия, чтобы найти средства DNS для enom.com.

1. Выполните вход на eNom с использованием своей учетной записи и выберите **Домены** и **Мои домены**. При этом отображаются ваши доменные имена.

2. На странице **Мои домены** выберите значение **Записи узла** в поле **Управление доменом**. При этом отображаются поля записей узла.

	![Вкладка файла зоны DNS](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. Редактор записей узла позволяет выбрать определенный тип записи с помощью поля **Тип записи**. Для веб-сайтов Azure следует использовать только значение **CNAME (псевдоним)** или **A (адрес)**.

	![редактор файлов зоны](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Перед добавлением записей в файл зоны обратите внимание, что eNom уже создал записи DNS для корневого домена ("@") и подстановочный знак для поддоменов ("\ *"). Если вы хотите перенаправить корневой домен на свой веб-сайт или использовать запись A с подстановочным знаком, следует изменить эти записи вместо создания новых.

	* При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

		> [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
		> 
		> * Значение **www** в поле **Псевдоним** и значение **&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Другой узел**.
		> 
		> ИЛИ
		> 
		> * Значение **awverify.www** в поле **Псевдоним** и значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net** в поле **Другой узел**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

	* При добавлении записи A необходимо задать в поле **Имя узла** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), * (подстановочный знак для сопоставления с несколькими поддоменами), либо конкретный поддомен, который вы хотите использовать (например, **www**.) Необходимо задать в поле **Адрес** IP-адрес вашего веб-сайта Azure.

		> [WACOM.NOTE] При добавлении записи A необходимо также добавить запись CNAME с узлом **awverify** и указать в поле **Указывает на** значение **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net.

5. По завершении добавления или изменения записей нажмите кнопку **Сохранить** для сохранения изменений.

<h2><a name="enabledomain"></a>Включение доменного имени на веб-сайте</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


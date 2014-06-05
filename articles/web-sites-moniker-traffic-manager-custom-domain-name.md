<properties title="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование доменного имени, зарегистрированного в Moniker" pageTitle="Настройка доменного имени Moniker для веб-сайта Azure, использующего Traffic Manager" metaKeywords="Windows Azure, веб-сайты Windows Azure, Moniker, Traffic Manager" description="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование доменного имени, зарегистрированного в Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом" class="current">Использование Traffic Manager веб-сайтом</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Moniker](https://moniker.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Moniker. Выполните следующие действия, чтобы найти средства DNS для Moniker.com.

1. Выполните вход на Moniker.com с использованием своей учетной записи и выберите **Мои домены** и **Управление шаблонами**.

    ![Страница моих доменов для Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. На странице **Управление шаблонами зоны** выберите **Создать новый шаблон**.

    ![Управление шаблонами зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Заполните поле **Имя шаблона**. 

5. Затем создайте запись DNS путем выбора элемента **Тип записи**. Затем заполните поля **Имя узла** и **Адрес**. 

    ![Создание шаблона зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.
	 
7. Нажмите кнопку **Добавить**, чтобы добавить запись.

8. Когда все записи будут добавлены, нажмите кнопку **Сохранить**.

5. Выберите элемент **Диспетчер доменов**, чтобы вернуться в список доменов.

6. Установите флажок для конечного домена, а затем щелкните элемент **Управление шаблонами** еще раз.

7. Найдите новый шаблон, созданный в предыдущих действиях. Выберите ссылку **поместить выбранные домены (1) в этот шаблон**.

    ![Создание шаблона зоны Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Включение Traffic Manager для веб-сайта</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


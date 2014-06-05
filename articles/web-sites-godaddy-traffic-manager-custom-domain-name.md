<properties title="Узнайте, как настроить веб-сайт Azure, использующий Traffic Manager, на использование доменного имени, зарегистрированного в GoDaddy" pageTitle="Настройка доменного имени GoDaddy для веб-сайта Azure, использующего Traffic Manager" metaKeywords="Azure, веб-сайты Azure, доменное имя" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Использование Traffic Manager веб-сайтом" class="current">Использование Traffic Manager веб-сайтом</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Go Daddy](https://godaddy.com), для веб-сайтов Azure.

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

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Go Daddy. Выполните следующие действия, чтобы найти средства DNS для GoDaddy.com.

1. Выполните вход на GoDaddy.com с использованием своей учетной записи и выберите **Моя учетная запись** и **Управление доменами**. Выберите имя домена, которое вы хотите использовать для веб-сайта Azure.

	![страница пользовательского домена для GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. На странице **Сведения о домене** откройте вкладку **Файл зоны DNS**. Этот раздел используется в целях добавления и изменения записей DNS для доменного имени. Нажмите кнопку **Изменить**, чтобы отобразить **редактор файлов зоны**.

	![Вкладка файла зоны DNS](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. **Редактор файлов зоны** разбит на разделы для каждого типа записи, начиная с записей A (указанных как **A (узел)**) в начале списка, и заканчивая записями CNAME (указанными как **CNAME (псевдоним)**). Чтобы добавить новую запись, используйте кнопку **Быстрое добавление**, расположенную под соответствующим разделом. Чтобы изменить существующую запись, выберите ее и измените существующие данные.

	![редактор файлов зоны](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [WACOM.NOTE] Перед добавлением записей в файл зоны обратите внимание, что GoDaddy уже создал записи DNS для таких популярных поддоменов (**Узел** в редакторе), как **email**, **files**, **mail** и другие. Если имя, которое вы хотите использовать, уже существует, измените существующую запись вместо того, чтобы создавать новую.

	При добавлении записи CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

	> [WACOM.NOTE] Записи CNAME необходимо использовать только при сопоставлении имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

5. По завершении добавления или изменения записей нажмите кнопку **Сохранить файл зоны** для сохранения изменений.

<h2><a name="enabledomain"></a>Включение Traffic Manager для веб-сайта</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


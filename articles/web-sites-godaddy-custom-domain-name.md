<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Настройка пользовательского имени домена для веб-сайта Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Go Daddy][Go Daddy], для веб-сайтов Azure.

[WACOM.INCLUDE [introfooter][introfooter]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configurecname"></a>Добавление записи DNS для пользовательского домена

Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых GoDaddy. Выполните следующие действия, чтобы найти средства DNS для GoDaddy.com.

1.  Выполните вход на GoDaddy.com с использованием своей учетной записи и выберите **Моя учетная запись** и **Управление доменами**. Выберите имя домена, которое вы хотите использовать для веб-сайта Azure и выберите **Управление DNS**.

    ![страница пользовательского домена для GoDaddy][страница пользовательского домена для GoDaddy]

2.  На странице **Сведения о домене** откройте вкладку **Файл зоны DNS**. Этот раздел используется в целях добавления и изменения записей DNS для доменного имени.

    ![Вкладка файла зоны DNS][Вкладка файла зоны DNS]

    Выберите **Добавить запись**, чтобы добавить существующую запись.

    Чтобы **отредактировать** существующую запись, выберите значок с карандашом и бумагой рядом с записью.

    > [WACOM.NOTE] Перед добавлением новых записей обратите внимание, что GoDaddy уже создал записи DNS для таких популярных поддоменов (**Узел** в редакторе), как **email**, **files**, **mail** и др. Если имя, которое вы хотите использовать, уже существует, измените существующую запись вместо того, чтобы создавать новую.

3.  При добавлении записи необходимо сначала выбрать тип записи.

    ![выберите тип записи][выберите тип записи]

    Далее вы должны ввести **Хост** (пользовательский домен или поддомен) и то, что будет значиться в поле **Указывает на**.

    ![добавление записи о зоне][добавление записи о зоне]

    -   При добавлении **записи A хоста** необходимо задать в поле **Узел** либо символ **\*\*@\***\* (представляющий имя корневого домена, например [contoso.com][contoso.com]), \* (подстановочный символ для сопоставления нескольких поддоменов) либо поддомен, который вы хотите использовать (например, [www][contoso.com]). Необходимо задать в поле **Указывает на** IP-адрес вашего веб-сайта Azure.

        > [WACOM.NOTE] При добавлении записи A (узла) необходимо также добавить запись CNAME со следующей конфигурацией:
        >
        > -   Значение **хоста** **awverify**, которое **Указывает на** значение **\<yourwebsitename\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

    -   При добавлении записи (псевдонима) CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

4.  По завершении добавления или изменения записей нажмите кнопку **Завершить** для сохранения изменений.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [modes][modes]]

  [Пользовательский домен]: /ru-ru/documentation/articles/web-sites-custom-domain-name "Пользовательский домен"
  [GoDaddy]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ru-ru/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Go Daddy]: https://godaddy.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [страница пользовательского домена для GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Вкладка файла зоны DNS]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [выберите тип записи]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [добавление записи о зоне]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
  [contoso.com]: mailto:**@*
  [modes]: ../includes/custom-dns-web-site-enable-on-web-site.md

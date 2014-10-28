<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов][пошаговое-css-руководство-по-облачным-службам-веб-сайтов]]

[WACOM.INCLUDE [введение][введение]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Go Daddy][Go Daddy], для веб-сайтов Azure.

[WACOM.INCLUDE [нижний-колонтитул-веб-сайта-tm][нижний-колонтитул-веб-сайта-tm]]

[WACOM.INCLUDE [нижний-колонтитул-введения][нижний-колонтитул-введения]]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для стандартного режима][Настройка веб-сайтов для стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение Traffic Manager для веб-сайта][Включение Traffic Manager для веб-сайта]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns][общие-сведения-о-dns]]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для стандартного режима

[WACOM.INCLUDE [режимы][режимы]]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых GoDaddy. Выполните следующие действия, чтобы найти средства DNS для GoDaddy.com.

1.  Выполните вход на GoDaddy.com с использованием своей учетной записи и выберите **Моя учетная запись** и **Управление доменами**. Выберите имя домена, которое вы хотите использовать для веб-сайта Azure.

    ![страница пользовательского домена для GoDaddy][страница пользовательского домена для GoDaddy]

2.  На странице **Сведения о домене** откройте вкладку **Файл зоны DNS**. Этот раздел используется в целях добавления и изменения записей DNS для доменного имени. Нажмите кнопку **Изменить**, чтобы отобразить **редактор файлов зоны**.

    ![Вкладка файла зоны DNS][Вкладка файла зоны DNS]

3.  **Редактор файлов зоны** разбит на разделы для каждого типа записи, начиная с записей A (указанных как **A (узел)**) в начале списка и заканчивая записями CNAME (указанными как **CNAME (псевдоним)**). Чтобы добавить новую запись, используйте кнопку **Быстрое добавление**, расположенную под соответствующим разделом. Чтобы изменить существующую запись, выберите ее и измените существующие данные.

    ![редактор файлов зоны][редактор файлов зоны]

    > [WACOM.ПРИМЕЧАНИЕ] Перед добавлением записей в файл зоны обратите внимание, что GoDaddy уже создал записи DNS для таких популярных поддоменов (**Узел** в редакторе), как **email**, **files**, **mail** и др. Если имя, которое вы хотите использовать, уже существует, измените существующую запись вместо того, чтобы создавать новую.

    При добавлении записи CNAME необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Указывает на** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

    > [WACOM.ПРИМЕЧАНИЕ] Записи CNAME необходимо использовать только для связи имени пользовательского домена с веб-сайтом, для которого реализована балансировка нагрузки с помощью Traffic Manager.

4.  По завершении добавления или изменения записей нажмите на кнопку **Сохранить файл зоны** для сохранения изменений.

## <a name="enabledomain"></a>Включение веб-сайта Traffic Manager

[WACOM.INCLUDE [режимы][1]]

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
  [пошаговое-css-руководство-по-облачным-службам-веб-сайтов]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [введение]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Go Daddy]: https://godaddy.com
  [нижний-колонтитул-веб-сайта-tm]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [нижний-колонтитул-введения]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [общие-сведения-о-dns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [режимы]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [страница пользовательского домена для GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Вкладка файла зоны DNS]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [редактор файлов зоны]: ./media/web-sites-custom-domain-name/godaddy-quickaddcname.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

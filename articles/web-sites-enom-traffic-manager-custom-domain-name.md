<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Настройка имени пользовательского домена для веб-сайта Azure, использующего Traffic Manager (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name/" title="Веб-сайты">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager" class="current">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов][пошаговое-css-руководство-по-облачным-службам-веб-сайтов]]

[WACOM.INCLUDE [введение][введение]]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [eNom][eNom], для веб-сайтов Azure.

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
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых eNom. Выполните следующие действия, чтобы найти средства DNS для enom.com.

1.  Выполните вход на eNom с использованием своей учетной записи и выберите **Домены** и **Мои домены**. При этом отображаются ваши доменные имена.

2.  На странице **Мои домены** выберите значение **Записи узла** в поле **Управление доменом**. При этом отображаются поля записей узла.

    ![Вкладка файла зоны DNS][Вкладка файла зоны DNS]

3.  Редактор записей узла позволяет выбрать определенный тип записи с помощью поля **Тип записи**. Для веб-сайтов Azure, использующих Traffic Manager, следует использовать только значение **CNAME (псевдоним)**, так как Traffic Manager работает только с записями CNAME.

    ![редактор файлов зоны][редактор файлов зоны]

    -   При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** доменное имя **.trafficmanager.net** профиля Traffic Manager, используемого для веб-сайта Azure. Например, **contoso.trafficmanager.net**.

4.  По завершении добавления или изменения записей нажмите кнопку **Сохранить** для сохранения изменений.

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
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name/ "Веб-сайты"
  [Веб-сайт, использующий Traffic Manager]: /ru-ru/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Веб-сайт, использующий Traffic Manager"
  [пошаговое-css-руководство-по-облачным-службам-веб-сайтов]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [введение]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [eNom]: https://enom.com
  [нижний-колонтитул-веб-сайта-tm]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [нижний-колонтитул-введения]: ../includes/custom-dns-web-site-intro-notes.md
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение Traffic Manager для веб-сайта]: #enabledomain
  [общие-сведения-о-dns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [режимы]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Вкладка файла зоны DNS]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [редактор файлов зоны]: ./media/web-sites-custom-domain-name/e-editrecordstm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md

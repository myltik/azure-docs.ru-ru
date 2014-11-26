<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Настройка пользовательского имени домена для веб-сайта Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [пошаговое-css-руководство-по-облачным-службам-веб-сайтов](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [введение](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [eNom][eNom], для веб-сайтов Azure.

[WACOM.INCLUDE [нижний-колонтитул-введения](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима][Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [общие-сведения-о-dns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима

[WACOM.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы сопоставить пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых eNom. Выполните следующие действия, чтобы найти средства DNS для enom.com.

1.  Выполните вход на eNom с использованием своей учетной записи и выберите пункты **Домены** и **Мои домены**. При этом отображаются ваши доменные имена.

2.  На странице **Мои домены** выберите значение **Записи узла** в поле **Управление доменом**. При этом отображаются поля записей узла.

    ![Вкладка файла зоны DNS][Вкладка файла зоны DNS]

3.  Редактор записей узла позволяет выбрать определенный тип записи с помощью поля **Тип записи**. Для веб-сайтов Azure следует использовать только значение **CNAME (псевдоним)** или **A (адрес)**.

    ![редактор файлов зоны][редактор файлов зоны]

    > [WACOM.ПРИМЕЧАНИЕ] Перед добавлением записей в файл зоны обратите внимание, что eNom уже создал записи DNS для корневого домена (<'@'>) и подстановочный знак для поддоменов ("\*"). Если вы хотите перенаправить корневой домен на свой веб-сайт или использовать запись A с подстановочным знаком, следует изменить эти записи вместо создания новых.

    -   При добавлении записи CNAME необходимо задать в поле **Имя узла** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо задать в поле **Адрес** имя домена **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.

        > [WACOM.ПРИМЕЧАНИЕ] Если вы будете использовать запись A, необходимо также добавить запись CNAME из одной из следующих конфигураций:
        >
        > -   Значение **Псевдоним** поддомена **www** со значением **Другой узел** поддомена **\<имя\_вашего\_веб-сайта\>.azurewebsites.net**.
        >
        > ИЛИ
        >
        > -   Значение **Псевдоним** поддомена **awverify.www** со значением **Другой узел** поддомена **awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

    -   При добавлении записи A необходимо задать в поле **Имя узла** либо символ <**@*>\* (представляющий имя корневого домена, например **contoso.com**), \* (подстановочный знак для сопоставления с несколькими поддоменами), либо конкретный поддомен, который вы хотите использовать (например, **www**). В поле **Адрес** необходимо задать IP-адрес вашего веб-сайта Azure.

        > [WACOM.ПРИМЕЧАНИЕ] При добавлении записи A необходимо также добавить запись CNAME с узлом **awverify** и указать в поле **Указывает на** значение \*\*awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net.

4.  По завершении добавления или изменения записей нажмите кнопку **Сохранить** для сохранения изменений.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]

  [eNom]: https://enom.com
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [Вкладка файла зоны DNS]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [редактор файлов зоны]: ./media/web-sites-custom-domain-name/e-editrecords.png

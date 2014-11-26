<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Настройка имени пользовательского домена для веб-сайта Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Веб-сайт, использующий Traffic Manager">Веб-сайт, использующий Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В данной статье приводятся инструкции по использованию имени пользовательского домена, приобретенного у [Network Solutions][1], для веб-сайтов Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общие сведения о записях DNS][Общие сведения о записях DNS]
-   [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима][Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]
-   [Добавление записи DNS для пользовательского домена][Добавление записи DNS для пользовательского домена]
-   [Включение домена на веб-сайте][Включение домена на веб-сайте]

## <a name="understanding-records"></a>Общие сведения о записях DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Добавление записи DNS для пользовательского домена

</p>
Чтобы связать пользовательский домен с веб-сайтом Azure, необходимо добавить новую запись в таблицу DNS для этого настраиваемого домена с помощью средств, предоставляемых Network Solutions. Выполните следующие действия, чтобы найти и использовать средства DNS для networksolutions.com.

1.  Выполните вход на networksolutions.com с использованием своей учетной записи и выберите **Моя учетная запись** в правом верхнем углу.

2.  На вкладке **Мои продукты и услуги** выберите **Изменить DNS**.

    ![страница изменения DNS][страница изменения DNS]

3.  В разделе **Управление <yourdomainname>** страницы **Доменные имена** выберите **Изменить дополнительные записи DNS**.

    ![страница доменных имен с выбранным элементом изменения дополнительных записей DNS][страница доменных имен с выбранным элементом изменения дополнительных записей DNS]

4.  **Страница** Обновление дополнительных записей DNS **содержит раздел для каждого типа записи с расположенной ниже кнопкой** Изменить</strong>.

    -   Для записей A используйте раздел **IP-адрес (записи A)**.
    -   Для записей CNAME используйте раздел **Псевдоним узла (записи CNAME)**.

    ![страница обновления дополнительных записей DNS][страница обновления дополнительных записей DNS]

5.  При нажатии кнопки **Изменить** появляется форма, позволяющая изменять существующие записи или добавлять новые.

    > [WACOM.NOTE] Перед добавлением новых записей обратите внимание, что Network Solutions уже создал несколько записей DNS по умолчанию для таких элементов, как корневой домен ("@"), и запись с подстановочным знаком (["@"]["@"]) для поддоменов. Если запись, которую вы хотите использовать, уже существует, измените ее вместо того, чтобы создавать новую.

    -   При добавлении записи CNAME необходимо задать в поле **Псевдоним** тот поддомен, который вы хотите использовать. Например, **www**. Необходимо выбрать круглое поле рядом с полем **Другой узел**, а также задать в поле **Другой узел** доменное имя **.azurewebsites.net** своего веб-сайта Azure. Например, **contoso.azurwebsites.net**. Оставьте значение **Выбрать** в поле **Ссылается на имя узла**, так как это поле не требуется при создании записи CNAME для использования с веб-сайтами Azure.

        ![форма CNAME][форма CNAME]

        > [WACOM.NOTE] Если вы будете использовать запись A, необходимо также добавить запись CNAME с одной из следующих конфигураций:
        >
        > -   Значение **псевдонима** **www**, которое **указывает на** значение **\<yourwebsitename\>.azurewebsites.net**.
        >
        > ИЛИ
        >
        > -   Значение **псевдонима** **awverify.www** со значением **Другой хост** для **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

    -   При добавлении записи A необходимо задать в поле **Имя узла** либо символ <**@*>\* (представляющий имя корневого домена, например **contoso.com**), \* (подстановочный символ для сопоставления нескольких поддоменов) либо поддомен, который вы хотите использовать (например, **www**). Необходимо задать в поле **Числовой IP-адрес** IP-адрес вашего веб-сайта Azure.

        ![форма записи А][форма записи А]

6.  По завершении добавления или изменения записей нажмите кнопку **Продолжить**, чтобы просмотреть изменения. Выберите **Сохранить только изменения** для сохранения изменений.

## <a name="enabledomain"></a>Включение доменного имени на веб-сайте

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [1]: https://www.networksolutions.com
  [Общие сведения о записях DNS]: #understanding-records
  [Настройка веб-сайтов для базового режима, режима общего доступа или стандартного режима]: #bkmk_configsharedmode
  [Добавление записи DNS для пользовательского домена]: #bkmk_configurecname
  [Включение домена на веб-сайте]: #enabledomain
  [страница изменения DNS]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [страница доменных имен с выбранным элементом изменения дополнительных записей DNS]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [страница обновления дополнительных записей DNS]: ./media/web-sites-custom-domain-name/ns-updateadvanced.png
  ["@"]: mailto:'@'
  [форма CNAME]: ./media/web-sites-custom-domain-name/ns-cname.png
  [форма записи А]: ./media/web-sites-custom-domain-name/ns-arecord.png

<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson"></tags>

# Настройка пользовательского имени домена для веб-сайта Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Пользовательский домен" class="current">Пользовательский домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name/" title="Веб-сайты" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Использование диспетчера трафика веб-сайтом">Использование диспетчера трафика веб-сайтом</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

Когда вы создаете веб-сайт, Azure назначает ему поддомен azurewebsites.net. Например, если имя вашего веб-сайта — **contoso**, то URL-адрес — **contoso.azurewebsites.net**. Azure также назначает виртуальный IP-адрес.

![поддомен contoso.azurewebsites.net][поддомен contoso.azurewebsites.net]

Для рабочего веб-сайта вы, возможно, хотите, чтобы пользователи видели имя пользовательского домена. В данной статье разъясняется, как настроить пользовательский домен для веб-сайтов Azure. (В данной статье приводятся общие инструкции для любого регистратора доменных имен. Вкладки в верхней части этой статьи содержат ссылки на несколько статей, посвященных отдельным регистраторам.)

[WACOM.INCLUDE [introfooter][introfooter]]

Содержание:

-   [Обзор][Обзор]
-   [Типы записей DNS][Типы записей DNS]
-   [Поиск виртуального IP-адреса][Поиск виртуального IP-адреса]
-   [Создание записей DNS][Создание записей DNS]
-   [Создание записи «awverify» (только записи A)][Создание записи «awverify» (только записи A)]
-   [Включение доменного имени на веб-сайте][Включение доменного имени на веб-сайте]

## Обзор

Ниже приведены общие шаги по настройке пользовательского имени домена:

1.  Зарезервируйте свое доменное имя. Данная статья не описывает этот процесс. Вы можете выбирать из большого числа регистраторов доменных имен. Сайт регистратора пошагово поможет вам пройти процесс регистрации.
2.  Создайте записи DNS, которые сопоставляют домен с вашим веб-сайтом Azure.
3.  Добавьте доменное имя на портал управления Azure.

В рамках этой упрощенной схемы есть несколько отдельных случаев, требующих внимания:

-   Сопоставление корневого домена. Корневой домен — это домен, который вы зарезервировали с помощью регистратора доменных имен. Например, **contoso.com**.
-   Сопоставление поддомена. Например, **blogs.contoso.com**. Вы можете сопоставить разные поддомены с разными веб-сайтами.
-   Сопоставление подстановочного знака. Например, **.contoso.com**. Запись с подстановочным знаком применяется ко всем поддоменам вашего домена.

[WACOM.INCLUDE [modes][modes]]

## Типы записей DNS

Система доменных имен (DNS) использует записи данных для сопоставления доменных имен с IP-адресами. Существует несколько типов записей DNS. Для веб-сайтов вы можете создать запись *A* или *CNAME*.

-   Запись A **(адрес)** сопоставляет доменное имя с IP-адресом.
-   Запись **CNAME (каноническое имя)** сопоставляет доменное имя с другим доменным именем. DNS использует второе имя для поиска адреса. Пользователи по-прежнему видят в своем браузере первое доменное имя. Например, вы можете сопоставить contoso.com с сайтом *\<ваш\_сайт\>*.azurewebsites.net.

Если IP-адрес меняется, то запись CNAME остается действительной, а запись A требует обновления. Однако некоторые регистраторы доменных имен не разрешают записи CNAME для корневого домена или доменов с подстановочными знаками. В таком случае вам необходимо использовать запись A.

> [WACOM.NOTE] IP-адрес может измениться, если вы удалите и заново создадите свой веб-сайт или измените режим веб-сайта на бесплатный.

## Поиск виртуального IP-адреса

Пропустите этот шаг, если вы создаете запись CNAME. Чтобы создать запись А, необходим виртуальный IP-адрес вашего веб-сайта. Для получения IP-адреса выполните следующее:

1.  Откройте в браузере [портал управления Azure][портал управления Azure].
2.  На вкладке **Веб-сайты** щелкните имя своего сайта и выберите **Панель мониторинга**.
3.  В нижней части страницы выберите **Управление доменами**. (Если этот параметр отключен, убедитесь, что вы используете режим общего доступа, базовый или стандартный режим. Дополнительную информацию см. в разделе [Масштабирование веб-сайтов][Масштабирование веб-сайтов].)

    ![][]

4.  IP-адрес приведен в нижней части диалогового окна.

    ![][1]

## Создание записей DNS

Войдите в систему вашего регистратора доменных имен и воспользуйтесь его средством добавления записи A или записи CNAME. Веб-сайты регистраторов слегка отличаются между собой, но мы приводим здесь некоторые общие правила.

1.  Найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку можно найти, открыв сведения своей учетной записи и найдя такую ссылку, как **Мои домены**.
2.  Когда вы нашли страницу управления, поищите на ней ссылку, которая позволяет добавлять или изменять записи DNS. Она может иметь название **Файл зоны**, **Записи DNS** или **Дополнительно**.

Эта страница может содержать список записей A и CNAME или предоставлять раскрывающийся список для выбора типа записи. На ней также могут использоваться другие названия типов записей, такие как **запись IP-адреса** вместо записи A или **запись псевдонима** вместо записи CNAME. Как правило, регистратор сам создает для вас несколько записей, поэтому для корневого домена или общих поддоменов, таких как **www**, уже могут отображаться записи.

В процессе создания или изменения записи соответствующие поля позволят вам сопоставить ваше доменное имя с IP-адресом (для записей A) или с другим доменом (для записей CNAME). Для записи CNAME выполните сопоставление *от* своего пользовательского домена *к* поддомену azurewebsites.net.

Во многих средствах регистраторов требуется ввести не полное доменное имя, а только поддоменную часть своего домена. Также во многих инструментах для обозначения корневого домена используется символ «@». Например:

| Узел | Тип записи        | IP-адрес или URL-адрес    |
|------|-------------------|---------------------------|
| @    | A (адрес)         | 127.0.0.1                 |
| www  | CNAME (псевдоним) | contoso.azurewebsites.net |

Предположим, что пользовательское имя домена — contoso.com. Тогда будут созданы такие записи:

-   **contoso.com** сопоставлен 127.0.0.1.
-   **www.contoso.com** сопоставлен с **contoso.azurewebsites.net**.

## Создание записи «awverify» (только записи A)

Если вы создаете запись A, то веб-сайтам Azure также необходима специальная запись CNAME. Она используется, чтобы проверить, являетесь ли вы владельцем домена, которым пытаетесь воспользоваться. Эта запись CNAME должна иметь следующий вид.

-   *Если запись A сопоставляет корневой домен или домен с подстановочными знаками:* Создайте запись CNAME с сопоставлением от **awverify.\<ваш\_домен\>** с **awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net**. Например, если запись A создана для **contoso.com**, то создайте запись CNAME для **awverify.contoso.com**.
-   *Если запись A сопоставляет конкретный поддомен:* Создайте запись CNAME с сопоставлением от **awverify.\<поддомен\>** с **awverify.\<имя\_вашего\_веб-сайта\>.azurewebsites.net**. Например, если запись A создана для **blogs.contoso.com**, то создайте запись CNAME для **awverify.blogs.contoso.com**.

Посетители вашего сайта не будут видеть поддомен awverify; он используется Azure только для проверки вашего домена.

## Включение доменного имени на веб-сайте

[WACOM.INCLUDE [modes][2]]



  [Пользовательский домен]: /ru-ru/documentation/articles/web-sites-custom-domain-name "Пользовательский домен"
  [GoDaddy]: /ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ru-ru/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ru-ru/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ru-ru/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ru-ru/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Веб-сайт]: /ru-ru/documentation/articles/web-sites-custom-domain-name/ "Веб-сайты"
  [Использование диспетчера трафика веб-сайтом]: /ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Использование диспетчера трафика веб-сайтом"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [поддомен contoso.azurewebsites.net]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Обзор]: #overview
  [Типы записей DNS]: #dns-record-types
  [Поиск виртуального IP-адреса]: #find-the-virtual-ip-address
  [Создание записей DNS]: #create-the-dns-records
  [Создание записи «awverify» (только записи A)]: #awverify
  [Включение доменного имени на веб-сайте]: #enable-the-domain-name-on-your-website
  [modes]: ../includes/custom-dns-web-site-modes.md
  [портал управления Azure]: https://manage.windowsazure.com
  [Масштабирование веб-сайтов]: http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-scale/
  []: media/web-sites-custom-domain-name/dncmntask-cname-6.png
  [1]: media/web-sites-custom-domain-name/ipaddress.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md

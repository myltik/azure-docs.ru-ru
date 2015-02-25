<properties 
	pageTitle="Настройка пользовательского имени домена для веб-сайта Azure" 
	description="Использование личного доменного имени для веб-сайтов Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth, mwasson"/>

#Настройка пользовательского имени домена для веб-сайта Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Личный домен</a><a href="/ru-ru/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ru-ru/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Сетевые решения</a><a href="/ru-ru/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ru-ru/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ru-ru/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ru-ru/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ru-ru/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ru-ru/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Веб-сайт</a> | <a href="/ru-ru/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Веб-сайт с использованием диспетчера Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

При создании веб-сайта Azure назначает ему поддомен azurewebsites.net. Например, если веб-сайт называется **contoso**, он будет иметь URL-адрес **contoso.azurewebsites.net**. Azure также назначает виртуальный IP-адрес.

![Поддомен contoso.azurewebsites.net][subdomain]

Для рабочего веб-сайта, возможно, потребуется, чтобы пользователи видели имя пользовательского домена. В данной статье разъясняется, как настроить пользовательский домен для веб-сайтов Azure. (В данной статье приводятся общие инструкции для любого регистратора доменных имен. Вкладки в верхней части этой статьи содержат ссылки на несколько статей, посвященных конкретным регистраторам.)

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:


-   [Обзор]
-   [Типы записей DNS]
-   [Поиск виртуального IP-адреса]
-   [Создание записей DNS]
-   [Создание записи "awverify" (только записи A)](#awverify)
-   [Включение доменного имени на веб-сайте]


## Обзор

Ниже приведены общие шаги по настройке пользовательского имени домена:

1. Зарезервируйте доменное имя. Данная статья не описывает этот процесс. Вы можете выбирать из большого числа регистраторов доменных имен. Сайт регистратора содержит пошаговое руководство по процессу регистрации.
1. Создайте записи DNS, которые сопоставляют домен с вашим веб-сайтом Azure. 
1. Добавьте доменное имя на портал управления Azure. 

В рамках этой упрощенной схемы есть несколько отдельных случаев, требующих внимания:

- Сопоставление корневого домена. Корневой домен - это домен, который вы зарезервировали с помощью регистратора доменных имен. Например, **contoso.com**.
- Сопоставление поддомена. Например, **blogs.contoso.com**.  Можно сопоставить разные поддомены с разными веб-сайтами.
- Сопоставление подстановочного знака. Например, ***.contoso.com**. Запись с подстановочным знаком применяется ко всем поддоменам вашего домена. 
 
[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-modes.md)]


## Типы записей DNS

Система доменных имен (DNS) использует записи данных для сопоставления доменных имен с IP-адресами. Существует несколько типов записей DNS. Для веб-сайтов можно создать запись *A* или *CNAME*.

- А-запись **(адрес)** сопоставляет доменное имя с IP-адресом. 
- Запись **CNAME (каноническое имя)** сопоставляет доменное имя с другим доменным именем. DNS использует второе имя для поиска адреса. Пользователи по-прежнему видят в своем браузере первое доменное имя. Например, вы можете сопоставить contoso.com с сайтом &lt;ваш_сайт&gt;.azurewebsites.net. 

Если IP-адрес изменился, то запись CNAME останется действительной, а А-запись потребует обновления. Однако некоторые регистраторы доменных имен не разрешают записи CNAME для корневого домена или доменов с подстановочными знаками. В таком случае вам необходимо использовать запись A. 

> [AZURE.NOTE] IP-адрес может измениться при удалении и повторном создании веб-сайта или изменения его режима на бесплатный.


## Поиск виртуального IP-адреса 

Пропустите этот шаг, если создается запись CNAME. Чтобы создать запись А, необходим виртуальный IP-адрес вашего веб-сайта. Чтобы получить IP-адрес, выполните следующие действия.

1.	Откройте в браузере [Портал управления Azure](https://manage.windowsazure.com).
2.	На вкладке **Веб-сайты** щелкните имя своего сайта и выберите **Панель мониторинга**.
3.	В нижней части страницы выберите **Управление доменами**. (Если этот параметр отключен, убедитесь, что вы используете режим общего доступа, базовый или стандартный режим. Дополнительную информацию см. в разделе [Масштабирование веб-сайтов](http://www.windowsazure.com/ru-ru/documentation/articles/web-sites-scale/).) 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	IP-адрес приведен в нижней части диалогового окна.

	![](media/web-sites-custom-domain-name/ipaddress.png)

## Создание записей DNS

Войдите в систему вашего регистратора доменных имен и воспользуйтесь его средством добавления А-записи или записи CNAME. Веб-сайты регистраторов слегка отличаются между собой, но мы приводим здесь некоторые общие правила. 

1.	Найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто эту ссылку можно найти, открыв информацию о своей учетной записи и найдя такую ссылку, как **Мои домены**.
2.	Найдя страницу управления, отыщите на ней ссылку, которая позволяет добавлять или изменять записи DNS. Это может быть **Файл зоны**, **Записи DNS** или ссылка на **Дополнительные настройки**.

Эта страница может содержать список А-записей и CNAME или содержать раскрывающийся список для выбора типа записи. На ней также могут использоваться другие названия типов записей, такие как **запись IP-адреса** вместо записи A или **запись псевдонима** вместо записи CNAME.  Как правило, регистратор сам создает для вас несколько записей, поэтому для корневого домена или общих поддоменов, таких как **www**, уже могут отображаться записи.

В процессе создания или изменения записи соответствующие поля позволят сопоставить доменное имя с IP-адресом (для А-записей) или с другим доменом (для записей CNAME). Для записи CNAME будете выполняться сопоставление *from* своего личного домена *to* поддомену azurewebsites.net. 

Во многих средствах регистраторов требуется ввести не полное доменное имя, а только поддоменную часть своего домена. Также во многих инструментах для обозначения корневого домена используется символ "@". Например:

<table cellspacing="0" border="1">
<tr>
	<th>Узел</th>
	<th>Тип записи</th>
	<th>IP-адрес или URL-адрес</th>
</tr>
<tr>
	<td>@</td>
	<td>A (адрес)</td>
	<td>127.0.0.1</td>
</tr>
<tr>
	<td>www</td>
	<td>CNAME (псевдоним)</td>
	<td>contoso.azurewebsites.net</td>
</tr>
</table>

Предположим, что личное доменное имя - 'contoso.com'. Тогда будут созданы такие записи:

- **contoso.com** сопоставлен 127.0.0.1.
- **www.contoso.com** сопоставлен с **contoso.azurewebsites.net**.


<h2 id="awverify">Создание записи "awverify" (только записи A)</h2>

При создании А-записи веб-сайтам Azure также необходима специальная запись CNAME. Она необходима для того, чтобы проверить, являетесь ли вы владельцем домена, которым пытаетесь воспользоваться. Эта запись CNAME должна иметь следующий вид. 

- *Если запись "A" сопоставляется с корневым доменом или с доменом с подстановочными знаками,* создайте запись CNAME, которая будет сопоставляться из **awverify.&lt;ваш_домен&gt;** с **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**. Например, если запись "A" используется для **contoso.com**, создайте запись CNAME для **awverify.contoso.com**.
- *Если запись "A" сопоставляется с корневым определенным поддоменом,* создайте запись CNAME, которая будет сопоставляться из **awverify.&lt;ваш_поддомен&gt;** с **awverify.&lt;имя_вашего_веб-сайта&gt;.azurewebsites.net**. Например, если запись "A" используется для **blogs.contoso.com**, создайте запись CNAME для **awverify.blogs.contoso.com**.

Посетители вашего сайта не будут видеть поддомен awverify; он используется Azure только для проверки вашего домена.

## Включение доменного имени на веб-сайте

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[Обзор]: #overview
[Типы записей DNS]: #dns-record-types
[Поиск виртуального IP-адреса]: #find-the-virtual-ip-address
[Создание записей DNS]: #create-the-dns-records
[Включение доменного имени на веб-сайте]: #enable-the-domain-name-on-your-website

<!-- Images -->
[поддомен]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=42-->

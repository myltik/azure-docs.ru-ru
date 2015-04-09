<properties 
	pageTitle="Настройка личного доменного имени в службе приложений Azure (GoDaddy)" 
	description="Узнайте, как использовать доменное имя из GoDaddy с веб-приложениями Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Настройка личного доменного имени в службе приложений Azure (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

В этой статье вы найдете инструкции по использованию личного доменного имени, приобретенного у [Go Daddy](https://godaddy.com), с [веб-приложениями службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Содержание:

-   [Общая информация о записях DNS](#understanding-records)
-   [Добавление записи DNS для личного домена](#bkmk_configurecname)
-   [Включение домена на веб-сайте](#enabledomain)

<h2><a name="understanding-records"></a>Общая информация о записях DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Добавление записи DNS для личного домена</h2>

Чтобы сопоставить личный домен с веб-приложением Azure, добавьте новую запись в таблицу DNS для этого домена с помощью средств, предоставляемых GoDaddy. Выполните следующие действия, чтобы найти средства DNS для GoDaddy.com.

1. Войдите на GoDaddy.com с использованием своей учетной записи и выберите **Моя учетная запись** и **Управление доменами**. Выберите в раскрывающемся меню доменное имя, которое вы хотите использовать для своего веб-приложения Azure и откройте **Управление DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. На странице **Сведения о домене** прокрутите до пункта **Файл зоны DNS**. Этот раздел используется в целях добавления и изменения записей DNS для доменного имени. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Выберите **Добавить запись**, чтобы добавить существующую запись.

	Чтобы **отредактировать** существующую запись, выберите значок с карандашом и бумагой рядом с записью.

	> [AZURE.NOTE] Перед добавлением новых записей обратите внимание, что GoDaddy уже создала записи DNS для таких популярных поддоменов (**Узел** в редакторе), как **email**, **files**, **mail** и другие. Если имя, которое вы хотите использовать, уже существует, измените существующую запись вместо того, чтобы создавать новую.

4. При добавлении записи необходимо сначала выбрать тип записи.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Далее вы должны ввести **Хост** (личный домен или поддомен) и то, что будет значиться в поле **Указывает на**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* При добавлении **записи (узла) A** необходимо задать в поле **Узел** либо символ **@** (представляющий имя корневого домена, например **contoso.com**), * (подстановочный знак для сопоставления с несколькими поддоменами), либо поддомен, который вы хотите использовать (например, **www**.) Задайте в поле **Указывает на** IP-адрес вашего веб-приложения Azure.
	
		> [AZURE.NOTE] При добавлении записи A (узла) вам необходимо также добавить запись CNAME со следующей конфигурацией:
		> 
		> * Значение **узла** **awverify**, которое **Указывает на** значение **awverify.&lt;имя_вашего_веб-приложения&gt;.azurewebsites.net**.
		> 
		> Запись CNAME позволяет Azure проверить, что именно вы являетесь владельцем домена, описанного в записи A.

	* При добавлении **записи (псевдонима) CNAME** необходимо задать в поле **Узел** тот поддомен, который вы хотите использовать. Например, **www**. Задайте в поле **Указывает на** доменное имя **.azurewebsites.net** вашего веб-сайта Azure. Например, **contoso.azurwebsites.net**.


5. По завершении добавления или изменения записей нажмите кнопку **Готово** для сохранения изменений.

<h2><a name="enabledomain"></a>Включение доменного имени в веб-приложении</h2>

[AZURE.INCLUDE [режимы](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

<!--HONumber=49-->
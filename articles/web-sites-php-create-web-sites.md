<properties 
	pageTitle="Создание веб-сайта PHP в службе Azure Websites" 
	description="Узнайте, как создать веб-сайт на PHP в веб-сайтах Azure" 
	documentationCenter="php" 
	services="" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Создание веб-сайта PHP в службе Azure Websites

В этой статье рассматривается создание веб-сайта на PHP в [веб-сайтах Azure][waws] с помощью [портала управления Azure], [программ командной строки Azure для Mac и Linux][xplat-tools] или [командлетов Azure PowerShell][powershell-cmdlets].

В целом создание веб-сайта на PHP ничем не отличается от создания *any* веб-сайта в веб-сайтах Azure. По умолчанию поддержка PHP включена для всех веб-сайтов. Информацию о настройке PHP (или подключении собственной настраиваемой среды выполнения PHP) см. в разделе [Настройка PHP для веб-сайтов Azure][configure-php]..

Каждый из описываемых ниже параметров показывает, как создать веб-сайт в общей среде размещения без дополнительных затрат, но с некоторыми ограничениями на использование ЦП и пропускной способности. Дополнительную информацию см. в разделе [Цены на веб-сайты Microsoft Azure][websites-pricing]. Дополнительную информацию об обновлении и масштабировании веб-сайта см. в разделе [Как масштабировать веб-сайты][scale-websites].

> [AZURE.NOTE]
> Если вы хотите ознакомиться с веб-сайтами Azure до создания учетной записи, перейдите по адресу <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, где вы можете быстро создать краткосрочный начальный сайт ASP.NET на веб-сайтах Azure бесплатно. Никаких кредитных карт и обязательств.

##Оглавление
* [Создание веб-сайта с помощью портала управления Azure](#portal)
* [Создание веб-сайта с помощью программ командной строки Azure для Mac и Linux](#XplatTools)
* [Создание веб-сайта с помощью командлетов Azure PowerShell](#PowerShell)

<h2><a name="portal"></a>Создание веб-сайта на PHP с помощью портала управления Azure</h2>

При создании веб-сайта на портале управления Azure есть три варианта: **Быстрое создание**, **Создание с базой данных** и **Из коллекции**. Приведенные ниже инструкции относятся к параметру **Быстрое создание**. Информацию о других двух параметрах см. в разделе [Создание веб-сайта PHP-MySQL Azure и его развертывание с помощью Git][website-mysql-git] и [Создание веб-сайта WordPress из коллекции в Azure][wordpress-gallery].

Чтобы создать веб-сайт на PHP с помощью портала управления Azure, выполните следующие действия.

1. Выполните вход на [портал управления Azure].
1. Щелкните **Создать** в нижней части страницы, а затем выберите **Среда выполнения приложений**, **Веб-сайт**, и **Быстрое создание**. Укажите **URL-адрес** для своего веб-сайта и выберите **Регион** для него. Нажмите кнопку **Создать веб-сайт**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Создание веб-сайта на PHP с использованием программ командной строки Azure для Mac и Linux</h2>

Чтобы создать веб-сайт на PHP с использованием средств командной строки Azure для Mac и Linux, выполните следующие действия.

1. Установите средства командной строки Azure, следуя данным инструкциям: [Установка программ командной строки Azure для Mac и Linux](/ru-ru/develop/php/how-to-guides/command-line-tools/#Download).

1. Загрузите и импортируйте свой файл настроек публикации, следуя инструкциям данного раздела: [Закачка и импорт параметров публикации](/ru-ru/develop/php/how-to-guides/command-line-tools/#Account).

1. Выполните следующую команду из командной строки:

		azure site create MySiteName

URL-адрес для созданного веб-сайта будет `http://MySiteName.azurewebsites.net`.  
 
Обратите внимание, что команду `azure site create` можно выполнять с любыми из следующих параметров.

* `--location [location name]`. Этот параметр позволяет указать расположение центра обработки данных, в котором создается веб-сайт (например, "Запад США"). Если этот параметр опущен, появится запрос выбрать расположение.
* `--hostname [custom host name]`. Этот параметр позволяет задать пользовательское имя узла для веб-сайта.
* `--git`. Этот параметр позволяет использовать git для публикации на веб-сайте путем создания репозиториев git в каталоге локального приложения и центре данных веб-сайта. Обратите внимание, что если локальная папка уже является репозиторием git, команда добавит новый удаленный к существующему репозиторию, с указанием на репозиторий в центре обработки данных веб-сайта.

Информацию о дополнительных параметрах см. в разделе [Создание веб-сайта Azure и управление им](/ru-ru/develop/php/how-to-guides/command-line-tools/#WebSites)..

<h2><a name="PowerShell"></a>Создание веб-сайта на PHP с помощью командлетов Azure PowerShell</h2>

Чтобы создать веб-сайт на PHP с помощью командлетов Azure PowerShell, выполните следующие действия.

1. Установите командлеты Azure PowerShell, следуя инструкции: [Приступая к работе с Azure PowerShell](/ru-ru/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Загрузите и импортируйте свой файл настроек публикации, следуя инструкциям данного раздела: [Практическое руководство: Импорт параметров публикации](/ru-ru/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Откройте окно командной строки PowerShell и выполните следующую команду:

		New-AzureWebSite MySiteName

URL-адрес для созданного веб-сайта будет  `http://MySiteName.azurewebsites.net`.  
 
Обратите внимание, что команду `New-AzureWebSite` можно выполнять с любыми из следующих параметров.

* `-Location [location name]`. Этот параметр позволяет указать расположение центра обработки данных, в котором создается веб-сайт (например, "Запад США"). Если этот параметр опущен, появится запрос выбрать расположение.
* `-Hostname [custom host name]`. Этот параметр позволяет задать пользовательское имя узла для веб-сайта.
* `-Git`. Этот параметр позволяет использовать git для публикации на веб-сайте путем создания репозиториев git в каталоге локального приложения и центре данных веб-сайта. Обратите внимание, что если локальная папка уже является репозиторием git, команда добавит новый удаленный к существующему репозиторию, с указанием на репозиторий в центре обработки данных веб-сайта.

Дополнительные сведения о других параметрах см. в разделе [Руководство. Создание веб-сайта Azure и управление им](/ru-ru/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Дальнейшие действия</h2>

После создания веб-сайта на PHP в Azure Websites его можно настраивать, отслеживать, использовать для развертывания и масштабировать, а также управлять им. Дополнительные сведения см. по следующим ссылкам:

* [Настройка веб-сайтов](/ru-ru/manage/services/web-sites/how-to-configure-websites/)
* [Настройка PHP на веб-сайтах Azure][configure-php]
* [Управление веб-сайтами](/ru-ru/manage/services/web-sites/how-to-manage-websites/)
* [Мониторинг веб-сайтов](/ru-ru/manage/services/web-sites/how-to-monitor-websites/)
* [Как масштабировать веб-сайты](/ru-ru/manage/services/web-sites/how-to-scale-websites/)
* [Публикация с Git](/ru-ru/develop/php/common-tasks/publishing-with-git/)

Полная подборка учебных курсов доступна на странице [Центр разработчиков PHP. Учебные курсы](/ru-ru/develop/php/tutorials/).

[waws]: /ru-ru/manage/services/web-sites/
[Портал управления Azure]: http://manage.windowsazure.com/
[xplat-tools]: /ru-ru/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /ru-ru/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /ru-ru/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /ru-ru/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /ru-ru/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://azure.microsoft.com/pricing/details/#header-1
[scale-websites]: /ru-ru/manage/services/web-sites/how-to-scale-websites/


<!--HONumber=42-->

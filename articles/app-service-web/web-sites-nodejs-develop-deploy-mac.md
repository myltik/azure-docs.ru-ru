<properties
	pageTitle="Создание веб-приложения Node.js в службе приложений Azure"
	description="Узнайте, как выполнить сборку и развертывание веб-приложения Node.js в Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Создание и развертывание веб-приложения Node.js в службе приложений Azure

В этом учебнике показано, как создать приложение [Узел][nodejs.org] и развернуть его в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) с помощью [Git]. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, на которой может работать Node.

Снимок экрана завершенного приложения приведен ниже:

![Браузер, в котором отображается сообщение Hello World.][helloworld-completed]

##Создание веб-приложения и включение публикации через Git

Выполните следующие действия для создания веб-приложения и включения публикации через Git.

> [AZURE.NOTE]
> Для работы с этим учебником требуется учетная запись Microsoft Azure. Если у вас нет учетной записи, можно [активировать преимущества для подписчиков MSDN](/ru-ru/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) или [подписаться на бесплатную пробную версию](/ru-ru/pricing/free-trial/?WT.mc_id=A261C142F).

1. Войдите на [портал Azure](https://portal.azure.com).

2. Щелкните значок **+ Создать** в верхнем левом углу портала.

3. Щелкните **Веб + мобильное**, а затем **Веб-приложение**.

    ![][portal-quick-create]

4. Введите значение в поле **URL-адрес**.

5. Выберите план службы приложений или создайте новый. При создании нового плана выберите ценовую категорию, расположение и другие параметры.

    ![][portal-quick-create2]

6. Щелкните **Создать**.

7. После изменения состояния параметра **Под управлением** портал будет автоматически открыт в колонке вашего веб-приложения. Можно также получить доступ к этой колонке, нажав **Обзор**.

	![][go-to-dashboard]

8. Щелкните **Развертывание**. Возможно, нужно будет выполнить прокрутку к этой части колонки.

	![][deployment-part]

9. Щелкните **Выбрать источник**, затем **Локальный репозиторий Git**. Нажмите кнопку **ОК**.

	![][setup-git-publishing]


10. Щелкните раздел **учетных данных развертывания** (обведен красным ниже). Укажите имя пользователя и пароль. Щелкните **Сохранить**. Если вы уже включили публикации для веб-приложения, не нужно выполнять этот шаг.

	![][deployment-credentials]


11. Чтобы опубликовать содержимое, необходимо отправить его в удаленный репозиторий Git. Чтобы найти URL-адрес репозитория, щелкните **Все параметры** и нажмите кнопку **Свойства**. URL-адрес указан в разделе "URL-адрес GIT".

	![][git-url]

##Построение и тестирование приложения на локальном ресурсе

В этом разделе будет создан файл **server.js** файл, содержащий пример hello world из [nodejs.org] В отличие от исходного примера в этот код был добавлен process.env.PORT в качестве прослушиваемого порта во время работы в веб-приложении Azure.

1. С помощью текстового редактора создайте новый файл с именем **server.js** в каталоге **helloworld**. Если каталог **helloworld** не существует, создайте его.

2. Добавьте следующий код в качестве содержимого файла **server.js** и сохраните файл:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Откройте командную строку и введите следующую команду, чтобы запустить веб-приложение локально:

        node server.js

4. Откройте веб-браузер и перейдите по адресу http://localhost:1337. Появится веб-страница Hello World, как показано на снимке экрана ниже:

    ![Браузер, в котором отображается сообщение Hello World.][helloworld-localhost]

##Публикация приложения

1. В командной строке измените каталоги на **helloworld** и введите следующие команды для инициализации локального репозитория Git.

		git init

	> [AZURE.NOTE] **Команда Git недоступна?**
	[Git](http://git-scm.com/%20target="_blank) является распределенной системой управления версиями, которую можно использовать для развертывания веб-сайта Azure. Указания по установке для своей платформы см. на [странице скачивания Git](http://git-scm.com/download%20target="_blank").

2. Используйте следующие команды для добавления файлов в репозиторий:

		git add .
		git commit -m "initial commit"

3. Добавьте удаленный Git для отправки обновлений в ранее созданное веб-приложение Azure с помощью команды:

		git remote add azure [URL for remote repository]


4. Отправьте обновления в Azure с помощью следующей команды:

		git push azure master

	Появится запрос на ввод ранее заданного пароля. Должен быть получен результат, аналогичный приведенному ниже:

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. Чтобы просмотреть приложение, нажмите кнопку **Обзор** в разделе **Веб-приложения** на портале управления.

##Публикация изменений в приложении

1. Откройте файл **server.js** в текстовом редакторе и измените Hello World\n на Hello Azure\n. Сохраните файл.
2. В командной строке измените каталоги на **helloworld** и выполните следующие команды:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Появится запрос на ввод ранее заданного пароля.

3. Перейдите в свое приложение, нажав кнопку **Обзор**, и обратите внимание, что обновления были применены.

	![Веб-страница с отображением строки Hello Azure][helloworld-completed]

4. Можно вернуться к предыдущему развертыванию, выбрав его в разделе **Развертывания**.

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

##Дальнейшие действия

Хотя приведенные в этой статье действия позволяют использовать портал Azure для создания веб-приложения, можно также использовать [интерфейс командной строки Azure](../xplat-cli.md), который позволяет выполнить те же самые операции.

Node.js предоставляет обширную экосистему модулей, которые можно использовать в своих приложениях. Чтобы узнать, как веб-приложения Azure работают с модулями, см. статью [Использование модулей Node.js с приложениями Azure](../nodejs-use-node-modules-azure-apps.md).

Для получения дополнительных сведений о версиях Node.js, входящих в состав Windows Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure](../nodejs-specify-node-version-azure-apps.md).

При возникновении проблем с приложением после его развертывания в Windows Azure сведения о диагностике проблемы можно найти в разделе [Отладка приложения Node.js на веб-сайтах Windows Azure](web-sites-nodejs-debug.md).


##Дополнительные ресурсы

* [Azure PowerShell](../install-configure-powershell.md)
* [Интерфейс командной строки Azure](../xplat-cli.md)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=August15_HO6-->
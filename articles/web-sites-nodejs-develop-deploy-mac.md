<properties urlDisplayName="Website" pageTitle="Создание веб-сайта Node.js на Mac - учебники Azure" metaKeywords="создание веб-сайта Node в Azure, развертывание веб-сайта Node в Azure, веб-сайт Node.js, веб-сайт Node" description="Узнайте, как выполнить сборку и развертывание веб-сайта Node.js в Azure. Пример кода написан на языке Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Выполнение сборки и развертывание веб-сайта Node.js в Azure

В этом учебнике показано, как создать приложение [Node] [nodejs.org] и развернуть его на веб-сайте Azure с помощью [Git]. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, на которой может работать Node.

Если вы предпочитаете смотреть видео, то в следующем клипе приведены те же действия, что и в учебнике:
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
Снимок экрана завершенного приложения приведен ниже:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Создание веб-сайта Azure и включение публикации Git

Выполните следующие действия, чтобы создать веб-сайт Azure, а затем включить публикацию Git для этого веб-сайта.

> [WACOM.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатное пробное использование Azure</a>.
> 
> Если вы хотите начать работу с веб-сайтами Azure до регистрации учетной записи, перейдите на страницу <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a>, где можно сразу же создать кратковременный начальный сайт ASP.NET на веб-сайтах Azure бесплатно. Никаких кредитных карт и обязательств.

1. Выполните вход на [портал управления Azure].

2. Щелкните значок **+СОЗДАТЬ** в нижнем левом углу портала.

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Щелкните **ВЕБ-САЙТ**, затем **БЫСТРОЕ СОЗДАНИЕ**. Введите значение для параметра **URL-адрес** и в раскрывающемся списке **РЕГИОН** выберите центр обработки данных для веб-сайта. Щелкните флажок в нижней части диалогового окна.

    ![The Quick Create dialog][portal-quick-create]

4. После изменения состояния веб-сайта на **Выполняется** щелкните имя веб-сайта, чтобы получить доступ к странице **Панель мониторинга**.

	![Open web site dashboard][go-to-dashboard]

6. В правом нижнем углу страницы быстрого запуска выберите пункт **Настройка развертывания из системы управления версиями**.

	![Set up Git publishing][setup-git-publishing]

6. В ответ на вопрос "Где исходный код?" выберите значение **Локальный репозиторий Git** и щелкните стрелку.

	![where is your source code][where-is-code]

7. Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Если вы уже включили публикации для веб-сайта Azure, имя пользователя и пароль запрашиваться не будут. Вместо этого будет создан репозиторий Git с использованием ранее указанных имени пользователя и пароля. Запишите имя пользователя и пароль, так как они будут использоваться для публикации через Git всех создаваемых вами веб-сайтов Azure.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. После подготовки репозитория Git появятся инструкции по командам Git, которые следует использовать для настройки локального репозитория и передачи файлов в Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Построение и тестирование приложения на локальном ресурсе

В этом разделе будет создан файл **server.js** файл, содержащий пример hello world из [nodejs.org] В отличие от исходного примера, в этот код был добавлен process.env.PORT в качестве прослушиваемого порта при выполнении на веб-сайте Azure.

1. С помощью текстового редактора создайте новый файл с именем **server.js** в каталоге **helloworld**. Если каталог **helloworld** не существует, создайте его.
2. Добавьте следующий код в качестве содержимого файла **server.js** и сохраните файл:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Откройте командную строку и используйте следующую команду, чтобы запустить веб-страницу локально:

        node server.js

4. Откройте веб-браузер и перейдите по адресу http://localhost:1337. Появится веб-страница Hello World, как показано на снимке экрана ниже:

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Публикация приложения

1. В командной строке измените каталоги на **helloworld** и введите следующие команды для инициализации локального репозитория Git. 

		git init

	<div class="dev-callout"><strong>Команда Git недоступна?</strong>
	<p><a href="http://git-scm.com/" target="_blank">Git</a> является распределенной системой управления версиями, которую можно использовать для развертывания веб-сайта Azure. Инструкции по установке для вашей платформы см. <a href="http://git-scm.com/download" target="_blank">на странице загрузки Git</a>.</p>
	</div>

2. Используйте следующие команды для добавления файлов в репозиторий:

		git add .
		git commit -m "initial commit"

3. Добавьте удаленный Git для отправки обновлений на ранее созданный веб-сайт Azure с помощью приведенной ниже команды:

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Отправьте обновления в Azure с помощью следующей команды:

		git push azure master

	Появится запрос на ввод ранее созданного пароля, после чего отобразятся следующие выходные данные:

		Password for 'testsite.scm.azurewebsites.net':
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
    
	При переходе на вкладку развертываний веб-сайта Azure на портале управления появится история развертываний с вашим первым развертыванием:

	![Git deployment status on the portal][git-deployments-first] 

5. Просмотрите свой сайт с помощью кнопки **Обзор** на странице веб-сайта Azure в рамках портала управления.

##Публикация изменений в приложении

1. Откройте файл **server.js** в текстовом редакторе и измените Hello World\n на Hello Azure\n. Сохраните файл.
2. В командной строке измените каталоги на **helloworld** и выполните следующие команды:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Появится запрос на ввод ранее заданного пароля. При переходе на вкладку развертываний веб-сайта Azure на портале управления появится обновленный журнал развертываний:
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Перейдите к сайту с помощью кнопки **Обзор** и обратите внимание на то, что обновления были применены.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Вы можете вернуться к предыдущему развертыванию, выбрав нужное развертывание на вкладке "Развертывания" веб-сайта Azure на портале управления и нажав кнопку **Повторное развертывание**.

##Дальнейшие действия

Хотя приведенные в этой статье действия позволяют использовать портал Azure для создания веб-сайта, можно также использовать [программы командной строки Azure для Mac и Linux], которые позволяют выполнить те же самые действия.

Node.js предоставляет обширную экосистему модулей, которые можно использовать в своих приложениях. Чтобы узнать, как работают веб-сайты Azure с модулями, см. раздел [Использование модулей Node.js в приложениях Azure](/ru-ru/documentation/articles/nodejs-use-node-modules-azure-apps/).

Для получения дополнительных сведений о версиях Node.js, входящих в состав Microsoft Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure](/ru-ru/documentation/articles/nodejs-specify-node-version-azure-apps/).

При возникновении проблем с приложением после его развертывания в Azure сведения о диагностике проблемы можно найти в статье [Настройка приложения Node.js для веб-сайтов Microsoft Azure](/ru-ru/documentation/articles/web-sites-nodejs-debug/) .


##Дополнительные ресурсы

* [Azure PowerShell]
* [Средства командной строки Azure для Mac и Linux]

[Azure PowerShell]: /ru-ru/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Портал управления Azure]: http://manage.windowsazure.com
[Средства командной строки Azure для Mac и Linux]: /ru-ru/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png

<!--HONumber=35.2-->

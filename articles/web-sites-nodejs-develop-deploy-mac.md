<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Веб-сайт" pageTitle="Учебники "Создание веб-сайта Node.js на Mac — Azure"" metaKeywords="Создание веб-сайта Node в Azure, развертывание веб-сайта Node в Azure, веб-сайт Node.js веб-сайт Node" description="Сведения о создании и развертывании веб-сайта Node.js в Azure. Пример кода написан на Java." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Построение и развертывание веб-сайта Node.js в Azure" authors=""  solutions="" writer="" manager="" editor=""  />





#Создание и развертывание веб-сайта Node.js в Azure

В этом учебнике показано, как создать приложение [Node] и развернуть его на веб-сайте Azure с помощью [Git]. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, на которой может работать Node.

Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.
 
Снимок экрана завершенного приложения приведен ниже:

![Браузер, в котором отображается сообщение Hello World.][helloworld-completed]

##Создание веб-сайта Azure и включение публикации Git

Выполните следующие действия, чтобы создать веб-сайт Azure, а затем включить публикацию Git для этого веб-сайта.

<div class="dev-callout"><strong>Примечание.</strong>
<p>Чтобы завершить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.</p>
</div>

1. Выполните вход на [Портал управления Azure].

2. Щелкните значок **+СОЗДАТЬ** в нижнем левом углу портала.

    ![Портал Azure с выделенной ссылкой +СОЗДАТЬ.][portal-new-website]

3. Щелкните **ВЕБ-САЙТ**, затем **БЫСТРО СОЗДАТЬ**. Введите значение для параметра **URL-адрес** и выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните флажок в нижней части диалогового окна.

    ![Диалоговое окно "Быстрое создание"][portal-quick-create]

4. После изменения состояния веб-сайта на **Выполняется** щелкните имя веб-сайта, чтобы получить доступ к **панели мониторинга**.

	![Откройте панель управления веб-сайта][go-to-dashboard]

6. В правом нижнем углу страницы быстрого запуска выберите пункт **Настройка развертывания из системы управления версиями**.

	![Настроить публикацию Git][setup-git-publishing]

6. В ответ на вопрос "Где исходный код?" выберите значение **Локальный репозиторий Git** и щелкните стрелку.

	![где находится исходный код][where-is-code]

7. Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Если вы уже включили публикации для веб-сайта Azure, имя пользователя и пароль запрашиваться не будут. Вместо этого будет создан репозиторий Git с использованием ранее указанных имени пользователя и пароля. Запишите имя пользователя и пароль, так как они будут использоваться для публикации через Git всех создаваемых вами веб-сайтов Azure.

	![Диалоговое окно с запросом на ввод имени пользователя и пароля.][portal-git-username-password]

8. После подготовки репозитория Git появятся инструкции по командам Git, которые следует использовать для настройки локального репозитория и передачи файлов в Azure.

	![Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.][git-instructions]

##Построение и тестирование приложения на локальном ресурсе

В этом разделе будет создан файл **server.js** файл, содержащий пример hello world из [nodejs.org] В отличие от исходного примера в этот код был добавлен process.env.PORT в качестве прослушиваемого порта при выполнении на веб-сайте Azure.

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
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

4. Откройте веб-браузер и перейдите по адресу http://localhost:1337. Появится веб-страница Hello World, как показано на снимке экрана ниже:

    ![Браузер, в котором отображается сообщение Hello World.][helloworld-localhost]

##Публикация приложения

1. В командной строке измените каталоги на **helloworld** и введите следующие команды для инициализации локального репозитория Git. 

		git init

	<div class="dev-callout"><strong>Команда Git недоступна?</strong>
	<p><a href="http://git-scm.com/" target="_blank">Git</a> является распределенной системой управления версиями, которую можно использовать для развертывания веб-сайта Azure. Инструкции по установке для вашей платформы см. на <a href="http://git-scm.com/download" target="_blank">странице загрузки Git</a>.</p>
	</div>

2. Используйте следующие команды для добавления файлов в репозиторий:

		git add .
		git commit -m "initial commit"

3. Добавьте удаленный Git для передачи обновлений на ранее созданный веб-сайт Azure с помощью приведенной ниже команды:

		git remote add azure [URL for remote repository]

    ![Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.][git-instructions]
 
4. Передайте обновления в Azure с помощью следующей команды:

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
    
	При переходе на вкладку развертываний вашего веб-сайта Azure на портале управления появится история развертываний с вашим первым развертыванием:

	![Состояние развертывания Git на портале][git-deployments-first] 

5. Обзор веб-сайта с помощью кнопки **Обзор** на странице веб-сайта Azure в рамках портала управления.

##Публикация изменений в приложении

1. Откройте файл **server.js** в текстовом редакторе и измените Hello World\n на Hello Azure\n. Сохраните файл.
2. В командной строке измените каталоги на **helloworld** и выполните следующие команды:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Появится запрос на ввод ранее заданного пароля. При переходе на вкладку развертываний вашего веб-сайта Azure на портале управления появится обновленная история развертываний:
	
	![Обновленное состояние развертывания Git на портале][git-deployments-second]

3. Перейдите к сайту с помощью кнопки **Обзор** и обратите внимание на то, что обновления были применены.

	![Веб-страница с отображением строки Hello Azure][helloworld-completed]

4. Можно вернуться к предыдущему развертыванию, выбрав нужное развертывание на вкладке "Развертывания" веб-сайта Azure на портале обслуживания и нажав кнопку **Повторное развертывание**.

##Следующие шаги

Хотя приведенные в этой статье действия позволяют использовать портал Azure для создания веб-сайта, можно также использовать [средства командой строки Azure для Mac и Linux], которые позволяют выполнить те же самые действия.

Node.js предоставляет обширную экосистему модулей, которые можно использовать в своих приложениях. Чтобы узнать, как работают веб-сайты Azure с модулями, см. раздел [Использование модулей Node.js в приложениях Azure](http://www.windowsazure.com/ru-ru/develop/nodejs/common-tasks/working-with-node-modules/).

Для получения дополнительных сведений о версиях Node.js, входящих в состав Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure](http://www.windowsazure.com/ru-ru/develop/nodejs/common-tasks/specifying-a-node-version/).

При возникновении проблем с приложением после его развертывания в Azure сведения о диагностики проблемы можно найти в разделе [Отладка приложения Node.js на веб-сайтах Azure](http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/Debug-Website/).


##Дополнительные ресурсы

* [Azure PowerShell]
* [Средства командной строки Azure для Mac и Linux]

[Azure PowerShell]: /ru-ru/develop/nodejs/how-to-guides/powershell-cmdlets/



[Портал управления Azure]: http://manage.windowsazure.com
[Средства командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/

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


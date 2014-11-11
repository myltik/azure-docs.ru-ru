<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Website" pageTitle="Create a Node.js website on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js website in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Выполнение сборки и развертывание веб-сайта Node.js в Azure

В этом учебнике показано, как создать приложение [Node][Node] и развернуть его на веб-сайте Azure с помощью [Git][Git]. Инструкции, приведенные в этом учебнике, применимы к любой операционной системе, на которой может работать Node.

Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.

Снимок экрана завершенного приложения приведен ниже:

![Браузер, в котором отображается сообщение Hello World.][Браузер, в котором отображается сообщение Hello World.]

## Создание веб-сайта Azure и включение публикации Git

Выполните следующие действия, чтобы создать веб-сайт Azure, а затем включить публикацию Git для этого веб-сайта.

<div class="dev-callout"><strong>Примечание.</strong>
<p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.</p>
</div>

1.  Войдите на [портал управления Azure][портал управления Azure].

2.  Щелкните значок **+СОЗДАТЬ** в нижнем левом углу портала.

    ![Портал Azure с выделенной ссылкой «+СОЗДАТЬ».][Портал Azure с выделенной ссылкой «+СОЗДАТЬ».]

3.  Щелкните **ВЕБ-САЙТ**, затем **БЫСТРОЕ СОЗДАНИЕ**. Введите значение для параметра **URL-адрес** и в раскрывающемся списке **РЕГИОН** выберите центр обработки данных для веб-сайта. Щелкните флажок в нижней части диалогового окна.

    ![Диалоговое окно "Быстрое создание"][Диалоговое окно "Быстрое создание"]

4.  После изменения состояния веб-сайта на **Выполняется** щелкните имя веб-сайта, чтобы получить доступ к странице **Панель мониторинга**.

    ![Откройте панель мониторинга веб-сайта][Откройте панель мониторинга веб-сайта]

5.  В правом нижнем углу страницы быстрого запуска выберите пункт **Настройка развертывания из системы управления версиями**.

    ![Настроить публикацию Git][Настроить публикацию Git]

6.  В ответ на вопрос "Где исходный код?" выберите значение **Локальный репозиторий Git** и щелкните стрелку.

    ![где находится исходный код][где находится исходный код]

7.  Чтобы включить публикацию Git, необходимо предоставить имя пользователя и пароль. Если вы уже включили публикации для веб-сайта Azure, имя пользователя и пароль запрашиваться не будут. Вместо этого будет создан репозиторий Git с использованием ранее указанных имени пользователя и пароля. Запишите имя пользователя и пароль, так как они будут использоваться для публикации через Git всех создаваемых вами веб-сайтов Azure.

    ![Диалоговое окно с запросом на ввод имени пользователя и пароля.][Диалоговое окно с запросом на ввод имени пользователя и пароля.]

8.  После подготовки репозитория Git вы увидите инструкции по командам Git, которые следует использовать для настройки локального репозитория и передачи файлов в Azure.

    ![Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.][Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.]

## Выполнение сборки и тестирование приложения на локальном компьютере

В этом разделе будет создан файл **server.js** файл, содержащий пример hello world из [nodejs.org][Node] В отличие от исходного примера, в этот код был добавлен process.env.PORT в качестве прослушиваемого порта при выполнении на веб-сайте Azure.

1.  С помощью текстового редактора создайте новый файл с именем **server.js** в каталоге **helloworld**. Если каталог **helloworld** не существует, создайте его.
2.  Добавьте следующий код в качестве содержимого файла **server.js** и сохраните файл:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3.  Откройте командную строку и используйте следующую команду, чтобы запустить веб-страницу локально:

        node server.js

4.  Откройте браузер и перейдите по адресу http://localhost:1337. Появится веб-страница Hello World, как показано на снимке экрана ниже:

    ![Браузер, в котором отображается сообщение Hello World.][1]

## Публикация приложения

1.  В командной строке измените каталоги на **helloworld** и введите следующие команды для инициализации локального репозитория Git.

        git init

    <div class="dev-callout"><strong>Команда Git недоступна?</strong>
<p><a href="http://git-scm.com/" target="_blank">Git</a> является распределенной системой управления версиями, которую можно использовать для развертывания веб-сайта Azure. Указания по установке для своей платформы см. на <a href="http://git-scm.com/download" target="_blank">странице скачивания Git</a>.</p>
</div>

2.  Используйте следующие команды для добавления файлов в репозиторий:

        git add .
        git commit -m "initial commit"

3.  Добавьте удаленный Git для отправки обновлений на ранее созданный веб-сайт Azure с помощью приведенной ниже команды:

        git remote add azure [URL for remote repository]

    ![Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.][Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.]

4.  Отправьте обновления в Azure с помощью следующей команды:

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

    ![Состояние развертывания Git на портале][Состояние развертывания Git на портале]

5.  Просмотрите свой сайт с помощью кнопки **Обзор** на странице веб-сайта Azure в рамках портала управления.

## Публикация изменений в приложении

1.  Откройте файл **server.js** в текстовом редакторе и измените Hello World\\n на Hello Azure\\n. Сохраните файл.
2.  В командной строке измените каталоги на **helloworld** и выполните следующие команды:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Появится запрос на ввод ранее заданного пароля. При переходе на вкладку развертываний веб-сайта Azure на портале управления появится обновленный журнал развертываний:

    ![Обновленное состояние развертывания Git на портале][Обновленное состояние развертывания Git на портале]

3.  Перейдите к сайту с помощью кнопки **Обзор** и обратите внимание на то, что обновления были применены.

    ![Веб-страница с отображением строки Hello Azure][Браузер, в котором отображается сообщение Hello World.]

4.  Вы можете вернуться к предыдущему развертыванию, выбрав нужное развертывание на вкладке «Развертывания» веб-сайта Azure на портале управления и нажав кнопку **Повторное развертывание**.

## Дальнейшие действия

Хотя приведенные в этой статье действия позволяют использовать портал Azure для создания веб-сайта, можно также использовать [программы командой строки Azure для Mac и Linux][программы командой строки Azure для Mac и Linux], которые позволяют выполнить те же самые действия.

Node.js предоставляет обширную экосистему модулей, которые можно использовать в своих приложениях. Чтобы узнать, как работают веб-сайты Azure с модулями, см. раздел [Использование модулей Node.js в приложениях Azure][Использование модулей Node.js в приложениях Azure].

Для получения дополнительных сведений о версиях Node.js, входящих в состав Windows Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure][Указание версии Node.js в приложении Azure].

При возникновении проблем с приложением после его развертывания в Windows Azure сведения о диагностике проблемы можно найти в разделе [Отладка приложения Node.js на веб-сайтах Windows Azure][Отладка приложения Node.js на веб-сайтах Windows Azure].

## Дополнительные ресурсы

-   [Azure PowerShell][Azure PowerShell]
-   [Программы командной строки Azure для Mac и Linux][программы командой строки Azure для Mac и Linux]

  [Node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Браузер, в котором отображается сообщение Hello World.]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
  [портал управления Azure]: http://manage.windowsazure.com
  [Диалоговое окно "Быстрое создание"]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
  [Откройте панель мониторинга веб-сайта]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
  [Настроить публикацию Git]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
  [где находится исходный код]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
  [Диалоговое окно с запросом на ввод имени пользователя и пароля.]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
  [Инструкции по развертыванию Git, возвращенные после создания репозитория для веб-сайта.]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png
  [1]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
  [Состояние развертывания Git на портале]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
  [Обновленное состояние развертывания Git на портале]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png
  [программы командой строки Azure для Mac и Linux]: /ru-ru/documentation/articles/xplat-cli/
  [Использование модулей Node.js в приложениях Azure]: /ru-ru/documentation/articles/nodejs-use-node-modules-azure-apps/
  [Указание версии Node.js в приложении Azure]: /ru-ru/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Отладка приложения Node.js на веб-сайтах Windows Azure]: /ru-ru/documentation/articles/web-sites-nodejs-debug/
  [Azure PowerShell]: /ru-ru/documentation/articles/install-configure-powershell/

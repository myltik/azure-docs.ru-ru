<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure

Node.js включает в себя минимальный набор функциональных возможностей в базовой среде выполнения.
Разработчики часто используют сторонние модули для обеспечения дополнительных функциональных возможностей
при разработке приложения Node.js. В этом учебнике будет создано новое приложение
с использованием модуля [Express][Express], который предоставляет платформу MVC для создания веб-приложений Node.js.

Снимок экрана завершенного приложения приведен ниже:

![Веб-браузер, отображающий приветствие модуля Express в Azure][Веб-браузер, отображающий приветствие модуля Express в Azure]

## Создание проекта облачной службы

Чтобы создать новый проект облачной службы с именем expressapp, выполните следующие действия.

1.  В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

    ![Значок Azure PowerShell][Значок Azure PowerShell]

    [WACOM.INCLUDE [установка-средств-разработки](../includes/install-dev-tools.md)]

2.  Перейдите в каталог **c:\\node**, а затем введите указанные ниже команды для создания нового решения с именем **expressapp** и рабочей роли с именем **WebRole1**.

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

    > [WACOM.ПРИМЕЧАНИЕ] По умолчанию командлет **Add-AzureNodeWebRole** использует более раннюю версию Node.js. Инструкция **Set-AzureServiceProjectRole**, приведенная выше, указывает Azure использовать Node версии 0.10.21.

## Установка модуля Express

1.  Установите генератор Express, выполнив следующую команду:

        PS C:\node\expressapp> npm install express-generator -g

    Результат команды npm должен соответствовать показанному ниже результату.

    ![Windows PowerShell с выходными данными команды npm install express.][Windows PowerShell с выходными данными команды npm install express.]

2.  Перейдите в каталог **WebRole1** и используйте команду express для создания нового приложения.

        PS C:\node\expressapp\WebRole1> express

    Вам будет предложено перезаписать более раннюю версию приложения. Для продолжения введите **y** или **yes**. Модуль Express сформирует файл app.js и структуру папок для создания вашего приложения.

    ![Результат команды express][Результат команды express]

3.  Чтобы установить дополнительные зависимости, определенные в файле package.json file,
    введите следующую команду:

        PS C:\node\expressapp\WebRole1> npm install

    ![Результат команды npm install][Результат команды npm install]

4.  Скопируйте файл **bin/www** в **server.js** с помощью следующей команды: Таким образом, облачная служба сможет найти точку входа в это приложение.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    После выполнения команды файл **server.js** должен содержаться в каталоге WebRole1.

5.  В файле **server.js** удалите одну из точек (символ ".") из строки, приведенной ниже.

        var app = require('../app');

    После сделанных изменений строка должна выглядеть следующим образом.

        var app = require('./app');

    Это необходимо сделать, так как мы перенесли файл (ранее **bin/www**) в тот же каталог, в котором должен находиться файл приложения. После внесения изменений сохраните файл **server.js**.

6.  Чтобы запустить приложение в эмуляторе Windows Azure,
    используйте следующую команду:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Веб-страница, содержащая приветствие модуля express.][Веб-страница, содержащая приветствие модуля express.]

## Изменение представления

Теперь измените представление для отображения приветственного сообщения
"Welcome to Express in Azure".

1.  Чтобы открыть файл index.jade, введите следующую команду:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Содержимое файла index.jade.][Содержимое файла index.jade.]

    Jade — это обработчик представлений по умолчанию, используемый приложениями Express. Дополнительные сведения об обработчике представлений Jade
    см. на веб-сайте [][]<http://jade-lang.com></a>.

2.  Измените последнюю строку текста, добавив слова **in Azure**.

    ![Последняя строка в файле index.jade содержит сообщение: p Welcome to \#{title} in Azure][Последняя строка в файле index.jade содержит сообщение: p Welcome to \#{title} in Azure]

3.  Сохраните файл и выйдите из Блокнота.

4.  Обновите браузер и увидите изменения.

    ![Окно браузера со страницей приветствия Express в Azure][Окно браузера со страницей приветствия Express в Azure]

После тестирования приложения остановите эмулятор с помощью командлета **Stop-AzureEmulator**.

## Публикация приложения в Azure

В окне Azure PowerShell используйте командлет **Publish-AzureServiceProject**, чтобы развернуть приложение в облачной службе.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

После завершения операции развертывания откроется браузер с отображением веб-страницы.

![В веб-браузере отображается страница Express. URL-адрес указывает, что страница теперь размещается в Azure.][Веб-браузер, отображающий приветствие модуля Express в Azure]

  [Express]: http://expressjs.com/
  [Веб-браузер, отображающий приветствие модуля Express в Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node36.png
  [Значок Azure PowerShell]: ./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png
  [Windows PowerShell с выходными данными команды npm install express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png
  [Результат команды express]: ./media/cloud-services-nodejs-develop-deploy-express-app/node23.png
  [Результат команды npm install]: ./media/cloud-services-nodejs-develop-deploy-express-app/node26.png
  [Веб-страница, содержащая приветствие модуля express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/node28.png
  [Содержимое файла index.jade.]: ./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png
  []: http://jade-lang.com
  [Окно браузера со страницей приветствия Express в Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node32.png

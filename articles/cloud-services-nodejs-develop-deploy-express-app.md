<properties 
	pageTitle="Веб-приложения с помощью Express (Node.js) - учебник Azure" 
	description="Настоящий учебник основывается на учебнике по облачным службам, и здесь демонстрируется использование модуля Express." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure

Node.js включает минимальный набор функциональных возможностей в базовой среде выполнения.
Разработчики часто используют сторонние модули, чтобы обеспечить дополнительные
функциональные возможности при разработке приложения Node.js. В этом учебнике рассматриваются следующие темы:
будет создано новое приложение с использованием модуля [Express][], который предоставляет платформу MVC для создания веб-приложений Node.js.

Снимок экрана завершенного приложения приведен ниже:

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Создание проекта облачной службы

Чтобы создать новый проект облачной службы с именем 'expressapp', выполните следующие действия.

1. В меню **Пуск** или на **начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Перейдите в каталог **c:\\node**, а затем введите следующие команды, чтобы создать новое решение с именем **expressapp** и веб-роль с именем **WebRole1**:

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [AZURE.NOTE] По умолчанию **Add-AzureNodeWebRole** использует предыдущую версию Node.js. Показанная выше инструкция **Set-AzureServiceProjectRole** предписывает Azure использовать Node v0.10.21. 

## Установка модуля Express

1. Установите генератор Express, выполнив следующую команду:

		PS C:\node\expressapp> npm install express-generator -g

	Результат команды npm должен соответствовать показанному ниже результату. 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Перейдите в каталог **WebRole1** и используйте команду express для создания нового приложения.

        PS C:\node\expressapp\WebRole1> express

	Вам будет предложено перезаписать более раннюю версию приложения. Для продолжения введите **y** или **yes**. Модуль Express сформирует файл app.js и структуру папок для создания вашего приложения.

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Чтобы установить дополнительные зависимости, определенные в файле package.json,
    введите следующую команду:

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  С помощью следующей команды скопируйте файл **bin/www** в **server.js**. Таким образом, облачная служба сможет найти точку входа в это приложение.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	После выполнения этой команды файл **server.js** должен находиться в каталоге WebRole1.

7.  Измените **server.js**, удалив один из символов "." из следующей строки.

		var app = require('../app');

	После сделанных изменений строка должна выглядеть следующим образом.

		var app = require('./app');

	Это изменение является обязательным, поскольку мы переместили файл (прежнее название **bin/www**,) в тот же каталог, что и обязательный файл приложения. После внесения изменений сохраните файл **server.js**.

8.  Выполните следующую команду, чтобы запустить приложение в
    эмуляторе Microsoft Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Изменение представления

Теперь измените представление для отображения сообщения "Вас приветствует Express в
Azure".

1.  Чтобы открыть файл index.jade, введите следующую команду:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade - это обработчик представлений по умолчанию, используемый приложениями Express. Дополнительную
    информацию об обработчике представлений Jade см. на сайте [http://jade-lang.com][].

2.  Измените последнюю строку текста, добавив **в Azure**.

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Сохраните файл и выйдите из Блокнота.

4.  Обновите браузер и увидите изменения.

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

После тестирования приложения с помощью командлета **Stop-AzureEmulator** остановите эмулятор.

## Публикация приложения в Azure

В окне Azure PowerShell с помощью командлета **Publish-AzureServiceProject** разверните приложение в облачной службе

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

После завершения операции развертывания откроется браузер с отображением веб-страницы.

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Веб-приложение Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


<!--HONumber=45--> 

.<properties 
	pageTitle="Веб-приложение с Express (Node.js) | Microsoft Azure" 
	description="Этот учебник основывается на учебнике по облачным службам и демонстрирует использование модуля Express." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>






# Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure

Node.js включает минимальный набор функциональных возможностей в базовой среде выполнения. Разработчики часто используют сторонние модули для получения дополнительной функциональности при разработке приложения Node.js. В этом учебнике будет создано новое приложение с помощью модуля [Express][], который предоставляет платформу MVC для создания веб-приложений Node.js.

Снимок экрана завершенного приложения приведен ниже:

![Веб-браузер, отображающий приветствие модуля Express в Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Создание проекта облачной службы

Чтобы создать новый проект облачной службы с именем expressapp, выполните следующие действия.

1. В **меню Пуск** или на **начальном экране** найдите **Windows PowerShell**. Щелкните правой кнопкой мыши **Windows PowerShell** и выберите **Запуск от имени администратора**.

	![Значок Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Перейдите в каталог **c:\\node**, а затем введите указанные ниже команды для создания нового решения с именем **expressapp** и рабочей роли с именем **WebRole1**.

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

	> [AZURE.NOTE] По умолчанию **Add-AzureNodeWebRole** использует предыдущую версию Node.js. Показанная выше инструкция **Set-AzureServiceProjectRole** предписывает Azure использовать Node v0.10.21. Обратите внимание, что параметры зависят от регистра. Можно проверить, выбрана ли правильная версия Node.js, выбрав свойство **engines** в **WebRole1\\package.json**.

##Установка модуля Express

1. Установите генератор Express, выполнив следующую команду:

		PS C:\node\expressapp> npm install express-generator -g

	Результат команды npm должен соответствовать показанному ниже результату.

	![Windows PowerShell с выходными данными команды npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Перейдите в каталог **WebRole1** и используйте команду express для создания нового приложения.

        PS C:\node\expressapp\WebRole1> express

	Вам будет предложено перезаписать более раннюю версию приложения. Для продолжения введите **y** или **yes**. Модуль Express сформирует файл app.js и структуру папок для создания вашего приложения.

	![Результат команды express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Чтобы установить дополнительные зависимости, определенные в файле package.json, введите следующую команду:

        PS C:\node\expressapp\WebRole1> npm install

	![Результат команды npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Скопируйте файл **bin/www** в **server.js** с помощью следующей команды: Таким образом, облачная служба сможет найти точку входа в это приложение.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	После выполнения команды файл **server.js** должен содержаться в каталоге WebRole1.

7.  В файле **server.js** удалите одну из точек (символ ".") из строки, приведенной ниже.

		var app = require('../app');

	После сделанных изменений строка должна выглядеть следующим образом.

		var app = require('./app');

	Это необходимо сделать, так как мы перенесли файл (ранее **bin/www**) в тот же каталог, в котором должен находиться файл приложения. После внесения изменений сохраните файл **server.js**.

8.  Выполнив следующую команду, запустите приложение в эмуляторе Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![Веб-страница, содержащая приветствие модуля express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Изменение представления

Теперь измените представление для отображения сообщения «Вас приветствует Express в Azure».

1.  Чтобы открыть файл index.jade, введите следующую команду:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Содержимое файла index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade — это обработчик представлений по умолчанию, используемый приложениями Express. Дополнительные сведения об обработчике представлений Jade см. на сайте [http://jade-lang.com][].

2.  Измените последнюю строку текста, добавив слова **in Azure**.

	![Файл index.jade, последняя строка содержит текст: p Welcome to #{title} in Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Сохраните файл и выйдите из Блокнота.

4.  Обновите браузер и увидите изменения.

	![Окно браузера со страницей приветствия Express в Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

После тестирования приложения остановите эмулятор с помощью командлета **Stop-AzureEmulator**.

##Публикация приложения в Azure

В окне Azure PowerShell используйте командлет **Publish-AzureServiceProject**, чтобы развернуть приложение в облачной службе.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

После завершения операции развертывания откроется браузер с отображением веб-страницы.

![В веб-браузере отображается страница Express. URL-адрес указывает, что страница теперь размещается в Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Дальнейшие действия

Дополнительную информацию см. в [Центре разработчика Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 

<!---HONumber=AcomDC_0817_2016-->
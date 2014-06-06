<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Веб-приложение с модулем Express" pageTitle="Веб-приложение с модулем Express (Node.js) — учебник по Azure" metaKeywords="учебник Azure Node.js hello world, Azure Node.js hello world, приступая к работе с Azure Node.js, учебник Azure Node.js, учебник Azure Node.js Express" description="Учебник, который опирается на учебник по облачной службе и демонстрирует, как использовать модуль Express." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure

Node.js включает минимальный набор функциональных возможностей в базовой среде выполнения.
Разработчики часто используют сторонние модули для обеспечения дополнительных функциональных возможностей при разработке приложения Node.js. В этом учебнике будет создано новое приложение с использованием модуля [Express][], который предоставляет платформу MVC для создания веб-приложений Node.js.

Снимок экрана завершенного приложения приведен ниже:

![Веб-браузер, отображающий приветствие модуля Express в Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Создание проекта облачной службы

Чтобы создать новый проект облачной службы с именем 'expressapp', выполните следующие действия:

1. В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Значок Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Перейдите в каталог **c:\\node**, а затем введите указанные ниже команды для создания нового решения с именем **expressapp** и веб-роли с именем **WebRole1**.

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node> Add-AzureNodeWebRole

##Установка модуля Express

1. Установите модуль Express, выполнив следующую команду:

		PS C:\node\expressapp> npm install express -g

	Результат команды npm должен соответствовать показанному ниже результату. 

	![Windows PowerShell с выходными данными команды npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Перейдите в каталог **WebRole1** и используйте команду express для создания нового приложения.

        PS C:\node\expressapp\WebRole1> express

	Вам будет предложено перезаписать более раннюю версию приложения. Для продолжения введите **y** или **yes**. Модуль Express сформирует файл app.js и структуру папок для создания вашего приложения.

	![Результат команды express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  Удалите файл **server.js**, а затем измените имя созданного файла **app.js** на **server.js**.

        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

5.  Чтобы установить дополнительные зависимости, определенные
    в файле package.json, введите следующую команду:

        PS C:\node\expressapp\WebRole1> npm install

	![Результат команды npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

8.  Чтобы запустить приложение в эмуляторе Azure,
    используйте следующую команду:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![Веб-страница, содержащая приветствие модуля express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Изменение представления

Теперь измените представление для отображения приветственного сообщения "Welcome to Express in Azure".

1.  Введите следующую команду, чтобы открыть файл index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Содержимое файла index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade — это обработчик представлений по умолчанию, используемый приложениями Express. Дополнительные
    сведения об обработчике представлений Jade см. на веб-сайте [http://jade-lang.com][].

2.  Измените последнюю строку текста, добавив слова **in Azure**.

	![Файл index.jade, последняя строка содержит текст: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Сохраните файл и выйдите из Блокнота.

4.  Обновите браузер и увидите изменения.

	![Окно браузера со страницей приветствия Express в Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

После тестирования приложения остановите эмулятор с помощью командлета **Stop-AzureEmulator**.

##Публикация приложения в Azure

В окне Azure PowerShell используйте командлет **Publish-AzureServiceProject**, чтобы развернуть приложение в облачной службе

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

После завершения операции развертывания откроется браузер с отображением веб-страницы.

![В веб-браузере отображается страница Express. URL-адрес указывает, что страница теперь размещается в Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Веб-приложение Node.js]: http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com



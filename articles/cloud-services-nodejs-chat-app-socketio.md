<properties 
	pageTitle="Приложения Node.js с помощью Socket.io - Учебник Azure" 
	description="В настоящем учебнике рассматривается использование Socket.io в приложениях Node.js, размещенных в Azure." 
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





# Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure

Socket.IO обеспечивает обмен данными в реальном времени между сервером node.js
и клиентами. В этом учебнике рассматривается размещение
приложения для разговора на основе Socket.IO в Azure. Дополнительные сведения
о Socket.IO см. на сайте <a href="http://socket.io/">http://socket.io/</a>.

Снимок экрана завершенного приложения приведен ниже:

![A browser window displaying the service hosted on Azure][completed-app]  

## Создание проекта облачной службы

Ниже описаны действия, в результате которых создается проект облачной службы, в которой будет размещаться приложение Socket.IO.

1. В меню **Пуск** или на **начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. Перейдите в каталог **c:\\node** и введите следующие команды, чтобы создать новое решение с именем **chatapp** и рабочую роль с именем **WorkerRole1**:

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Вы увидите следующий ответ:

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Скачивание примера разговора

Для этого проекта будет использоваться пример чата из [Репозитория GitHub
Socket.IO]. Чтобы скачать пример, сделайте следующее.
и добавьте его в проект, созданный ранее.

1.  Создайте локальную копию репозитория с помощью кнопки **Клонировать**. Скачать проект также можно при помощи кнопки **ZIP**.

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  Пройдитесь по структуре каталогов локального репозитория до каталога **examples\\chat**
    . Скопируйте содержимое этого каталога в
    **C:\\node\\chatapp\\WorkerRole1**.

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    Выделенные элементы на показанном выше снимке экрана - это файлы, скопированные из каталога **examples\\chat**

4.  В каталоге **C:\\node\\chatapp\\WorkerRole1** удалите файл **server.js**, а затем переименуйте файл **app.js** в **server.js**. Файл по умолчанию **server.js**, созданный ранее командлетом **Add-AzureNodeWorkerRole**, будет удален и заменен файлом приложения из примера разговора.

### Изменение Server.js и установка модулей

Перед началом тестирования приложения в эмуляторе Azure необходимо
внести некоторые незначительные изменения. Выполните следующие действия для
файл server.js:

1.  Откройте файл server.js в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Module dependencies** в начале файла server.js и замените строку, содержащую **sio = require('..//..//lib//socket.io')**, строкой **sio = require('socket.io')**, как показано ниже:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Чтобы убедиться, что приложение прослушивает необходимый порт, откройте
    файл server.js в Блокноте или в ином предпочитаемом редакторе и измените
    следующие строки, заменив **3000** на **process.env.port**, как показано ниже:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Cохранив изменения в server.js, выполните следующие действия для
установить необходимые модули, а затем протестируйте приложение в
эмуляторе Azure:

1.  С помощью **Azure PowerShell** перейдите в каталог **C:\\node\\chatapp\\WorkerRole1** и выполните следующую команду, чтобы установить модули, необходимые для этого приложения:

        PS C:\node\chatapp\WorkerRole1> npm install

    Будут установлены модули, указанные в файле package.json. После
    завершения команды будет выведен результат,
    подобный этому:

    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  Поскольку этот пример изначально был частью Socket.IO в репозитории GitHub
    и напрямую ссылался на библиотеку Socket.IO по
    относительному пути, Socket.IO не указывалась в файле package.json,
    поэтому необходимо его установить, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Тестирование и развертывание

1.  Запустите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  В открывшемся окне браузера введите псевдоним, а затем нажмите клавишу ВВОД.
    Это все, что вам потребуется для отправки сообщений в качестве конкретного псевдонима. Чтобы проверить
    функциональные возможности нескольких пользователей, откройте дополнительные окна браузера, используя
    тот же URL-адрес и разные псевдонимы.

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  После тестирования приложения остановите эмулятор, выполнив
    следующую команду:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Чтобы развернуть приложение в Azure, воспользуйтесь
    командлет **Publish-AzureServiceProject**. Например:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] Обязательно используйте уникальное имя, иначе произойдет сбой в процессе публикации. После завершения развертывания откроется браузер с переходом к развернутой службе.
	> 
	> Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо скачать и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Создание и развертывание приложения Node.js в облачной службе Azure](https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/)

    ![A browser window displaying the service hosted on Azure][completed-app]

	> [AZURE.NOTE] Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо скачать и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Создание и развертывание приложения Node.js в облачной службе Azure](https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/)

Теперь ваше приложение работает в Azure и вы можете ретранслировать сообщения
чата между разными клиентами, используя Socket.IO.

> [AZURE.NOTE] Чтобы упростить процесс, в этом примере мы ограничили разговор пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры см. в примерах использования очередей и разделов Service Bus в [репозитории пакета SDK для Azure для Node.js на GitHub.](https://github.com/WindowsAzure/azure-sdk-for-node).

## Дальнейшие действия

В этом учебнике было рассмотрено создание базового приложения для разговора, размещаемого в облачной службе Azure. Чтобы узнать, как разместить это приложение на веб-сайте Azure, ознакомьтесь с учебником [Создание приложения для разговора Node.js с Socket.IO на веб-сайте Azure][chatwebsite].

  [chatwebsite]: /ru-ru/develop/nodejs/tutorials/website-using-socketio/

  [Соглашение об уровне обслуживания Azure]: http://azure.microsoft.com/support/sla/
  [Репозиторий GitHub для пакета SDK для Azure для Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Пакет SDK для Azure для Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/
  [Веб-приложение Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
  [Репозитория GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Рекомендации по Azure]: #windowsazureconsiderations
  [Размещение примера чата в рабочей роли]: #hostingthechatexampleinawebrole
  [Сводка и дальнейшие действия]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [Пример чата]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Вывод команды Publish-AzureService]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=45--> 

<properties urlDisplayName="App Using Socket.IO" pageTitle="Приложение Node.js с Socket.IO - учебник по Azure" metaKeywords="учебник по Azure Node.js socket.io, Azure Node.js socket.io, учебник по Azure Node.js" description="В настоящем учебнике рассматривается использование Socket.io в приложениях Node.js, размещенных в Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />





# Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером и клиентами node.js. В этом учебнике рассматривается размещение приложения для разговора на основе Socket.IO в Azure. Дополнительные сведения о Socket.IO см. на сайте <a href="http://socket.io/">http://socket.io/</a>.

Снимок экрана завершенного приложения приведен ниже:
	
![A browser window displaying the service hosted on Azure][completed-app]  

## Создание проекта облачной службы

Ниже описаны действия, в результате которых создается проект облачной службы, в которой будет размещаться приложение Socket.IO.

1. В **меню "Пуск"** или на **начальном экране** выполните поиск **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. Измените каталоги на каталог **c:\\node**, а затем введите следующие команды, чтобы создать новое решение **chatapp** и рабочую роль **WorkerRole1**:

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Вы увидите следующий ответ:

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Скачивание примера разговора

Для этого проекта мы будем использовать пример разговора из [репозитория GitHub Socket.IO]. Выполните следующие действия, чтобы скачать пример и добавить его в ранее созданный проект.

1.  Создайте локальную копию репозитория с помощью кнопки **Клонировать**. Скачать проект также можно при помощи кнопки **ZIP**.

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  Перемещайтесь по структуре каталога локального репозитория, пока не доберетесь до каталога **examples\\chat**  . Скопируйте содержимое этого каталога в ранее созданный каталог **C:\\node\\chatapp\\WorkerRole1**.

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    Выделенные элементы на приведенном выше снимке экрана - это файлы, скопированные из каталога **examples\\chat**

4.  В каталоге **C:\\node\\chatapp\\WorkerRole1** удалите файл **server.js**, а затем переименуйте файл **app.js** в **server.js**. Файл по умолчанию **server.js**, созданный ранее командлетом **Add-AzureNodeWorkerRole**, будет удален и заменен файлом приложения из примера разговора.

### Изменение Server.js и установка модулей

Прежде чем тестировать приложение в эмуляторе Azure, необходимо внести небольшие изменения. Выполните следующие действия над файлом server.js:

1.  Откройте файл server.js в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Module dependencies** в начале файла server.js и измените строку, содержащую **sio = require('..//..//lib//socket.io')**, на **sio = require('socket.io')**, как показано ниже:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Чтобы приложение открывало правильный порт для передачи данных, откройте server.js в Блокноте или другом текстовом редакторе, а затем измените следующую строку, заменив **3000** на **process.env.port**, как показано ниже:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Сохранив изменения в server.js, выполните следующие действия, чтобы установить необходимые модули, а затем проверьте приложение в эмуляторе Azure:

1.  Используя **Azure PowerShell**, измените каталоги на каталог **C:\\node\\chatapp\\WorkerRole1** и воспользуйтесь следующей командой, чтобы установить необходимые для этого приложения модули:

        PS C:\node\chatapp\WorkerRole1> npm install

    Будут установлены модули, указанные в файле package.json. После выполнения команды должен появиться результат, аналогичный приведенному ниже: 
    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  Поскольку этот пример изначально был частью репозитория GitHub Socket.IO и через относительный путь ссылался непосредственно на библиотеку Socket.IO, модуль Socket.IO не указан в файле package.json, поэтому его необходимо установить, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Тестирование и развертывание

1.  Запустите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  В открывшемся окне браузера введите псевдоним, а затем нажмите клавишу ВВОД. Это все, что вам потребуется для отправки сообщений в качестве конкретного псевдонима. Чтобы проверить многопользовательскую функциональность, откройте дополнительные окна браузера, используя тот же URL-адрес, и введите разные псевдонимы.

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  После проверки приложения остановите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Чтобы развернуть приложение в Azure, воспользуйтесь командлетом **Publish-AzureServiceProject**. Например:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Обязательно используйте уникальное имя, иначе произойдет сбой в процессе публикации. После завершения развертывания откроется браузер с переходом к развернутой службе.</p>
	<p>Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо загрузить и импортировать профиль публикации для вашей подписки. См. раздел <b>Развертывание приложения в Azure</b> в статье <a href="https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/">Создание и развертывание приложения Node.js в облачной службе Azure</a></p>
	</div>

    ![A browser window displaying the service hosted on Azure][completed-app]

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо скачать и импортировать профиль публикации для вашей подписки. См. раздел <b>Развертывание приложения в Azure</b> в статье <a href="https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/">Создание и развертывание приложения Node.js в облачной службе Azure</a></p>
	</div>

Теперь ваше приложение работает в Azure и вы можете ретранслировать сообщения
чата между разными клиентами, используя Socket.IO.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Чтобы упростить процесс, в этом примере мы ограничили разговор пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры использования см. в разделах "Очереди служебной шины" и "Тома служебной шины" в <a href="https://github.com/WindowsAzure/azure-sdk-for-node">репозитории GitHub Azure SDK для Node.js</a>.</p>
</div>

##Дальнейшие действия

В этом учебнике было рассмотрено создание базового приложения для разговора, размещаемого в облачной службе Azure. Чтобы узнать, как разместить это приложение на веб-сайте Azure, ознакомьтесь с учебником [Создание приложения для разговора Node.js с Socket.IO на веб-сайте Azure][chatwebsite].

  [chatwebsite]: /ru-ru/develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/ru-ru/support/sla/
  [Репозиторий GitHub Azure SDK для Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK для Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/
  [Веб-приложение Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
  [Репозиторий GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Ограничения Azure]: #windowsazureconsiderations
  [Размещение примера чата в рабочей роли]: #hostingthechatexampleinawebrole
  [Сводка и дальнейшие действия]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [пример чата]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=35.2-->

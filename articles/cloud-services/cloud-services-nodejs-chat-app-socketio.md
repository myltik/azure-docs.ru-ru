<properties 
	pageTitle="Приложение node.js с использованием Socket.io | Microsoft Azure" 
	description="Узнайте, как использовать socket.io в приложении Node.js, размещенном в Azure." 
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
	ms.date="06/24/2016" 
	ms.author="robmcm"/>

# Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером и клиентами node.js. В этом учебнике рассматривается размещение приложения для разговора на основе Socket.IO в Azure. Дополнительные сведения о Socket.IO см. на сайте <http://socket.io/>.

Снимок экрана завершенного приложения приведен ниже:

![Окно браузера со службой, размещенной в Azure][completed-app]

## Предварительные требования

Убедитесь, что следующие продукты и версии установлены для успешного завершения примера, описанного в этой статье.

* Установите [Visual Studio 2013](https://www.visualstudio.com/ru-RU/downloads/download-visual-studio-vs.aspx).
* Установите [Node.js](https://nodejs.org/download/).
* Установите [Python версии 2.7.10](https://www.python.org/).

## Создание проекта облачной службы

Ниже описаны действия, в результате которых создается проект облачной службы, в которой будет размещаться приложение Socket.IO.

1. В **меню Пуск** или на **начальном экране** найдите **Windows PowerShell**. Щелкните правой кнопкой мыши **Windows PowerShell** и выберите **Запуск от имени администратора**.

	![Значок Azure PowerShell][powershell-menu]

2. Сначала создайте каталог **c:\\node**.
 
		PS C:\> md node

3. Перейдите в каталог **c:\\node**.
 
		PS C:\> cd node

4. Введите указанные ниже команды для создания нового решения с именем **chatapp** и рабочей роли с именем **WorkerRole1**.

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Вы увидите следующий ответ:

	![Выходные значения параметров new-azureservice и add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Загрузка примера разговора

Для этого проекта мы будем использовать пример разговора из [репозитория GitHub Socket.IO]. Выполните следующие действия, чтобы скачать пример и добавить его в ранее созданный проект.

1.  Создайте локальную копию репозитория с помощью кнопки **Клонировать**. Можно также загрузить проект при помощи кнопки **ZIP**.

    ![Окно браузера со страницей https://github.com/LearnBoost/socket.io/tree/master/examples/chat с подсвеченным значком загрузки ZIP.][chat-example-view]

3.  Перемещайтесь по структуре каталога локального репозитория, пока не доберетесь до каталога **examples\\\chat**. Скопируйте содержимое этого каталога в ранее созданный каталог **C:\\node\\chatapp\\WorkerRole1**.

    ![Обозреватель с извлеченным из архива содержимым каталога examples\\chat][chat-contents]

    На представленном выше снимке экрана выделены файлы, скопированные из каталога **examples\\chat**.

4.  Удалите из каталога **C:\\node\\chatapp\\WorkerRole1** файл **server.js** и переименуйте файл **app.js** на **server.js**. При этом файл по умолчанию **server.js**, созданный ранее с помощью командлета **Add-AzureNodeWorkerRole**, удаляется и заменяется файлом приложения из примера разговора.

### Изменение Server.js и установка модулей

Прежде чем тестировать приложение в эмуляторе Azure, необходимо внести небольшие изменения. Выполните следующие действия над файлом server.js:

1.  Откройте файл **server.js** в Visual Studio или в любом текстовом редакторе.

2.  Найдите раздел **Зависимости модуля** в начале файла server.js и измените строку **sio = require('..//..//lib//socket.io')** на **sio = require('socket.io')**, как показано ниже.

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Чтобы приложение прослушивало правильный порт, откройте файл server.js в Блокноте или другом текстовом редакторе, а затем измените следующую строку, заменив **3000** на **process.env.port**, как показано ниже:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Сохранив изменения в **server.js**, выполните следующие действия, чтобы установить необходимые модули, а затем проверьте приложение в эмуляторе Azure:

1.  Используя **Azure PowerShell**, перейдите в каталог **C:\\node\\chatapp\\WorkerRole1** и выполните следующую команду для установки необходимых приложению модулей:

        PS C:\node\chatapp\WorkerRole1> npm install

    Будет выполнена установка модулей, перечисленных в файле package.json. После выполнения команды должен появиться результат, аналогичный приведенному ниже:

    ![Результат команды npm install][The-output-of-the-npm-install-command]

4.  Поскольку этот пример изначально был частью репозитория GitHub Socket.IO и через относительный путь ссылался непосредственно на библиотеку Socket.IO, модуль Socket.IO не указан в файле package.json, поэтому его необходимо установить, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### Тестирование и развертывание

1.  Запустите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Откройте веб-браузер и перейдите по адресу **http://127.0.0.1**.

3.  В открывшемся окне браузера введите псевдоним, а затем нажмите клавишу ВВОД. Это все, что вам потребуется для отправки сообщений в качестве конкретного псевдонима. Чтобы проверить многопользовательскую функциональность, откройте дополнительные окна браузера, используя тот же URL-адрес, и введите разные псевдонимы.

    ![Два окна браузера с сообщениями разговора от пользователей User1 и User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  После проверки приложения остановите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Чтобы развернуть приложение в Azure, воспользуйтесь командлетом **Publish-AzureServiceProject**. Например:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] Обязательно используйте уникальное имя, иначе произойдет сбой в процессе публикации. После завершения развертывания откроется браузер с переходом к развернутой службе.
	> 
	> Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо загрузить и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Сборка и развертывание приложения Node.js в облачной службе Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/).

    ![Окно браузера со службой, размещенной в Azure][completed-app]

	> [AZURE.NOTE] Если появляется сообщение о том, что предоставленного названия подписки в импортированном профиле публикации нет, то перед развертыванием в Azurе необходимо загрузить и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Сборка и развертывание приложения Node.js в облачной службе Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/).

Теперь ваше приложение выполняется на платформе Azure и может передавать сообщения чата между различными клиентами с использованием Socket.IO.

> [AZURE.NOTE] Чтобы упростить процесс, в этом примере мы ограничили разговор пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры см. в примерах использования очередей и разделов Service Bus в [репозитории пакета Azure SDK для Node.js на GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).

##Дальнейшие действия

В этом учебнике было рассмотрено создание базового приложения для разговора, размещаемого в облачной службе Azure. Чтобы узнать, как разместить это приложение на веб-сайте Azure, см. раздел [Создание приложения для разговора Node.js с использованием Socket.IO на веб-сайте Azure][chatwebsite].

Дополнительную информацию см. также в [Центре разработчика Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [репозитория GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 

<!---HONumber=AcomDC_0629_2016-->
<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="Приложение с использованием Socket.IO" pageTitle="Приложение Node.js с использованием Socket.IO — учебник по Azure" metaKeywords="учебник Azure Node.js socket.io, socket.io Azure Node.js, учебник Azure Node.js" description="Учебник, в котором демонстрируется использование socket.io в приложении Node.js, размещаемом в Azure." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Создание приложения для разговора Node.js с использованием Socket.IO в облачной службе Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Создание приложения для разговора Node.js с использованием Socket.IO в облачной службе Azure

Socket.IO обеспечивает связь в режиме реального времени между вашим сервером Node.js и клиентами. Этот учебник поможет выполнить размещение приложения для разговора на основе socket.IO в Azure. Дополнительные сведения о Socket.IO см. на веб-сайте <a href="http://socket.io/">http://socket.io/</a>.

Снимок экрана завершенного приложения приведен ниже:

![Окно браузера со службой, размещенной в Azure][completed-app]  

## Создание проекта облачной службы

Ниже описаны действия, в результате которых создается проект облачной службы, в которой будет размещаться приложение Socket.IO.

1. В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Значок Azure PowerShell][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. Перейдите в каталог **c:\\node**, а затем введите указанные ниже команды для создания нового решения с именем **chatapp** и рабочей роли с именем **WorkerRole1**.

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Вы увидите следующий ответ:

	![Выходные значения параметров new-azureservice и add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Загрузка примера разговора

Для этого проекта будет использоваться пример разговора из [репозитория Socket.IO в GitHub]. Чтобы загрузить пример и добавить его в созданный ранее проект, выполните перечисленные ниже действия.

1.  Создайте локальную копию репозитория с помощью кнопки **Клонировать**. Можно также загрузить проект при помощи кнопки **ZIP**.

    ![Вид окна браузера https://github.com/LearnBoost/socket.io/tree/master/examples/chat с выделенным значком загрузки ZIP][chat-example-view]

3.  Перейдите по структуре каталогов локального репозитория до каталога **examples\\chat**. Скопируйте содержимое этого каталога в созданный ранее каталог
    **C:\\node\\chatapp\\WorkerRole1**.

    ![Обозреватель с извлеченным из архива содержимым каталога examples\\chat][chat-contents]

    На представленном выше снимке экрана выделены файлы, скопированные из каталога **examples\\chat**

4.  В каталоге **C:\\node\\chatapp\\WorkerRole1** удалите файл **server.js**, затем измените имя файла **app.js** на **server.js**. При этом файл по умолчанию **server.js**, созданный ранее с помощью командлета **Add-AzureNodeWorkerRole**, удаляется и заменяется файлом приложения из примера разговора.

### Изменение Server.js и установка модулей

Перед тестированием приложения в эмуляторе Azure необходимо внести некоторые незначительные изменения. Выполните следующие действия для файла server.js:

1.  Откройте файл server.js в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Module dependencies** в начале файла server.js и измените строку с текстом **sio = require('..//..//lib//socket.io')** на **sio = require('socket.io')**, как показано ниже.

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Чтобы приложение прослушивало нужный порт, откройте
    server.js в Блокноте или своем любимом редакторе, а затем измените
    следующую строку, заменив **3000** на **process.env.port**, как показано ниже:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

После сохранения изменений в server.js выполните описанные ниже действия, чтобы установить необходимые модули, а затем протестировать приложение в эмуляторе Azure.

1.  Используя **Azure PowerShell**, перейдите в каталог **C:\\node\\chatapp\\WorkerRole1** и выполните следующую команду для установки необходимых приложению модулей:

        PS C:\node\chatapp\WorkerRole1> npm install

    Будет выполнена установка модулей, перечисленных в файле package.json. Результат
    выполнения команды должен иметь примерно следующий
    вид:

    ![Результат команды npm install][The-output-of-the-npm-install-command]

4.  Поскольку этот пример изначально был частью репозитория Socket.IO
    в GitHub и на него прямо указывает ссылка библиотеки Socket.IO
    по относительному пути, Socket.IO не указана в файле package.json,
    поэтому необходимо установить ее, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Тестирование и развертывание

1.  Запустите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  В открывшемся окне браузера введите псевдоним, а затем нажмите клавишу ВВОД.
    Это все, что вам потребуется для отправки сообщений в качестве конкретного псевдонима. Для
    тестирования многопользовательских функций откройте дополнительные
    окна браузера, используя тот же URL-адрес, и введите другие псевдонимы.

    ![Два окна браузера с сообщениями разговора от пользователей User1 и User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  После тестирования приложения остановите эмулятор,
    выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Для развертывания приложения в Azure, используйте
    командлет **Publish-AzureServiceProject**. Например:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Обязательно используйте уникальное имя, иначе произойдет сбой в процессе публикации. После завершения развертывания откроется браузер с переходом к развернутой службе.</p>
	<p>Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, необходимо загрузить и импортировать профиль публикации для вашей подписки перед развертыванием в Azure. См. раздел <b>Развертывание приложения в Azure</b> в статье <a href="https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/">Сборка и развертывание приложения Node.js в облачной службе Azure</a></p>
	</div>

    ![Окно браузера со службой, размещенной в Azure][completed-app]

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, необходимо загрузить и импортировать профиль публикации для вашей подписки перед развертыванием в Azure. См. раздел <b>Развертывание приложения в Azure</b> в статье <a href="https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/">Сборка и развертывание приложения Node.js в облачной службе Azure</a></p>
	</div>

Теперь ваше приложение выполняется в Azure и может передавать сообщения разговора между различными клиентами с использованием Socket.IO.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Для простоты в этом примере разговор ограничен пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Образцы см. в примерах использования очередей и разделов Service Bus в <a href="https://github.com/WindowsAzure/azure-sdk-for-node">репозитории пакета Azure SDK для Node.js в GitHub</a>.</p>
</div>

##Дальнейшие действия

В этом учебнике было рассмотрено создание базового приложения для разговора, размещаемого в облачной службе Azure. Чтобы узнать, как разместить это приложение на веб-сайте Azure, см. раздел [Создание приложения для разговора Node.js с использованием Socket.IO на веб-сайте Azure][chatwebsite].

  [chatwebsite]: /ru-ru/develop/nodejs/tutorials/website-using-socketio/

  [Соглашение об уровне обслуживания (SLA) Azure]: http://www.windowsazure.com/ru-ru/support/sla/
  [Репозиторий пакета Azure SDK для Node.js в GitHub]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Пакет Azure SDK для Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/
  [Веб-приложение Node.js]: https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
  [репозитория Socket.IO в GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Рекомендации по Azure]: #windowsazureconsiderations
  [Размещение примера разговора в рабочей роли]: #hostingthechatexampleinawebrole
  [Сводка и дальнейшие действия]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [Пример разговора]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  


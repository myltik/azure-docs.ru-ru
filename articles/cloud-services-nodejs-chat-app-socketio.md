<properties urlDisplayName="App Using Socket.IO" pageTitle="Приложения Node.js с помощью Socket.io &mdash; Учебник Azure" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="В настоящем учебнике рассматривается использование Socket.io в приложениях Node.js, размещенных в Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure

Socket.IO обеспечивает связь в режиме реального времени
между вашим сервером Node.js и клиентами. Этот учебник поможет вам разместить приложение
для разговора на основе socket.IO в Azure. Дополнительные сведения о Socket.IO
см. по адресу <http://socket.io/>.

Снимок экрана завершенного приложения приведен ниже:

![Окно браузера со службой, размещенной в Azure][Окно браузера со службой, размещенной в Azure]

## Создание проекта облачной службы

Ниже описаны действия, в результате которых создается проект облачной службы, в которой будет размещаться приложение Socket.IO.

1.  В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

    ![Значок Azure PowerShell][Значок Azure PowerShell]

    [WACOM.INCLUDE [установка-средств-разработки](../includes/install-dev-tools.md)]

2.  Перейдите в каталог **c:\\node**, а затем введите указанные ниже команды для создания нового решения с именем **chatapp** и рабочей роли с именем **WorkerRole1**.

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Вы увидите следующий ответ:

    ![Выходные значения параметров new-azureservice и add-azurenodeworkerrolecmdlets][Выходные значения параметров new-azureservice и add-azurenodeworkerrolecmdlets]

## Загрузка примера разговора

Для этого проекта будет использоваться пример разговора из [репозитория Socket.IO
в GitHub][репозитория Socket.IO
в GitHub]. Чтобы загрузить пример и добавить его в созданный ранее проект,
выполните перечисленные ниже действия.

1.  Создайте локальную копию репозитория с помощью кнопки **Клонировать**. Можно также загрузить проект при помощи кнопки **ZIP**.

    ![Окно браузера со страницей https://github.com/LearnBoost/socket.io/tree/master/examples/chat с подсвеченным значком загрузки ZIP.][Окно браузера со страницей https://github.com/LearnBoost/socket.io/tree/master/examples/chat с подсвеченным значком загрузки ZIP.]

2.  Перейдите по структуре каталогов локального репозитория
    до каталога **examples\\chat**. Скопируйте содержимое каталога в ранее созданный каталог
    **C:\\node\\chatapp\\WorkerRole1**.

    ![Обозреватель с извлеченным из архива содержимым каталога examples\chat][Обозреватель с извлеченным из архива содержимым каталога examples\chat]

    На представленном выше снимке экрана выделены файлы, скопированные из каталога **examples\\chat**.

3.  Удалите из каталога **C:\\node\\chatapp\\WorkerRole1** файл **server.js** и переименуйте файл **app.js** на **server.js**. При этом файл по умолчанию **server.js**, созданный ранее с помощью командлета **Add-AzureNodeWorkerRole**, удаляется и заменяется файлом приложения из примера разговора.

### Изменение Server.js и установка модулей

Перед тестированием приложения в эмуляторе Windows Azure
необходимо внести некоторые незначительные изменения. Выполните следующие действия
для файла server.js.

1.  Откройте файл server.js в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Зависимости модуля** в начале файла server.js и измените строку **sio = require('..//..//lib//socket.io')** на **sio = require('socket.io')**, как показано ниже.

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Чтобы приложение прослушивало нужный порт,
    откройте server.js в Блокноте или своем любимом редакторе, а затем измените следующую строку,
    заменив **3000** на **process.env.port**, как показано ниже.

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

После сохранения изменений в server.js
выполните описанные ниже действия, чтобы установить необходимые модули,
а затем протестировать приложение в эмуляторе Azure.

1.  Используя **Azure PowerShell**, перейдите в каталог **C:\\node\\chatapp\\WorkerRole1** и выполните следующую команду для установки необходимых приложению модулей:

        PS C:\node\chatapp\WorkerRole1> npm install

    Будет выполнена установка модулей, перечисленных в файле package.json. Результат
    выполнения команды должен иметь примерно следующий
    вид:

    ![Результат команды npm install][Результат команды npm install]

2.  Так как этот пример изначально был частью репозитория Socket.IO
    в GitHub и на него прямо указывает ссылка библиотеки Socket.IO
    по относительному пути, Socket.IO не указана в файле
    package.json, поэтому необходимо установить ее, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Тестирование и развертывание

1.  Запустите эмулятор, выполнив следующую команду:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  В открывшемся окне браузера введите псевдоним и нажмите клавишу ВВОД.
    Это все, что вам потребуется для отправки сообщений от имени определенного псевдонима. Для тестирования многопользовательских функций
    откройте дополнительные окна браузера, используя тот же URL-адрес,
    и введите другие псевдонимы.

    ![Два окна браузера с сообщениями разговора от пользователей User1 и User2][Два окна браузера с сообщениями разговора от пользователей User1 и User2]

3.  Протестировав приложение, остановите эмулятор
    посредством следующей команды:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Для развертывания приложения в Azure
    используйте командлет **Publish-AzureServiceProject**. Например:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">

    **Примечание.**
    Обязательно используйте уникальное имя, иначе произойдет сбой в процессе публикации. После завершения развертывания откроется браузер с переходом к развернутой службе.

    Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо загрузить и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Сборка и развертывание приложения Node.js в облачной службе Azure][Сборка и развертывание приложения Node.js в облачной службе Azure].

    </div>

    ![Окно браузера со службой, размещенной в Azure][Окно браузера со службой, размещенной в Azure]

    <div class="dev-callout">

    **Примечание.**
    Если появляется сообщение о том, что предоставленное название подписки не существует в импортированном профиле публикации, то перед развертыванием в Azurе необходимо загрузить и импортировать профиль публикации для вашей подписки. См. раздел **Развертывание приложения в Azure** в статье [Сборка и развертывание приложения Node.js в облачной службе Azure][Сборка и развертывание приложения Node.js в облачной службе Azure].

    </div>

Теперь ваше приложение выполняется на платформе Azure и может передавать сообщения
разговора между различными клиентами с использованием Socket.IO.

<div class="dev-callout">

**Примечание.**
Чтобы упростить процесс, в этом примере мы ограничили разговор пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры см. в примерах использования очередей и разделов Service Bus в [репозитории пакета Azure SDK для Node.js на GitHub][репозитории пакета Azure SDK для Node.js на GitHub].

</div>

## Дальнейшие действия

В этом учебнике было рассмотрено создание базового приложения для разговора, размещаемого в облачной службе Azure. Чтобы узнать, как разместить это приложение на веб-сайте Azure, см. раздел [Создание приложения для разговора Node.js с использованием Socket.IO на веб-сайте Azure][Создание приложения для разговора Node.js с использованием Socket.IO на веб-сайте Azure].

  [Окно браузера со службой, размещенной в Azure]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Значок Azure PowerShell]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [Выходные значения параметров new-azureservice и add-azurenodeworkerrolecmdlets]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [Окно браузера со страницей https://github.com/LearnBoost/socket.io/tree/master/examples/chat с подсвеченным значком загрузки ZIP.]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [Результат команды npm install]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Два окна браузера с сообщениями разговора от пользователей User1 и User2]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Сборка и развертывание приложения Node.js в облачной службе Azure]: https://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
  [репозитории пакета Azure SDK для Node.js на GitHub]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Создание приложения для разговора Node.js с использованием Socket.IO на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/website-using-socketio/

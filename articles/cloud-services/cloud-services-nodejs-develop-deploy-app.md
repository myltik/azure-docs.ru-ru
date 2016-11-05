---
title: Руководство по началу работы с Node.js | Microsoft Docs
description: Сведения о создании простого веб-приложения Node.js и его развертывании в облачной службе Azure.
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: wpickett
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 08/11/2016
ms.author: robmcm

---
# Построение и развертывание приложения Node.js в облачной службе Azure
> [!div class="op_single_selector"]
> * [Node.js](cloud-services-nodejs-develop-deploy-app.md)
> * [.NET](cloud-services-dotnet-get-started.md)
> 
> 

В этом учебнике показано, как создать простое приложение Node.js, запускаемое в облачной службе Azure. Облачные службы являются основой для построения масштабируемых облачных приложений в Azure. Они обеспечивают разделение, независимое управление и масштабирование компонентов внешнего и внутреннего интерфейса приложения. Облачные службы предоставляют надежную выделенную виртуальную машину для надежного размещения каждой роли.

Дополнительную информацию об облачных службах и сравнении их с веб-сайтами Azure и виртуальными машинами см. раздел [Сравнение веб-сайтов Azure, облачных служб и виртуальных машин].

> [!TIP]
> Требуется собрать простой веб-сайт? Если в вашем сценарии задействован только простой интерфейс веб-сайта, мы рекомендуем [использовать упрощенное веб-приложение]. По мере роста веб-приложения и изменения требований можно легко выполнить обновление к облачной службе.
> 
> 

Руководствуясь этим учебником, вы соберете простое веб-приложение, размещенное в веб-роли. Эмулятор вычислений будет использоваться для тестирования приложения локально, а затем будет развернут с помощью средств командной строки PowerShell.

Приложение — это простое приложение hello world:

![В окне браузера отображается веб-страница "Hello World"][A web browser displaying the Hello World web page]

## Предварительные требования
> [!NOTE]
> В этом учебнике используется Azure PowerShell, для которого требуется операционная система Windows.
> 
> 

* Установите и настройте [Azure PowerShell].
* Скачайте и установите [пакет SDK для Azure для .NET 2.7]. В параметрах установки выберите:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## Создание проекта облачной службы Azure
Выполните следующие задачи для создания нового проекта облачной службы Azure с основными функциями формирования Node.js:

1. Запустите **Windows PowerShell** от имени администратора. В меню **Пуск** или на **начальном экране** найдите приложение **Windows PowerShell**.
2. [Подключите PowerShell] к своей подписке.
3. Введите следующий командлет PowerShell, чтобы создать проект:
   
        New-AzureServiceProject helloworld
   
    ![Результат выполнения команды New-AzureService helloworld][The result of the New-AzureService helloworld command]
   
    Командлет **New-AzureServiceProject** формирует базовую структуру для публикации приложения Node.js в облачной службе. Он содержит файлы конфигурации, необходимые для публикации в Azure. Командлет также изменяет рабочий каталог на каталог для службы.
   
    Командлет создает следующие файлы:
   
   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** и **ServiceDefinition.csdef** являются специальными файлами Azure, необходимыми для публикации вашего приложения. Дополнительные сведения см. в разделе [Обзор создания размещенной службы для Azure].
   * **deploymentSettings.json** хранит локальные параметры, используемые командлетами развертывания Azure PowerShell.
4. Введите следующую команду, чтобы добавить новую веб-роль:
   
       Add-AzureNodeWebRole
   
   ![Вывод команды Add-AzureNodeWebRole.][The output of the Add-AzureNodeWebRole command]
   
   Командлет **Add-AzureNodeWebRole** создает базовое приложение Node.js. Он также изменяет файлы **.csfg** и **.csdef**, чтобы добавить записи конфигурации для новой роли.
   
   > [!NOTE]
   > Если имя роли не указано, используется имя по умолчанию. Можно ввести имя в качестве первого параметра командлета: `Add-AzureNodeWebRole MyRole`
   > 
   > 

Приложение Node.js определяется в файле **server.js**, который находится в каталоге веб-роли (по умолчанию это **WebRole1**). Ниже приведен код:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Данный пример кода является практически аналогичным образцу Hello World на веб-сайте [nodejs.org], за исключением того, что он использует номер порта, назначенный средой облака.

## Развертывание приложения в Azure
    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### Загрузка настроек публикации Azure
Для развертывания приложения в Azure сначала нужно скачать настройки публикации для вашей подписки Azure.

1. Выполните следующий командлет Azure PowerShell:
   
       Get-AzurePublishSettingsFile
   
   Для перехода на страницу загрузки настроек публикации будет использован браузер. Может отобразиться предложение войти в систему с помощью учетной записи Майкрософт. В таком случае подписка Azure будет связана с этой учетной записью.
   
   Сохраните загруженный профиль в расположении файла, к которому есть доступ.
2. Выполните следующий командлет, чтобы импортировать профиль публикации, который вы загрузили:
   
       Import-AzurePublishSettingsFile [path to file]

    > [AZURE.NOTE] После импорта настроек публикации удалите загруженный файл PUBLISHSETTINGS, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи.

### Публикация приложения
Для публикации выполните следующие команды.

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **-ServiceName** указывает имя для развертывания. Это должно быть уникальное имя, в противном случае произойдет сбой в процессе публикации. Команда **Get-Date** добавляет к строке дату и время, чтобы сделать имя уникальным.
* **-Location** указывает центр обработки данных, в котором будет размещаться приложение. Чтобы просмотреть список доступных центров обработки данных, используйте командлет **Get-AzureLocation**.
* **-Launch** открывает окно браузера и переходит к размещенной службе после завершения развертывания.

После успешной публикации появится следующий ответ:

![Вывод команды Publish-AzureService][The output of the Publish-AzureService command]

> [!NOTE]
> При первой публикации развертывание приложения может занять несколько минут, после этого оно становится доступным.
> 
> 

После завершения развертывания откроется окно браузера и можно будет перейти в облачную службу.

![В окне браузера отображается страница "hello world"; URL-адрес указывает, что страница размещается в Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Теперь приложение работает в Azure.

Командлет **Publish-AzureServiceProject** выполняет следующие шаги:

1. Создает пакет развертывания. Пакет содержит все файлы в папке приложения.
2. Создайте новую **учетную запись хранения**, если она не существует. Учетная запись хранения Azure используется для хранения пакета приложения во время развертывания. После развертывания можно безопасно удалить учетную запись хранения.
3. Создайте новую **облачную службу**, если она не существует. При развертывании приложения в Azure **облачная служба** служит контейнером для размещения приложения. Дополнительные сведения см. в разделе [Обзор создания размещенной службы для Azure].
4. Публикует пакет развертывания в Azure.

## Остановка и удаление приложения
После развертывания приложения необходимо отключить его, чтобы избежать лишних затрат. Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

1. В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:
   
       Stop-AzureService
   
   Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.
   
   ![Состояние команды Stop-AzureService][The status of the Stop-AzureService command]
2. Чтобы удалить службу, вызовите следующий командлет:
   
       Remove-AzureService
   
   При появлении запроса введите **Y**, чтобы удалить службу.
   
   Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.
   
   ![Состояние команды Remove-AzureService][The status of the Remove-AzureService command]
   
   > [!NOTE]
   > При удалении службы учетная запись хранения, созданная при первоначальной публикации службы, не удаляется. Оплата за использование хранилища будет насчитываться. Если хранилище не используется другими объектами, его можно удалить.
   > 
   > 

## Дальнейшие действия
Дополнительную информацию см. в [Центре разработчика Node.js].

<!-- URL List -->

[Сравнение веб-сайтов Azure, облачных служб и виртуальных машин]: ../app-service-web/choose-web-site-cloud-service-vm.md
[использовать упрощенное веб-приложение]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md">
[Azure Powershell]: ../powershell-install-configure.md
[пакет SDK для Azure для .NET 2.7]: http://www.microsoft.com/ru-RU/download/details.aspx?id=48178
[Подключите PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Обзор создания размещенной службы для Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Центре разработчика Node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png

<!---HONumber=AcomDC_1005_2016-->
<properties 
	pageTitle="Руководство по началу работы с Node.js - Учебник по Azure" 
	description="Сведения о создании простого веб-приложения Node.js и его развертывании в облачной службе Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Построение и развертывание приложения Node.js в облачной службе Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

В этом учебнике показано, как создать простое приложение Node.js, запускаемое в облачной службе Azure. Облачные службы являются основой для построения масштабируемых облачных приложений в Azure. Они обеспечивают разделение, независимое управление и масштабирование компонентов внешнего и внутреннего интерфейса приложения.  Облачные службы предоставляют надежную выделенную виртуальную машину для надежного размещения каждой роли.

Дополнительную информацию об облачных службах и сравнение их с веб-сайтами Azure и виртуальными машинами см. в разделе [Сравнение веб-сайтов Azure, облачных служб и виртуальных машин](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Требуется собрать простой веб-сайт? Если в вашем сценарии задействован только простой интерфейс веб-сайта, следует рассмотреть возможность <a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">использования упрощенного веб-сайта Azure.</a> По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе.


Руководствуясь этим учебником, вы соберете простое веб-приложение, размещенное в веб-роли. Эмулятор вычислений будет использоваться для тестирования приложения локально, а затем будет развернут с помощью средств командной строки PowerShell.

Приложение - это простое приложение hello world:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. URL-адрес указывает, что страница размещается в Azure."
</p>

## Предварительные требования

> [AZURE.NOTE] В этом учебнике используется Azure PowerShell, для которого требуется операционная система Windows.

- Установите пакет Azure SDK для Node.js: <a href="http://go.microsoft.com/fwlink/?LinkId=254279">Установщик Windows</a> 

- Установите и настройте [Azure Powershell](install-configure-powershell.md).


## Создание проекта облачной службы Azure

Выполните следующие задачи для создания нового проекта облачной службы Azure с основными функциями формирования Node.js:


1. Запустите **Azure PowerShell** от имени администратора. В меню **Пуск** или на **начальном экране** найдите **Azure PowerShell**.

2.  Введите следующий командлет PowerShell, чтобы создать проект:

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Командлет **New-AzureServiceProject** формирует базовую структуру для публикации приложения Node.js в облачной службе. Он содержит файлы конфигурации, необходимые для публикации в Azure. Командлет также изменяет рабочий каталог на каталог для службы.

	Командлет  создает следующие файлы:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: 
        это файлы, необходимые для публикации приложения, которые используются только в. Дополнительные сведения см. в разделе
        [Обзор создания размещенной службы для Azure][].

	-   **deploymentSettings.json**: хранит локальные параметры, 
         используемые командлетами развертывания Windows Azure PowerShell.


4.  Введите следующую команду, чтобы добавить новую веб-роль:

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Командлет **Add-AzureNodeWebRole** создает базовое приложение Node.js. Он также изменяет файлы **.csfg** и **.csdef**, чтобы добавить записи конфигурации для новой роли.

	> [AZURE.NOTE] Если имя роли не указано, используется имя по умолчанию. Можно ввести имя в качестве первого параметра командлета: `Add-AzureNodeWebRole MyRole`


Приложение Node.js определяется в файле **server.js**, который находится в каталоге веб-роли (** WebRole1** по умолчанию). Ниже приведен код:

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Данный пример кода является практически аналогичным образцу Hello World на веб-сайте [nodejs.org][], за исключением того, что он использует номер порта, назначенный средой облака.


## Локальный запуск приложения в облаке

Одним из инструментов, устанавливаемых при установке пакета SDK для Azure, является
эмулятор вычислений Azure, который позволяет тестировать приложение локально. Свойство
вычислений моделирует среду, в которой будет работать приложение,
если его развернуть в облаке. 

1.  Введите следующий командлет Azure PowerShell для запуска службы в эмуляторе:

        Start-AzureEmulator -Launch

	Используйте параметр **-Launch** для автоматического открытия окна браузера при работе веб-роли в эмуляторе. В окне браузера отобразится Hello World, как показано на приведенном ниже снимке экрана. 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Чтобы остановить эмулятор вычисления, используйте командлет **Stop-AzureEmulator**:
	
		Stop-AzureEmulator

## Развертывание приложения в Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Загрузка настроек публикации Azure

Для развертывания приложения в Azure сначала нужно скачать настройки публикации для вашей подписки Azure. 

1.  Выполните следующий командлет Azure PowerShell:

        Get-AzurePublishSettingsFile

	Для перехода на страницу загрузки настроек публикации будет использован браузер. Может отобразиться предложение войти в систему с помощью учетной записи Майкрософт. В таком случае подписка Azure будет связана с этой учетной записью.

	Сохраните загруженный профиль в расположении файла, к которому есть доступ.

2.  Выполните следующий командлет, чтобы импортировать профиль публикации, который вы загрузили:

        Import-AzurePublishSettingsFile [путь к файлу]


	> [AZURE.NOTE] После импорта настроек публикации удалите загруженный файл PUBLISHSETTINGS, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи.
    

### Публикация приложения

Чтобы опубликовать, запустите командлет **Publish-AzureServiceProject** следующим образом.

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** указывает имя для развертывания. Это должно быть уникальное имя, в противном случае произойдет сбой в процессе публикации.

- Параметр **location** указывает центр обработки данных, в котором будет размещаться приложение. Чтобы просмотреть список доступных центров обработки данных, воспользуйтесь командлетом **Get-AzureLocation**.

- **-Launch** открывает окно браузера и переходит к размещенной службе после завершения развертывания.

После успешной публикации появится следующий ответ:

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> При первой публикации развертывание приложения может занять 5-7 минут, после этого оно становится доступным.

После завершения развертывания откроется окно браузера и можно будет перейти в облачную службу.


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

Теперь приложение работает в Azure!

Командлет **Publish-AzureServiceProject** выполняет следующие действия.

1.  Создает пакет развертывания. Пакет содержит все файлы в папке приложения.

2.  Создайте новую **учетную запись хранения**, если она не существует. Учетная запись хранения Azure используется для хранения пакета приложения во время развертывания. После развертывания можно безопасно удалить учетную запись хранения.

3.  Создайте новую **облачную службу**, если она не существует. **Облачная служба** - это контейнер, в котором размещается приложение при его развертывании в Azure. Дополнительную информацию см. в разделе [Обзор создания размещенной службы для Azure][].

4.  Публикует пакет развертывания в Azure.



## Остановка и удаление приложения

После развертывания приложения необходимо отключить его, чтобы избежать лишних затрат. Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

1.  В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:

        Stop-AzureService

	Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Чтобы удалить службу, вызовите следующий командлет:

        Remove-AzureService

	При появлении запроса введите **Y**, чтобы удалить службу.

	Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] При удалении службы учетная запись хранения, созданная при первоначальной публикации службы, не удаляется. Оплата за использование хранилища будет насчитываться. Дополнительную информацию об удалении учетной записи хранения см. в разделе [Удаление учетной записи хранения из подписки Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[Меню "Пуск" Windows с развернутым узлом Azure SDK Node.js]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Список каталогов папки helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Обзор создания размещенной службы для Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Список каталогов папки WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Меню, которое выводится, если щелкнуть правой кнопкой мыши эмулятор Azure на панели задач.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[В окне браузера отображается http://www.windowsazure.com/ с выделенной ссылкой "Бесплатная пробная версия"]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Окно браузера со страницей входа через liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer отображается диалоговое окно "Сохранить как" для файла publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Полный вывод состояния команды Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Удаление учетной записи хранения из подписки Azure]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=52-->
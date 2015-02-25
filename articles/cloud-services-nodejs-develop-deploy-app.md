<properties urlDisplayName="Cloud Service" pageTitle="Руководство по началу работы с Node.js - Учебник по Azure" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="Полноценный учебник, в котором объясняется процесс разработки простого веб-приложения и  его развертывания на Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Построение и развертывание приложения Node.js в облачной службе Azure

После завершения этого руководства вы получите простое приложение Node.js, работающее в облачной службе Azure. Облачные службы являются основой для построения масштабируемых облачных приложений в Azure. Они обеспечивают разделение, независимое управление и масштабирование компонентов внешнего и внутреннего интерфейса приложения.  Облачные службы предоставляют надежную выделенную виртуальную машину для надежного размещения каждой роли.

Дополнительные сведения об облачных службах и сравнение их с веб-сайтами Azure и виртуальными машинами см. в разделе [Сравнение веб-сайтов Azure, облачных служб и виртуальных машин ](http://azure.microsoft.com/ru-ru/documentation/articles/choose-web-site-cloud-service-vm/).

<p />

<div class="dev-callout"><strong>Требуется собрать простой веб-сайт?</strong>
<p>Если сценарий включает только простой веб-интерфейс, рассмотрите возможность <a href="/ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/">использования облегченного веб-сайта Azure.</a> По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе.</p>
</div>
<br />

Руководствуясь этим учебником, вы соберете простое веб-приложение, размещенное в веб-роли. Эмулятор вычислений будет использоваться для тестирования приложения локально, а затем будет развернут с помощью средств командной строки PowerShell.

Снимок экрана завершенного приложения приведен ниже:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt = "в окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.">
</p>



## Создание нового приложения узла

Выполните следующие задачи для создания нового проекта облачной службы Azure с основными функциями формирования Node.js:

1. Из **меню "Пуск"** или c **рабочего стола** выполните поиск **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Создайте новый каталог **узла** на диске C и перейдите в c:\\каталог узла:
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Введите следующий командлет, чтобы создать новое решение:

        PS C:\node> New-AzureServiceProject helloworld

    	Вы увидите следующий ответ:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Командлет **New-AzureServiceProject** формирует базовую структуру для создания нового приложения Azure Node, которое будет опубликовано в облачной службе. Он содержит файлы конфигурации, необходимые для публикации в Azure. Командлет также изменяет рабочий каталог на каталог для службы.

	Командлет **New-AzureServiceProject** создает следующие файлы.

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** и **ServiceDefinition.csdef**
        являютсяспециальными файлами Azure, необходимыми для
        публикации вашегоприложения.
		
	Дополнительную информацию об этих файлах см. в разделе
        [Обзор создания размещенной службы для Azure][].

	-   **deploymentSettings.json** хранит локальные параметры, используемые
        командлетами развертыванияAzure PowerShell.

4.  Введите следующую команду, чтобы добавить новую веб-роль с помощью **командлета Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Вы увидите следующий ответ:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Командлет **Add-AzureNodeWebRole** создает новый каталог для приложения и формирует шаблоны для базового приложения Node.js. Он также изменяет файлы **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** и **ServiceDefinition.csdef**, созданные на предыдущем шаге, чтобы добавить записи конфигурации для новой роли.

	<div class="dev-callout">
	<b>Примечание.</b>
	<p>По умолчанию, если не указано имя роли, одно имя будет создано для вас. Можно ввести имя в качестве первого параметра <b>Add-AzureNodeWebRole</b>. Например: <code>Add-AzureNodeWebRole MyRole</code></p>
	</div>

5.  Выполнив следующие команды, перейдите в каталог **WebRole1** и откройте файл **server.js** в блокноте. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Файл **Server.js** был создан командлетом **Add-AzureNodeWebRole** и содержит следующий стартовый код. Этот код похож на образец "Hello, World!" на веб-сайте [nodejs.org][], за исключением следующих отличий.

   	-   Порт был изменен, чтобы приложение могло найти правильный порт, назначенный ему облачной средой.
   	-   Журнал консоли был удален.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Выполнение приложения в эмуляторе локально

Одним из средств, устанавливаемых при установке Azure SDK, является эмулятор вычислений Azure, который позволяет протестировать приложение локально. Он имитирует среды, в которых приложение будет работать при развертывании в облаке. Выполните следующие действия, чтобы проверить приложение в эмуляторе.

1.  Закройте Блокнот и вернитесь в окно Windows PowerShell.
  	Введите следующий командлет для запуска службы в эмуляторе:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Параметр **-Launch** определяет, что средства автоматически открывают окно браузера и отображают приложение после его запуска в эмуляторе. Браузер открывает и отображает "Hello World", как показано на нижеприведенном снимке экрана. Это означает, что служба выполняется в эмуляторе среды выполнения приложений и работает правильно.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Чтобы остановить эмулятор среды выполнения приложений используйте команду **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Развертывание приложения в Azure

	[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Скачивание пакета настроек публикации Azure

Для развертывания приложения в Azure необходимо сначала скачать настройки публикации для подписки Azure. Следующие шаги описывают этот процесс:

1.  В окне Windows PowerShell откройте страницу загрузки, выполнив следующий командлет:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Для перехода на страницу загрузки настроек публикации будет использован браузер. Может отобразиться предложение войти в систему с помощью учетной записи Майкрософт. В таком случае подписка Azure будет связана с этой учетной записью.

	Сохраните загруженный профиль в расположении файла, к которому есть доступ.

2.  В окне PowerShell Azure используйте следующий командлет для настройки Windows PowerShell для Node.js, чтобы использовать профиль публикации Azure, который вы скачать:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	<div class="dev-callout">
	<b>Примечание.</b>
	<p>После импорта настроек публикации удалите загруженный файл .publishSettings, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи.</p>
	</div>
    

### Публикация приложения

1.  Опубликуйте приложение с помощью командлета **Publish-AzureServiceProject**, как показано ниже.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Параметр **servicename** указывает имя, используемое для этого развертывания. Это должно быть уникальное имя, в противном случае произойдет сбой в процессе публикации.

	- Параметр **location** указывает центр обработки данных, который будет размещать приложение. Чтобы просмотреть список доступных центров обработки данных, воспользуйтесь командлетом **Get-AzureLocation**.

	- Параметр **launch** будет запускать веб-обозреватель и перейдет к размещенной службе после завершения развертывания.

	После успешной публикации появится следующий ответ:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	Командлет **Publish-AzureServiceProject** выполняет следующие действия.

1.  Создает пакет, который будет развернут в Azure. Пакет содержит все файлы в папке приложения node.js.

2.  Создайте новую **учетную запись хранения**, если она не существует. Учетная запись хранения Azure используется для хранения пакета приложения во время развертывания. После развертывания можно безопасно удалить учетную запись хранения.

3.  Создайте новую **облачную службу**, если она не существует. **Облачная служба** - это контейнер, в котором размещается приложение при его развертывании в Azure. Дополнительные сведения см. в разделе [Обзор создания размещенной службы для Azure][].

4.  Публикует пакет развертывания в Azure.


	> [WACOM.NOTE]
	> При первой публикации развертывание приложения может занять 5-7 минут, после этого оно становится доступным.

	После завершения развертывания откроется окно браузера и можно будет перейти в облачную службу.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	Теперь приложение работает в Azure!


## Остановка и удаление приложения

После развертывания приложения необходимо отключить его, чтобы избежать лишних затрат. Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

1.  В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Чтобы удалить службу, вызовите следующий командлет:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	При появлении запроса введите **Y**, чтобы удалить службу.

	Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>При удалении службы учетная запись хранения, созданная при первоначальной публикации службы, не удаляется. Оплата за использование хранилища будет насчитываться. Дополнительную информацию об удалении учетной записи хранения см. в разделе
Удаление учетной записи хранения, см. <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/hh531562.aspx">Удаление учетной записи хранения из подписки Azure</a>.</p>
</div>


[Меню "Пуск" Windows с развернутым узлом Azure SDK Node.js]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Список каталогов папки helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Обзор создания размещенной службы для Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj155995.aspx
[Список каталогов папки WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Меню, которое выводится, если щелкнуть правой кнопкой мыши эмулятор Azure на панели задач.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[В окне браузера отображается http://www.windowsazure.com/ с выделенной ссылкой "Бесплатная пробная версия"]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Окно браузера со страницей входа через liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer отображается диалоговое окно "Сохранить как" для файла publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Полный вывод состояния команды Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Удаление учетной записи хранения из подписки Azure]: https://www.windowsazure.com/ru-ru/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=35.2-->

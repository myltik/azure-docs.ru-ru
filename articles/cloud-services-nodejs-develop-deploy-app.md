<properties linkid="dev-nodejs-getting-started" urlDisplayName="Облачная служба" pageTitle="Приступая к работе с Node.js – учебник по Azure" metaKeywords="Приступая к работе с Azure node., учебник по Azure Node.js, учебник по Azure Node.js" description="Комплексный учебник помогает в разработке веб-приложений Node.js и их развертывании в Azure." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Сборка и развертывание приложения Node.js в облачной службе Azure" authors=""  solutions="" writer="larryfr" manager="" editor=""  />






# Сборка и развертывание приложения Node.js в облачной службе Azure

После изучения этого руководства вы получите простое приложение Node.js, работающее в облачной службе Azure. Облачные службы являются основой для построения масштабируемых облачных приложений в Azure. Они позволяют осуществлять разделение, независимое управление и масштабирование интерфейсных и серверных компонентов приложения.  Облачные службы предоставляют надежную выделенную виртуальную машину для надежного размещения каждой роли.

Дополнительные сведения об облачных службах и сравнение их с веб-сайтами Azure и виртуальными машинами см. в разделе [Веб-сайты Azure, облачные службы и виртуальные машины: когда они используются?](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj218759.aspx)

<div class="dev-callout"><strong>Требуется собрать простой веб-сайт?</strong>
<p>Если ваш сценарий включает только простой интерфейсный сервер веб-сайта, следует рассмотреть возможность <a href="../create-a-website-(mac)">использования упрощенного веб-сайта Azure.</a> По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе.</p>
</div>
<br />

Руководствуясь этим учебником, вы соберете простое веб-приложение, размещенное в веб-роли. В эмуляторе среды выполнения приложений вы будете тестировать приложение локально, а затем развернете его с помощью программ командной строки PowerShell.

Снимок экрана завершенного приложения приведен ниже:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается на Azure.">
</p>



## Создание нового приложения узла

Выполните следующие задачи для создания нового проекта облачной службы Azure с основными функциями формирования Node.js:

1. В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

	![Значок Azure PowerShell][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Создайте новый каталог **узла** на диске C и перейдите в c:\\каталог узла:
	
	![В командной строке отображаются команды "mkdir c:\\node" и "cd node".][mkdir]

3.  Введите следующий командлет, чтобы создать новое решение:

        PS C:\node> New-AzureServiceProject helloworld

    	Вы увидите следующий ответ:

	![Результат выполнения команды New-AzureService helloworld](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Командлет **New-AzureServiceProject** создает базовую структуру для создания нового приложения узла Azure, который будет опубликован в облачной службе. Он содержит файлы конфигурации, необходимые для публикации в Azure. Командлет также изменяет рабочий каталог на каталог для службы.

	Файлы, созданные командлетом **New-AzureServiceProject**:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** и **ServiceDefinition.csdef** являются
        специфическими файлами Azure, необходимыми для публикации приложения.
		
	Дополнительные сведения об этих файлах см. в разделе
        [Обзор создания размещенной службы для Azure][].

	-   **deploymentSettings.json** хранит локальные параметры, используемые командлетами развертывания Azure PowerShell.

4.  Введите следующую команду, чтобы добавить новую веб-роль с помощью командлета
    **Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Вы увидите следующий ответ:

	![Вывод команды Add-AzureNodeWebRole.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Командлет **Add-AzureNodeWebRole** создает новый каталог для приложения и генерирует формирование шаблонов для базового приложения Node.js. Он также изменяет файлы **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** и **ServiceDefinition.csdef**, созданные на предыдущем шаге, чтобы добавить записи конфигурации для новой роли.

	<div class="dev-callout">
	<b>Примечание.</b>
	<p>По умолчанию, если не указано имя роли, одно имя будет создано для вас. Можно указать имя как первый параметр для <b>Add-AzureNodeWebRole</b>. Например, <code>Add-AzureNodeWebRole MyRole</code></p>
	</div>

5.  Используйте следующие команды для перехода к каталогу **WebRole1**, а затем откройте файл **server.js** в блокноте. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Файл **server.js** был создан командлетом **Add-AzureNodeWebRole** и содержит следующий стартовый код. Этот код похож на пример "Hello, World!" на веб-сайте [nodejs.org][], за исключением того, что:

   	-   Порт был изменен, чтобы разрешить приложению найти порт, назначенный ему средой облака.
   	-   Журнал консоли был удален.

	![Блокнот, отображающий содержимое server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Выполнение приложения в эмуляторе локально

Одним из средств, установленных с помощью пакета SDK Azure, является эмулятор среды выполнения приложений Azure для тестирования приложения локально. Эмулятор среды выполнения приложений имитирует среду выполнения приложения, развернутого в облаке. Выполните следующие действия, чтобы проверить приложение в эмуляторе.

1.  Закройте Блокнот и вернитесь в окно Windows PowerShell.
  	Введите следующий командлет для запуска службы в эмуляторе:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Параметр **-Launch** определяет, что средства автоматически открывают окно браузера и отображают приложение после его запуска в эмуляторе. Браузер открывает и отображает "Hello World", как показано на нижеприведенном снимке экрана. Это означает, что служба выполняется в эмуляторе среды выполнения приложений и работает правильно.

	![В окне браузера отображается веб-страница "Hello World"](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Чтобы остановить эмулятор среды выполнения приложений используйте команду **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Развертывание приложения в Azure

	[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Загрузка пакета настроек публикации Azure

Для развертывания приложения в Azure необходимо сначала загрузить настройки публикации для подписки Azure. Следующие шаги описывают этот процесс:

1.  В окне Windows PowerShell откройте страницу загрузки, выполнив следующий командлет:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Для перехода на страницу загрузки настроек публикации будет использован браузер. Может отобразиться предложение войти в систему с помощью учетной записи Майкрософт. В таком случае подписка Azure будет связана с этой учетной записью.

	Сохраните загруженный профиль в расположении файла, к которому есть доступ.

2.  В окне PowerShell Azure используйте следующий командлет для настройки Windows PowerShell для Node.js, чтобы использовать профиль публикации Azure, который вы загрузили:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	<div class="dev-callout">
	<b>Примечание.</b>
	<p>После импорта настроек публикации удалите загруженный файл .publishSettings, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи.</p>
	</div>
    

### Публикация приложения

1.  Опубликуйте приложения с помощью командлета **Publish-AzureServiceProject**, как показано ниже.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Параметр **servicename** указывает имя, используемое для этого развертывания. Это должно быть уникальное имя, в противном случае произойдет сбой в процессе публикации.

	- Параметр **location** указывает центр обработки данных, в котором будет размещаться приложение. Чтобы просмотреть список доступных центров обработки данных, используйте командлет **Get-AzureLocation**.

	- Параметр **launch** запустит браузер и перейдет к размещенной службе после завершения развертывания.

	После успешной публикации появится следующий ответ:

	![Вывод команды Publish-AzureService](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	Командлет **Publish-AzureServiceProject** выполняет следующие шаги:

1.  Создает пакет, который будет развернут в Azure. Пакет содержит все файлы в папке приложения node.js.

2.  Создайте новую **учетную запись хранения**, если она не существует. Учетная запись хранения Azure используется для хранения пакета приложения во время развертывания. После развертывания можно безопасно удалить учетную запись хранения.

3.  Создайте новую **облачную службу**, если она не существует. При развертывании приложения в Azure **облачная служба** служит контейнером для размещения приложения. Дополнительные сведения см. в разделе [Обзор создания размещенной службы для Azure][].

4.  Публикует пакет развертывания в Azure.


	> [WACOM.NOTE]
	> Развертывание приложения и его доступность после публикации может занять от 5 до 7 минут.

	После завершения развертывания откроется окно браузера и можно будет перейти в облачную службу.


	![В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	Теперь приложение работает в Azure!


## Остановка и удаление приложения

После развертывания приложения необходимо отключить его, чтобы избежать лишних затрат. Azure берет почасовую плату с экземпляров веб-ролей за использование сервера. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

1.  В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

	![Состояние команды Stop-AzureService](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Чтобы удалить службу, вызовите следующий командлет:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	При появлении запроса введите **Y**, чтобы удалить службу.

	Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

	![Состояние команды Remove-AzureService](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>При удалении службы учетная запись хранения, созданная при первоначальной публикации службы, не удаляется. Оплата за использование хранилища будет насчитываться. Дополнительные сведения об удалении учетной записи хранения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/hh531562.aspx">Удаление учетной записи хранения из подписки Azure</a>.</p>
</div>


[Развернется меню "Пуск" Windows с записью Node.js SDK Azure]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Список каталогов папки helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Обзор создания размещенной службы для Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj155995.aspx
[Список каталогов папки WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Меню, которое появляется, если щелкнуть правой кнопкой мыши эмулятор Azure на панели задач.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Окно браузера со страницей http://www.windowsazure.com/ с выделенной ссылкой на бесплатное пробное использование]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Окно браузера со страницей входа liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer отображает диалоговое окно "Сохранить как" для файла publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Полный вывод состояния команды Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Удаление учетной записи хранения из подписки Azure]: https://www.windowsazure.com/ru-ru/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png


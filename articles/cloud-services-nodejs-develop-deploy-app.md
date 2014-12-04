<properties linkid="dev-nodejs-getting-started" urlDisplayName="Cloud Service" pageTitle="Node.js Getting Started Guide - Azure Tutorial" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="An end-to-end tutorial that helps you develop a simple Node.js web application and deploy it to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Построение и развертывание приложения Node.js в облачной службе Azure

После изучения этого руководства вы получите простое приложение Node.js, работающее в облачной службе Azure. Облачные службы являются основой для построения масштабируемых облачных приложений в Azure. Они позволяют осуществлять разделение, независимое управление и масштабирование интерфейсных и серверных компонентов приложения. Облачные службы предоставляют надежную выделенную виртуальную машину для надежного размещения каждой роли.

Дополнительную информацию об облачных службах и сравнении их с веб-сайтами Azure и виртуальными машинами см. раздел [Сравнение веб-сайтов Azure, облачных служб и виртуальных машин][Сравнение веб-сайтов Azure, облачных служб и виртуальных машин].
<p />
<div class="dev-callout"><strong>Требуется собрать простой веб-сайт?</strong>
<p>Если ваш сценарий включает только простой интерфейсный сервер веб-сайта, следует рассмотреть возможность <a href="/ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/">использования упрощенного веб-сайта Azure.</a> По мере роста веб-сайта и изменения требований можно легко выполнить обновление к облачной службе.</p>
<p><br /></p>

Руководствуясь этим учебником, вы соберете простое веб-приложение, размещенное в веб-роли. В эмуляторе среды выполнения приложений вы будете тестировать приложение локально, а затем развернете его с помощью программ командной строки PowerShell. Снимок экрана завершенного приложения приведен ниже:

![В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.][В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.]

## Создание нового приложения узла

Выполните следующие задачи для создания нового проекта облачной службы Azure с основными функциями формирования Node.js:

1.  В меню **Пуск** или на **Начальном экране** найдите **Azure PowerShell**. Щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.

    ![Значок Azure PowerShell][Значок Azure PowerShell]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Создайте новый каталог **узла** на диске C и перейдите в c:\\каталог узла:

	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Введите следующий командлет, чтобы создать новое решение:

        PS C:\node> New-AzureServiceProject helloworld
        You will see the following response:

    ![Результат выполнения команды New-AzureService helloworld][Результат выполнения команды New-AzureService helloworld]

    Командлет **New-AzureServiceProject** создает базовую структуру для создания нового приложения узла Azure, который будет опубликован в облачной службе. Он содержит файлы конфигурации, необходимые для публикации в Azure. Командлет также изменяет рабочий каталог на каталог для службы.

    Файлы, созданные командлетом **New-AzureServiceProject**:

    -   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** и **ServiceDefinition.csdef** являются
        специальными файлами Azure, необходимыми для публикации вашего
        приложения.

    Дополнительную информацию об этих файлах см. в разделе [Обзор создания размещенной службы для Windows Azure.][Обзор создания размещенной службы для Windows Azure.]

    -   **deploymentSettings.json** хранит локальные параметры, используемые командлетами развертывания
        Azure PowerShell.

4.  Введите следующую команду, чтобы добавить новую веб-роль с помощью командлета **Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

    Вы увидите следующий ответ:

    ![Вывод команды Add-AzureNodeWebRole.][Вывод команды Add-AzureNodeWebRole.]

    Командлет **Add-AzureNodeWebRole** создает новый каталог для приложения и генерирует формирование шаблонов для базового приложения Node.js. Также он изменяет файлы **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** и **ServiceDefinition.csdef**, созданные на предыдущем шаге, чтобы добавить записи о конфигурации к новой роли.

    <div class="dev-callout">
<b>Примечание.</b>
<p>По умолчанию, если не указано имя роли, одно имя будет создано для вас. Можно указать имя как первый параметр для <b>Add-AzureNodeWebRole</b>. Например, <code data-inline="1">Add-AzureNodeWebRole MyRole</code></p>
</div>

5.  Используйте следующие команды для перехода к каталогу **WebRole1**, а затем откройте файл **server.js** в блокноте.

    PS C:\\node\\helloworld\> cd WebRole1
     PS C:\\node\\helloworld\\WebRole1\> notepad server.js

    Файл **server.js** был создан командлетом **Add-AzureNodeWebRole** и содержит следующий стартовый код. Этот код похож на пример «Hello, World!» на веб-сайте [nodejs.org][nodejs.org], за исключением того, что:

    -   Порт был изменен, чтобы разрешить приложению найти порт,
        назначенный ему средой облака.
    -   Журнал консоли был удален.

    ![Блокнот, отображающий содержимое server.js][Блокнот, отображающий содержимое server.js]

## Выполнение приложения в эмуляторе локально

Одним из средств, установленных с помощью пакета Azure SDK, является эмулятор среды выполнения приложений Azure для тестирования приложения локально. Эмулятор среды выполнения приложений имитирует среду выполнения приложения, развернутого в облаке. Выполните следующие действия, чтобы проверить приложение в эмуляторе.

1.  Закройте Блокнот и вернитесь в окно Windows PowerShell.
    Введите следующий командлет для запуска службы в эмуляторе:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

    Параметр **-Launch** определяет, что средства автоматически открывают окно браузера и отображают приложение после его запуска в эмуляторе. Браузер открывает и отображает "Hello World", как показано на нижеприведенном снимке экрана. Это означает, что служба выполняется в эмуляторе среды выполнения приложений и работает правильно.

    ![В окне браузера отображается веб-страница "Hello World"][В окне браузера отображается веб-страница "Hello World"]

2.  Чтобы остановить эмулятор среды выполнения приложений используйте команду **Stop-AzureEmulator**:

    PS C:\\node\\helloworld\\WebRole1\> Stop-AzureEmulator

## Развертывание приложения в Azure

    [WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### <span id="download_publishing_settings"></span></a>Скачивание пакета настроек публикации Azure

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

1.  Опубликуйте приложения с помощью командлета **Publish-AzureServiceProject**, как показано
    ниже.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

    -   Параметр **servicename** указывает имя, используемое для этого развертывания. Это должно быть уникальное имя, в противном случае произойдет сбой в процессе публикации.

    -   Параметр **location** указывает центр обработки данных, в котором будет размещаться приложение. Чтобы просмотреть список доступных центров обработки данных, используйте командлет **Get-AzureLocation**.

    -   Параметр **launch** запустит браузер и перейдет к размещенной службе после завершения развертывания.

    После успешной публикации появится следующий ответ:

    ![Вывод команды Publish-AzureService][Вывод команды Publish-AzureService]

    Командлет **Publish-AzureServiceProject** выполняет следующие шаги:

2.  Создает пакет, который будет развернут в Azure. Пакет содержит все файлы в папке приложения node.js.

3.  Создайте новую **учетную запись хранения**, если она не существует. Учетная запись хранения Azure используется для хранения пакета приложения во время развертывания. После развертывания можно безопасно удалить учетную запись хранения.

4.  Создайте новую **облачную службу**, если она не существует. При развертывании приложения в Azure **облачная служба** служит контейнером для размещения приложения. Дополнительные сведения см. в разделе [Обзор создания размещенной службы для Azure][Обзор создания размещенной службы для Windows Azure.].

5.  Публикует пакет развертывания в Azure.

    > [WACOM.NOTE]
    > Развертывание приложения и его доступность после публикации может занять от 5 до 7 минут.

    После завершения развертывания откроется окно браузера и можно будет перейти в облачную службу.

    ![В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.][1]

    Теперь приложение работает в Azure!

## Остановка и удаление приложения

После развертывания приложения необходимо отключить его, чтобы избежать лишних затрат. Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

1.  В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

    Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

    ![Состояние команды Stop-AzureService][Состояние команды Stop-AzureService]

2.  Чтобы удалить службу, вызовите следующий командлет:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

    При появлении запроса введите **Y**, чтобы удалить службу.

    Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

    ![Состояние команды Remove-AzureService][Состояние команды Remove-AzureService]

    <div class="dev-callout">
<strong>Примечание.</strong>
<p>При удалении службы учетная запись хранения, созданная при первоначальной публикации службы, не удаляется. Оплата за использование хранилища будет насчитываться. Дополнительную информацию об удалении учетной записи хранения см. в разделе
<a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/hh531562.aspx">Как удалить учетную запись хранения из подписки Azure</a>.</p>
</div>

  [Сравнение веб-сайтов Azure, облачных служб и виртуальных машин]: http://azure.microsoft.com/ru-ru/documentation/articles/choose-web-site-cloud-service-vm/
  [В окне браузера отображается страница hello world. URL-адрес указывает, что страница размещается в Azure.]: https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png
  [Значок Azure PowerShell]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
  [Результат выполнения команды New-AzureService helloworld]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
  [Обзор создания размещенной службы для Windows Azure.]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj155995.aspx
  [Вывод команды Add-AzureNodeWebRole.]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
  [nodejs.org]: http://nodejs.org/
  [Блокнот, отображающий содержимое server.js]: ./media/cloud-services-nodejs-develop-deploy-app/node13.png
  [В окне браузера отображается веб-страница "Hello World"]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
  [Вывод команды Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
  [1]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
  [Состояние команды Stop-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
  [Состояние команды Remove-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
  [The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
  [mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
  [nodejs.org]: http://nodejs.org/
  [A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
  [Overview of Creating a Hosted Service for Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj155995.aspx
  [A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
  [The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
  [A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
  [A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
  [Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png
  [The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
  [How to Delete a Storage Account from an Azure Subscription]: https://www.windowsazure.com/ru-ru/manage/services/storage/how-to-manage-a-storage-account/
  [powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
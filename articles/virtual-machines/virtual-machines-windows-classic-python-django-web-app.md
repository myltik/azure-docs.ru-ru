<properties
	pageTitle="Веб-приложение Python на Django | Microsoft Azure"
	description="В этом учебнике изучается размещение веб-сайта, созданного на основе Django, в Azure с помощью виртуальной машины Windows Server 2012 R2 Datacenter и классической модели развертывания."
	services="virtual-machines-windows"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>


<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="huvalo"/>


# Веб-приложение Hello World на Django на виртуальной машине Windows Server

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 

В этом учебнике описывается, как разместить веб-сайт на основе Django в Microsoft Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у вас нет опыта использования платформы Azure. По завершении работы с данным учебником у вас будет приложение на базе Django, выполняемое в облаке.

Вы научитесь:

* Настраивать виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в Windows Server, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Azure.
* Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello World. Приложение будет размещаться в виртуальной машине Azure.

Ниже приведен снимок экрана готового приложения.

![В окне браузера в Azure отображается страница hello world.][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1. Чтобы создать виртуальную машину Azure с Windows Server 2012 R2 Datacenter, следуйте [этим](virtual-machines-windows-classic-tutorial.md) указаниям.

1. Дайте Azure команду для перенаправления трафика порта 80 из Интернета в порт 80 на виртуальной машине:
 - Перейдите к только что созданной виртуальной машине на классическом портале Azure и откройте вкладку **КОНЕЧНЫЕ ТОЧКИ**.
 - В нижней части страницы нажмите кнопку **ДОБАВИТЬ**. ![добавление конечной точки](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Откройте **ОБЩИЙ ПОРТ 80** протокола **TCP** как **ЧАСТНЫЙ ПОРТ 80**. ![][port80]
1. На вкладке **ПАНЕЛЬ МОНИТОРИНГА** щелкните **ПОДКЛЮЧИТЬ**, чтобы использовать **Удаленный рабочий стол** для удаленного входа в созданную виртуальную машину Azure.  

**Важное примечание.** Во всех инструкциях ниже подразумевается, что вы правильно выполнили вход на виртуальную машину и выполняете команды на ней, а не на локальном компьютере.

## <a id="setup"> </a>Установка Python, Django, WFastCGI

**Примечание.** Для скачивания с помощью Internet Explorer может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите «Пуск > Администрирование > Диспетчер серверов > Локальный сервер», щелкните **Конфигурация усиленной безопасности Internet Explorer** и установите значение «Выкл.»).

1. Установите последнюю версию Python 2.7 или 3.4 c сайта [python.org][].
1. Установите пакеты wfastcgi и django с помощью pip.

    Для Python 2.7 используйте указанную ниже команду.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Для Python 3.4 используйте указанную ниже команду.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## Установка IIS с помощью FastCGI

1. Установите службы IIS с поддержкой FastCGI. Это может занять несколько минут.

		start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## Создание нового приложения Django

1.  В папке *C:\\inetpub\\wwwroot* введите следующую команду для создания нового проекта Django:

    Для Python 2.7 используйте указанную ниже команду.

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Для Python 3.4 используйте указанную ниже команду.

		C:\Python34\Scripts\django-admin.exe startproject helloworld

	![Результат выполнения команды New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  Команда **django-admin** создает базовую структуру для веб-сайтов на основе Django:

  -   **helloworld\\manage.py** помогает вам начать и остановить размещение веб-сайта Django.
  -   **helloworld\\helloworld\\settings.py** содержит параметры Django для приложения.
  -   **helloworld\\helloworld\\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

1.  Создайте новый файл с именем **views.py** в папке *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:

		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Замените содержимое файла urls.py на приведенный ниже код.

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

## Настройка IIS

1. Разблокируйте раздел обработчиков в глобальном файле applicationhost.config. В результате вы сможете использовать обработчик python в файле web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Включите WFastCGI. В результате приложение будет добавлено в глобальный файл applicationhost.config, в котором имеются ссылки на исполняемый файл интерпретатора Python и сценарий wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Создайте файл web.config в каталоге *C:\\inetpub\\wwwroot\\helloworld*. Значение атрибута `scriptProcessor` должно соответствовать выходным данным предыдущего шага. Дополнительные сведения о настройках wfastcgi приведены на странице [wfastcgi][] в pypi.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Измените расположение веб-сайта IIS по умолчанию, указав папку проекта django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Наконец, загрузите веб-страницу в браузере.

![В окне браузера в Azure отображается страница hello world.][1]


## Завершение работы виртуальной машины Azure

По завершении работы с данным учебником завершите работу созданной вами виртуальной машины Azure или удалите ее, чтобы освободить ресурсы для других учебников и избежать платы за использование Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=AcomDC_0323_2016-->
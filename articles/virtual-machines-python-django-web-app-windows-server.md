<properties 
	pageTitle="Веб-приложение Python на Django - учебник по Azure" 
	description="В этом учебнике описывается размещение веб-сайта, созданного на основе Django, в Azure с помощью виртуальной машины Windows Server 2012 R2 Datacenter." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Веб-приложение Hello World на Django

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

В этом учебнике описывается, как разместить веб-сайт на основе Django в Microsoft 
Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у вас нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

* Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Windows Server**, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Windows Azure. 
* Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello World
. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![A browser window displaying the hello world page on Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1. Следуйте приведенным [здесь][portal-vm] инструкциям, чтобы создать виртуальную машину Azure для распространения*Windows Server 2012 R2 Datacenter*.

1. Дайте Azure команду для перенаправления трафика порта **80** из Интернета в порт **80** на виртуальной машине:
 - Перейдите к только что созданной виртуальной машине на портале и откройте вкладку *ENDPOINTS*.
 - Нажмите кнопку *ADD*, расположенную в нижней части экрана.
	![add endpoint](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Откройте *PUBLIC PORT 80* протокола *TCP* как *PRIVATE PORT 80*.
![][port80]
1. На вкладке *DASHBOARD* щелкните *CONNECT*, чтобы использовать *Remote Desktop* для удаленного входа в созданную виртуальную машину Azure.  

**Важное примечание.** Все следующие указания подразумевают, что вы правильно выполнили вход на виртуальную машину и выполняете команды там, а не на локальном компьютере! 

## <a id="setup"> </a>Настройка Python и Django

**Примечание.** Для скачивания с помощью Internet Explorer может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск > Администрирование > Диспетчер серверов > Локальный сервер",  щелкните **Конфигурация усиленной безопасности Internet Explorer** и установите значение "Выкл.").

1. Установите [установщик веб-платформы][].
1. Установите Python и WFastCGI с помощью установщика веб-платформы.  В папку сценариев Python будет установлен wfastcgi.py.
	1. Запустите установщик веб-платформы.
	1. В строке поиска введите WFastCGI. 
	1. Выберите запись WFactCGI для версии Python, которую вы хотите использовать (2.7 или 3.4).  Обратите внимание, что Python будет установлен в виде зависимости WFastCGI. 
1. Установите Django с помощью pip.

    Python 2.7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django


## Настройка служб IIS с помощью FastCGI

1. Установите службы IIS с поддержкой FastCGI.  Это может занять несколько минут.

		start /wait %windir%\System32\\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


### Python 2.7

Выполните эти команды только для версии Python 2.7.

1. Настройте обработчик Python FastCGI.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"


1. Зарегистрируйте обработчик для этого сайта.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Настройте обработчик для запуска приложения Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Настройте PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Сообщите шлюзу FastCGI-WSGI, какой обработчик WSGI использовать:

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Вы увидите следующее:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png) 

### Python 3.4

Выполните эти команды только для версии Python 3.4.

1. Настройте обработчик Python FastCGI.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"


1. Зарегистрируйте обработчик для этого сайта.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Настройте обработчик для запуска приложения Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Настройте PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Сообщите шлюзу FastCGI-WSGI, какой обработчик WSGI использовать:

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

1. Вы увидите следующее:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png) 


## Создание нового приложения Django


1.  Из *C:\inetpub\wwwroot* введите следующую команду для создания нового проекта Django:

    Python 2.7:

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

		C:\Python34\Scripts\django-admin.exe startproject helloworld
    
	![The result of the New-AzureService command](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  Команда **django-admin** создает базовую структуру для веб-сайтов на основе Django:
    
  -   **helloworld\manage.py** помогает вам начать и остановить размещение веб-сайта Django.
  -   **helloworld\helloworld\settings.py** содержит параметры Django для приложения.
  -   **helloworld\helloworld\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.



1.  Создайте новый файл **views.py** в каталоге *C:\inetpub\wwwroot\helloworld\helloworld*. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Замените содержимое файла **urls.py** следующим кодом:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

1. Наконец, загрузите веб-страницу в браузере.

![A browser window displaying the hello world page on Azure][1]

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /manage/windows/tutorials/virtual-machine-from-gallery/

[установщик веб-платформы]: http://www.microsoft.com/web/downloads/platform.aspx


<!--HONumber=47-->

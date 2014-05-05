<properties linkid="develop-python-web-app-with-django" urlDisplayName="Веб-сайт на Django (Windows)" pageTitle="Веб-приложение на Python с Django — учебник по Azure" metaKeywords="Веб-приложение Azure Django, виртуальная машина Azure Django" description="Учебник, показывающий, как разместить веб-сайт Django в Azure с помощью виртуальной машины Windows Server 2008 R2." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Веб-приложение Hello World на Django" authors="" solutions="" manager="" editor="" />




# Веб-приложение Hello World на Django

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/ru-ru/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

В этом руководстве описывается, как разместить веб-сайт на основе Django в Windows
Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

* Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Windows Server**, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Azure. 
* Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello
World. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.][1]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1. Следуйте приведенным [здесь][portal-vm] инструкциям, чтобы создать виртуальную машину с дистрибутивом *Windows Server 2012 Datacenter*.

1. Дайте Azure команду для перенаправления трафика с порта **80** из Интернета на порт **80** на виртуальной машине:
 - Перейдите к только что созданной виртуальной машине в портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
 - Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
	![добавление конечной точки](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*.
![][port80]
1. С помощью *Удаленного рабочего стола* Windows удаленно войдите в созданную виртуальную машину Azure.  

**Важное замечание.** Все следующие инструкции подразумевают, что вы правильно выполнили вход на виртуальную машину и выполняете команды там, а не на локальном компьютере. 

## <a id="setup"> </a>Настройка среды разработки

Чтобы настроить среды Python и Django, см. дополнительные сведения в [руководстве по установке][].  

**Примечание 1.** Вам требуется *просто* установить продукт **Django** из установщика Windows WebPI на виртуальной машине Azure, чтобы активировать *этот* учебник.

**Примечание 2.** Для загрузки установщика WebPI может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск/Администрирование/Диспетчер серверов", щелкните элемент **Настроить конфигурацию усиленной безопасности Internet Explorer** и установите значение "Выкл.").

## Настройка служб IIS с помощью FastCGI


1. Установка служб IIS с поддержкой FastCGI 

		start /wait %windir%\System32\\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


1. Настройка обработчика Python Fast CGI

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"



1. Регистрация обработчика для этого сайта

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Настройка обработчика для запуска приложения Django

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

1. Настройка PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

	Вы увидите следующее:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png) 

1. Сообщите шлюзу FastCGI–WSGI, какой обработчик WSGI использовать:

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Загрузите файл wfastcgi.py из [codeplex](http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409) и сохраните его в C:\Python27\Scripts.  Это расположение использовалось предыдущими командами для регистрации обработчика FastCGI. Кроме того, можно установить его с помощью установщика веб-платформы.  Найдите значение "WFastCGI".


## Создание нового приложения Django


1.  Запустите cmd.exe
    
1.  Перейдите в папку C:\inetpub\wwwroot

1.  Введите следующую команду для создания нового проекта Django:


	C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication
    
	![Результат выполнения команды New-AzureService](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

 Скрипт **Django admin.py** создает базовую структуру для веб-сайтов на базе Django:
    
-   **manage.py** помогает вам начать и остановить размещение веб-сайта Django
-   **DjangoApplication\settings.py** содержит параметры Django для приложения.
-   **DjangoApplication\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.



1.  Создайте новый файл с именем **views.py** в подкаталоге *DjangoApplication* каталога *C:\inetpub\wwwroot\DjangoApplication* как элемент одного уровня с **urls.py**. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:
		
		from django.http import HttpResponse
		def hello(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Замените содержимое файла **urls.py** на код, приведенный ниже:

		from django.conf.urls.defaults import patterns, include, url
		from DjangoApplication.views import hello
		urlpatterns = patterns('',
			(r'^$',hello),
		)

1. Наконец, загрузите веб-страницу в браузере.

![В окне браузера в Azure отображается страница hello world.][1]

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/

[руководстве по установке]: ../python-how-to-install/


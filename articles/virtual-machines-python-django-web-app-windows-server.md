<properties urlDisplayName="Web with Django (Windows)" pageTitle="Веб-приложение Python на Django &mdash; учебник Azure" metaKeywords="Azure Django web app, Azure Django virtual machine" description="В этом учебнике описывается размещение веб-сайта, созданного на основе Django, в Azure с помощью виртуальной машины Windows Server 2012 R2 Datacenter." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Веб-приложение Hello World на Django" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Веб-приложение Hello World на Django

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/ru-ru/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

В этом учебнике описывается, как разместить веб-сайт на основе Django в Microsoft
Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

-   Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Windows Server**, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Windows Azure.
-   Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение
HelloWorld. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1.  Следуйте приведенным [здесь][здесь] указаниям, чтобы создать виртуальную машину Azure с дистрибутивом *Windows Server 2012 R2 Datacenter*.

2.  Дайте Azure команду для перенаправления трафика порта **80** из Интернета на порт **80** на виртуальной машине:

-   Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
-   Нажмите кнопку *ДОБАВИТЬ* в нижней части экрана.
    ![добавление конечной точки][добавление конечной точки]

-   Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*.
    ![][0]

1.  На вкладке *ПАНЕЛЬ МОНИТОРИНГА* щелкните *ПОДКЛЮЧИТЬ*, чтобы использовать *Удаленный рабочий стол* для удаленного входа в созданную виртуальную машину Azure.

**Важно!** Все следующие указания подразумевают, что вы правильно выполнили вход на виртуальную машину и выполняете команды там, а не на локальном компьютере.

## <span id="setup"></span> </a>Настройка Python и Django

**Примечание.** Для загрузки с помощью Internet Explorer может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск/Администрирование/Диспетчер серверов/Локальный сервер", щелкните элемент **Настроить конфигурацию усиленной безопасности Internet Explorer** и установите значение "Выкл.").

1.  Установите [установщик веб-платформы][установщик веб-платформы].
2.  Установите Python и WFastCGI с помощью установщика веб-платформы. В папку сценариев Python будет установлен wfastcgi.py.

    1.  Запустите установщик веб-платформы.
    2.  В строке поиска введите WFastCGI.
    3.  Выберите запись WFactCGI для версии Python, которую вы хотите использовать (2.7 или 3.4). Обратите внимание, что Python будет установлен в виде зависимости WFastCGI.

3.  В случае установки версии Python 2.7 [выполните эти инструкции для установки pip вручную][выполните эти инструкции для установки pip вручную] (в версии Python 3.4 уже установлен pip).
4.  Установите Django с помощью pip.

    Python 2.7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django

## Настройка служб IIS с помощью FastCGI

1.  Установите службы IIS с поддержкой FastCGI. Это может занять несколько минут.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

### Python 2,7

Выполните эти команды только для версии Python 2.7.

1.  Настройте обработчик Python FastCGI.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

2.  Зарегистрируйте обработчик для этого сайта.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Настройте обработчик для запуска приложения Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Настройте PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Сообщите шлюзу FastCGI—WSGI, какой обработчик WSGI использовать.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Вы увидите следующее:

    ![IIS config1][IIS config1]

### Python 3.4

Выполните эти команды только для версии Python 3.4.

1.  Настройте обработчик Python FastCGI.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"

2.  Зарегистрируйте обработчик для этого сайта.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Настройте обработчик для запуска приложения Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Настройте PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Сообщите шлюзу FastCGI—WSGI, какой обработчик WSGI использовать.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Вы увидите следующее:

    ![IIS config1][1]

## Создание нового приложения Django

1.  В папке *C:\\inetpub\\wwwroot* введите следующую команду для создания нового проекта Django:

    Python 2.7:

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Результат выполнения команды New-AzureService][Результат выполнения команды New-AzureService]

2.  Команда **django-admin** создает базовую структуру для веб-сайтов на основе Django:

-   **helloworld\\manage.py** помогает вам начать и остановить размещение веб-сайта Django.
-   **helloworld\\helloworld\\settings.py** содержит параметры Django для приложения.
-   **helloworld\\helloworld\\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

1.  Создайте новый файл с именем **views.py** в папке *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Замените содержимое файла **urls.py** на код, приведенный ниже:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

3.  Наконец, загрузите веб-страницу в браузере.

![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

  [В окне браузера в Azure отображается страница hello world.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [здесь]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/
  [добавление конечной точки]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [установщик веб-платформы]: http://www.microsoft.com/web/downloads/platform.aspx
  [выполните эти инструкции для установки pip вручную]: https://pip.pypa.io/en/latest/installing.html
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png
  [1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png
  [Результат выполнения команды New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png

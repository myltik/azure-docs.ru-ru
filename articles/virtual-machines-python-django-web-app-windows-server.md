<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Веб-приложение Hello World на Django

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/ru-ru/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

В этом учебнике описывается, как разместить веб-сайт на основе Django в Windows Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

-   Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Windows Server**, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Windows Azure.
-   Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение HelloWorld. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1.  Следуйте приведенным [здесь][здесь] указаниям, чтобы создать виртуальную машину Azure с дистрибутивом *Windows Server 2012 Datacenter*.

2.  Дайте Azure команду для перенаправления трафика порта **80** из Интернета на порт **80** на виртуальной машине:

 - Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
 - Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
    ![добавление конечной точки][добавление конечной точки]

 - Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*.
    ![][0]

1.  С помощью *Удаленного рабочего стола* Windows удаленно войдите в созданную виртуальную машину Azure.

**Важно!** Все следующие указания подразумевают, что вы правильно выполнили вход на виртуальную машину и выполняете команды там, а не на локальном компьютере.

## <span id="setup"></span> </a>Настройка среды разработки

Чтобы настроить среды Python и Django, см. дополнительные сведения в [руководстве по установке][руководстве по установке].

**Примечание 1.** Вам требуется *просто* установить продукт **Django** из установщика веб-платформы Windows на виртуальной машине Azure, чтобы активировать *этот* учебник.

**Примечание 2.** Для загрузки установщика веб-платформы может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите «Пуск/Администрирование/Диспетчер серверов», щелкните элемент **Настроить конфигурацию усиленной безопасности Internet Explorer** и установите значение «Выкл.»).

## Настройка служб IIS с помощью FastCGI

1.  Установка служб IIS с поддержкой FastCGI

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Настройка обработчика Python FastCGI

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  Регистрация обработчика для этого сайта

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Настройка обработчика для запуска приложения Django

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Настройка PYTHONPATH, чтобы приложение Django могло найти интерпретатор Python

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    Вы увидите следующее:

    ![IIS config1][IIS config1]

6.  Сообщите шлюзу FastCGI—WSGI, какой обработчик WSGI использовать:

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  Скачайте файл wfastcgi.py с [codeplex][codeplex] и сохраните его в C:\\Python27\\Scripts. Это расположение использовалось предыдущими командами для регистрации обработчика FastCGI. Кроме того, можно установить его с помощью установщика веб-платформы. Найдите значение "WFastCGI".

## Создание нового приложения Django

1.  Запустите cmd.exe

2.  Перейдите в папку C:\\inetpub\\wwwroot

3.  Введите следующую команду для создания нового проекта Django:

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![Результат выполнения команды New-AzureService][Результат выполнения команды New-AzureService]

 Сценарий **Django admin.py** создает базовую структуру для веб-сайтов на основе Django:

-   **manage.py** помогает вам начать и остановить размещение веб-сайта Django.
-   **DjangoApplication\\settings.py** содержит параметры Django для приложения.
-   **DjangoApplication\\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

1.  Создайте новый файл с именем **views.py** в подкаталоге *DjangoApplication* каталога *C:\\inetpub\\wwwroot\\DjangoApplication* как элемент одного уровня с **urls.py**. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Замените содержимое файла **urls.py** на код, приведенный ниже:

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  Наконец, загрузите веб-страницу в браузере.

![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

  [В окне браузера в Azure отображается страница hello world.]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [здесь]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/
  [добавление конечной точки]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [руководстве по установке]: ../python-how-to-install/
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [codeplex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [Результат выполнения команды New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png

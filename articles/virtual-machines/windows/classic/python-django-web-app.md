---
title: "Сборка приложения Django на виртуальной машине Azure | Документация Майкрософт"
description: "В этом учебнике изучается размещение веб-сайта, созданного на основе Django, в Azure с помощью виртуальной машины Windows Server 2012 R2 Datacenter и классической модели развертывания."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d777d2a7944d17a452732c0e820dc781357bc8d2
ms.lasthandoff: 04/03/2017


---
# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Веб-приложение Hello World на Django на виртуальной машине Windows Server
> [!div class="op_single_selector"]
> * [Windows](python-django-web-app.md)
> * [Mac/Linux](../../linux/python-django-web-app.md)
> 
> 

<br>

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Шаблон Resource Manager для развертывания Django см. [здесь](https://azure.microsoft.com/documentation/templates/django-app/).

В этом учебнике описывается, как разместить веб-сайт на основе Django в Microsoft Azure с помощью виртуальной машины Windows Server. В данном учебнике предполагается, что у вас нет опыта использования платформы Azure. По завершении работы с данным учебником у вас будет приложение на базе Django, выполняемое в облаке.

Вы научитесь:

* Настраивать виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в Windows Server, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Azure.
* Создайте новое приложение Django в Windows.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello World. Приложение будет размещаться в виртуальной машине Azure.

Ниже приведен снимок экрана готового приложения.

![В окне браузера в Azure отображается страница hello world.][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Создание и настройка виртуальной машины Azure для размещения Django
1. Чтобы создать виртуальную машину Azure с дистрибутивом Windows Server 2012 R2 Datacenter, следуйте [этим](tutorial.md) указаниям.
2. Дайте Azure команду для перенаправления трафика порта 80 из Интернета в порт 80 на виртуальной машине:
   
   * Перейдите к только что созданной виртуальной машине на классическом портале Azure и откройте вкладку **КОНЕЧНЫЕ ТОЧКИ** .
   * В нижней части страницы нажмите кнопку **ДОБАВИТЬ** .
     ![добавление конечной точки](./media/python-django-web-app/django-helloworld-addendpoint.png)
   * Откройте **PUBLIC PORT 80** (Общий порт 80) протокола **TCP** как **PRIVATE PORT 80** (Частный порт 80).
     ![][port80]
3. На вкладке **Панель мониторинга** щелкните **Подключить**, чтобы использовать **Удаленный рабочий стол** для удаленного входа в созданную виртуальную машину Azure.  

**Важное примечание.** Во всех инструкциях ниже подразумевается, что вы правильно выполнили вход на виртуальную машину и выполняете команды на ней, а не на локальном компьютере.

## <a id="setup"> </a>Установка Python, Django, WFastCGI
**Примечание.** Для скачивания с помощью Internet Explorer может потребоваться настроить параметры конфигурации усиленной безопасности Internet Explorer (выберите "Пуск > Администрирование > Диспетчер серверов > Локальный сервер", щелкните **Конфигурация усиленной безопасности Internet Explorer** и установите значение "Выкл.").

1. Установите последнюю версию Python 2.7 или 3.4 c сайта [python.org][python.org].
2. Установите пакеты wfastcgi и django с помощью pip.
   
    Для Python 2.7 используйте указанную ниже команду.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Для Python 3.4 используйте указанную ниже команду.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Установка IIS с помощью FastCGI
1. Установите службы IIS с поддержкой FastCGI.  Это может занять несколько минут.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Создание нового приложения Django
1. В папке *C:\inetpub\wwwroot* введите следующую команду для создания проекта Django:
   
   Для Python 2.7 используйте указанную ниже команду.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Для Python 3.4 используйте указанную ниже команду.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Результат выполнения команды New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. Команда **django-admin** создает базовую структуру для веб-сайтов на основе Django:
   
   * **helloworld/manage.py** поможет вам начать и остановить размещение веб-сайта Django.
   * **helloworld/helloworld/settings.py** содержит параметры Django для приложения.
   * **helloworld/helloworld/urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.
3. Создайте файл с именем **views.py** в каталоге *C:\inetpub\wwwroot\helloworld\helloworld*. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Замените содержимое файла urls.py на приведенный ниже код.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="configuring-iis"></a>Настройка IIS
1. Разблокируйте раздел обработчиков в глобальном файле applicationhost.config.  В результате вы сможете использовать обработчик python в файле web.config.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Включите WFastCGI.  В результате приложение будет добавлено в глобальный файл applicationhost.config, в котором имеются ссылки на исполняемый файл интерпретатора Python и сценарий wfastcgi.py.
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. Создайте файл web.config в каталоге *C:\inetpub\wwwroot\helloworld*.  Значение атрибута `scriptProcessor` должно соответствовать выходным данным предыдущего шага.  Дополнительные сведения о настройках wfastcgi приведены на странице [wfastcgi][wfastcgi] в pypi.
   
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
4. Измените расположение веб-сайта IIS по умолчанию, указав папку проекта django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Наконец, загрузите веб-страницу в браузере.

![В окне браузера в Azure отображается страница hello world.][1]

## <a name="shutting-down-your-azure-virtual-machine"></a>Завершение работы виртуальной машины Azure
По завершении работы с данным учебником завершите работу созданной вами виртуальной машины Azure или удалите ее, чтобы освободить ресурсы для других учебников и избежать платы за использование Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi


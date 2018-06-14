---
title: Веб-приложение Django на виртуальной машине Azure для Windows Server | Документация Майкрософт
description: Узнайте, как разместить веб-сайт на основе Django в Azure с помощью виртуальной машины Центра обработки данных Windows Server 2012 R2 и классической модели развертывания.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
ms.locfileid: "24057024"
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Веб-приложение Hello World на Django на виртуальной машине Windows Server

> [!IMPORTANT] 
> В Azure предлагаются две разные модели развертывания для создания ресурсов и работы с ними: [Azure Resource Manager и классическая модель развертывания](../../../resource-manager-deployment-model.md). В этой статье рассматривается классическая модель развертывания. Для большинства новых развертываний рекомендуется использовать модель Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

В этом руководстве описывается размещение веб-сайта на основе Django в Windows Server на виртуальных машинах Azure. Для работы с этим руководством опыт работы с Azure не требуется. По завершении работы с этим руководством у вас будет готовое приложение на базе Django, выполняющееся в облаке.

Ниже перечислено, что вы можете узнать.

* Настраивать виртуальную машину Azure для размещения Django. Хотя в этом руководстве описываются действия для **Windows Server**, эти же шаги вы можете выполнить для виртуальных машин Linux, размещенных в Azure.
* Создайте новое приложение Django в Windows.

В этом руководстве рассматривается создание базового веб-приложения Hello World. Приложение будет размещаться на виртуальной машине Azure.

На снимке экрана ниже показано готовое приложение:

![Окно браузера, отображающее страницу Hello World в Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Создание и настройка виртуальной машины Azure для размещения приложения Django

1. Чтобы создать виртуальную машину Azure с дистрибутивом Windows Server 2012 R2 Datacenter, ознакомьтесь со статьей [Создание виртуальной машины под управлением Windows на портале Azure](tutorial.md).
2. Дайте Azure команду для перенаправления трафика порта 80 из Интернета в порт 80 на виртуальной машине:
   
   1. На портале Azure перейдите на панель мониторинга и выберите только что созданную виртуальную машину.
   2. Щелкните **Конечные точки**, а затем нажмите кнопку **Добавить**.

     ![Добавление конечной точки](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. На странице **Добавить конечную точку** для **имени** введите **HTTP**. Укажите для общедоступного и частного портов TCP значение **80**.

     ![Введите имя и задайте общедоступные и частные порты.](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Последовательно выберите **ОК**.
     
3. На панели мониторинга выберите свою виртуальную машину. Чтобы использовать протокол удаленного рабочего стола (RDP) для удаленного входа на только что созданную виртуальную машину, щелкните **Подключить**.  

> [!IMPORTANT] 
> В следующих инструкциях предполагается, что вход на виртуальную машину выполнен правильно. Также предполагается, что команды выполняются через виртуальную машину, а не локальный компьютер.

## <a id="setup"> </a>Установка Python, Django и WFastCGI
> [!NOTE]
> Для скачивания с помощью Internet Explorer вы должны настроить параметры **конфигурации усиленной безопасности** Internet Explorer. Для этого щелкните **Пуск** > **Администрирование** > **Диспетчер сервера** > **Локальный сервер**. Щелкните **Конфигурация усиленной безопасности Internet Explorer**, а затем выберите **Выкл.**

1. Установите последнюю версию Python 2.7 или Python 3.4 с помощью [python.org][python.org].
2. Установите пакеты wfastcgi и django с помощью pip.
   
    Для Python 2.7 используйте команду ниже:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Для Python 3.4 используйте команду ниже:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Установка IIS с поддержкой FastCGI
* Установите службы IIS с поддержкой FastCGI. Это может занять несколько минут.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Создание нового приложения Django
1. В папке C:\inetpub\wwwroot введите следующую команду для создания проекта Django:
   
   Для Python 2.7 используйте команду ниже:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Для Python 3.4 используйте команду ниже:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Результат выполнения команды New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. Команда `django-admin` создает базовую структуру для веб-сайтов на основе Django:
   
   * `helloworld\manage.py` поможет вам начать и остановить размещение веб-сайта на основе Django.
   * `helloworld\helloworld\settings.py` содержит настройки Django для приложения.
   * `helloworld\helloworld\urls.py` содержит код сопоставления между каждым URL-адресом и его представлением.
3. Создайте файл с именем views.py в каталоге C:\inetpub\wwwroot\helloworld\helloworld. Он содержит представление, которое осуществляет отрисовку страницы "hello world". В редакторе кода введите следующие команды:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Замените содержимое файла urls.py на команды ниже:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Настройка IIS
1. Разблокируйте раздел обработчиков в глобальном файле applicationhost.config.  Это позволит файлу web.config использовать обработчик Python. Добавьте команду ниже:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Активируйте WFastCGI. В результате приложение будет добавлено в глобальный файл applicationhost.config, в котором имеются ссылки на исполняемый файл интерпретатора Python и сценарий wfastcgi.py.
   
    Для Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    Для Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. Создайте файл web.config в каталоге C:\inetpub\wwwroot\helloworld. Значение атрибута `scriptProcessor` должно соответствовать выходным данным предыдущего шага. Дополнительные сведения о параметре wfastcgi см. на странице [wfastcgi 3.0.0][wfastcgi].
   
   Для Python 2.7:
   
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
   
   Для Python 3.4:
   
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
4. Обновите расположение веб-сайта IIS по умолчанию, чтобы указать папку проекта Django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Загрузите веб-страницу в браузере.

![Окно браузера, отображающее страницу Hello World в Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Завершение работы виртуальной машины Azure
По завершении работы с этим руководством мы рекомендуем завершить работу виртуальной машины Azure (созданной для работы с этим руководством) или же удалить ее. Это позволит освободить ресурсы для работы с другими руководствами, а также избежать расходов за использование Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

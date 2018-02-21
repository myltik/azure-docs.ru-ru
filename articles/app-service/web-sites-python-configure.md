---
title: "Настройка Python в веб-приложениях службы приложений Azure"
description: "В этом учебнике описываются возможности создания и настройки в веб-приложениях службы приложений Azure базового приложения Python, совместимого с интерфейсом шлюза веб-сервера (WSGI)."
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: 86e19d5bb942937779665eb60d9dc0654c16747d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Настройка Python в веб-приложениях службы приложений Azure
В этом учебнике описываются возможности создания и настройки в [веб-приложениях службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714)базового приложения Python, совместимого с интерфейсом шлюза веб-сервера (WSGI).

Здесь также описываются дополнительные функции развертывания Git, например установка виртуальной среды или пакета с использованием файла requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django и Flask
В Azure Marketplace содержатся шаблоны для платформ Bottle, Django и Flask. Если вы разрабатываете первое веб-приложение в службе приложений Azure, это можно быстро сделать на портале Azure.

* [Создание веб-приложений на основе Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Создание веб-приложений на основе Django](https://portal.azure.com/#create/PTVS.Django)
* [Создание веб-приложений на основе Flask](https://portal.azure.com/#create/PTVS.Flask)

## <a name="web-app-creation-on-azure-portal"></a>Создание веб-приложения на портале Azure
В этом руководстве предполагается, что у вас есть подписка Azure и доступ к порталу Azure.

Если у вас нет веб-приложения, его можно создать на [портале Azure](https://portal.azure.com).  В верхнем левом углу нажмите кнопку "Создать", а затем щелкните **Интернет + мобильные устройства** > **Веб-приложение**.

## <a name="git-publishing"></a>Публикация с использованием Git
Настройте публикацию Git для вновь созданного веб-приложения, следуя инструкциям из статьи [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md). В этом руководстве для создания, изменения и публикации веб-приложения Python в службе приложений Azure мы будем использовать Git.

После настройки публикации с помощью Git будет создан репозиторий Git, который затем будет связан с вашим веб-приложением. Появится URL-адрес репозитория, который можно будет использовать для передачи данных в облако из локальной среды разработки. Для публикации приложений с помощью Git убедитесь, что клиент Git также установлен. Затем следуйте предоставленным указаниям для передачи содержимого веб-приложения в службу приложений Azure.

## <a name="application-overview"></a>Обзор приложений
В дальнейших разделах будут созданы следующие файлы. Их нужно сохранить в корневую папку репозитория Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Обработчик WSGI
WSGI — это стандарт Python, описываемый в [PEP 3333](http://www.python.org/dev/peps/pep-3333/) и определяющий интерфейс между веб-сервером и Python. Он предоставляет стандартный интерфейс для написания различных веб-приложений и платформ с использованием Python. Сегодня популярные веб-платформы Python используют WSGI. Веб-приложения службы приложений Azure предоставляют поддержку любых таких платформ. Кроме того, опытные пользователи даже могут создавать собственные платформы, если пользовательский обработчик следует правилам спецификации WSGI.

Ниже приведен пример кода `app.py`, который определяет настраиваемый обработчик:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Это приложение можно запустить локально с помощью `python app.py`, а затем перейти в браузере по адресу `http://localhost:5555`.

## <a name="virtual-environment"></a>Виртуальная среда
Приведенный выше пример приложения не требует каких-либо внешних пакетов, но для вашего приложения они, вероятно, потребуются.

Для облегчения управления зависимостями внешних пакетов, развертывание Azure Git поддерживает создание виртуальных сред.

Если платформа Azure обнаруживает в корневой папке репозитория файл requirements.txt, она автоматически создает виртуальную среду с именем `env`. Это происходит только при первом развертывании или после изменения выбранной среды выполнения Python.

Вы, возможно, захотите создать локальную виртуальную среду для разработки и не включать ее в репозиторий Git.

## <a name="package-management"></a>Управление пакетами
Пакеты, перечисленные в файле requirements.txt, устанавливаются в виртуальной среде автоматически с помощью pip. Это происходит при каждом развертывании, но если пакет уже установлен, pip пропустит его установку.

Пример `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Версия Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Пример `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Чтобы указать, как сервер должен обрабатывать запросы, нужно создать файл web.config.

Если в репозитории есть файл web.x.y.config, в котором x.y совпадает с выбранной средой выполнения Python, Azure автоматически скопирует соответствующий файл как web.config.

Следующие примеры файла web.config зависят от сценария прокси в виртуальной среде, который описан в следующем разделе.  Они работают с обработчиком WSGI, который использовался в приведенном выше примере `app.py` .

Пример файла `web.config` для Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Пример файла `web.config` для Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Для повышения производительности статические файлы обрабатываются непосредственно веб-сервером без использования кода Python.

В предыдущих примерах расположение статических файлов на диске должно совпадать с расположением, указанным в URL-адресе. Это означает, что запрос `http://pythonapp.azurewebsites.net/static/site.css` будет использовать файл на диске в расположении `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` — здесь указывается обработчик WSGI. В предыдущих примерах обработчик указывается в файле `app.wsgi_app`, так как обработчик является функцией с именем `wsgi_app` в файле `app.py` в корневой папке.

`PYTHONPATH` можно настраивать, но если вы устанавливаете все зависимости в виртуальной среде, указывая их в файле requirements.txt, путь менять не нужно.

## <a name="virtual-environment-proxy"></a>Прокси-сервер с поддержкой виртуальной среды
Следующий сценарий используется для извлечения обработчика WSGI, активации виртуальной среды и ведения журнала ошибок. Он разработан как универсальный и применяется без изменений.

Содержимое `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Настройка развертывания Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Устранение неполадок — установка пакета
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Устранение неполадок — виртуальная среда
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию можно найти в [Центре разработчика Python](/develop/python/).

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

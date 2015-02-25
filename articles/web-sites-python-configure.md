<properties 
	pageTitle="Настройка Python для веб-сайтов Azure" 
	description="В этом учебнике описываются возможности создания и настройки на веб-сайтах Azure базового приложения Python, совместимого с интерфейсом шлюза веб-сервера (WSGI)." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Настройка Python для веб-сайтов Azure

В этом учебнике описываются возможности создания и настройки на веб-сайтах Azure базового приложения Python, совместимого с интерфейсом шлюза веб-сервера WSGI.

Здесь также описываются дополнительные функции развертывания Git, например установка виртуальной среды или пакета с использованием файла requirements.txt.

+ [Bottle, Django или Flask?](#bottle-django-flask)
+ [Создание веб-сайта на портале](#website-creation-on-portal)
+ [Публикация с использованием Git](#git-publishing)
+ [Обзор приложений](#application-overview)
+ [Обработчик WSGI](#wsgi-handler)
+ [Виртуальная среда](#next-steps)
+ [Управление пакетами](#next-steps)
+ [Версия Python](#next-steps)
+ [Прокси-сервер с поддержкой виртуальной среды](#virtual-environment-proxy)
+ [Настройка развертывания Git](#customize-git-deployment)
+ [Устранение неполадок - развертывание](#troubleshooting-deployment)
+ [Устранение неполадок - установка пакета](#troubleshooting-package-installation)
+ [Устранение неполадок - виртуальная среда](#troubleshooting-virtual-environment)


<h2><a name="bottle-django-flask"></a>Bottle, Django или Flask?</h2>

В коллекции Azure содержатся шаблоны для платформ Bottle, Django и Flask.  Если вы разрабатываете веб-сайт Azure впервые, или не знакомы с Git, советуем следовать одному из следующих учебников, которые содержат пошаговые указания по созданию рабочего приложения из коллекции с помощью развертывания Git в Windows или Mac:

- [Создание веб-сайтов с помощью Bottle][]
- [Создание веб-сайтов с помощью Django][]
- [Создание веб-сайтов с помощью Flask][]


<h2><a name="website-creation-on-portal"></a>Создание веб-сайта на портале</h2>

В этом учебнике предполагается наличие существующей подписки Azure и доступа к порталу управления Azure.

Если нет существующего веб-сайта, можно создать его на портале управления Azure.  Нажмите кнопку "СОЗДАТЬ" в левом нижнем углу. Откроется окно. Последовательно выберите СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ, ВЕБ-САЙТ и БЫСТРО СОЗДАТЬ.

![](./media/web-sites-python-configure/configure-python-create-website.png)


<h2><a name="git-publishing"></a>Публикация с использованием Git</h2>

Используйте вкладки "БЫСТРЫЙ ЗАПУСК" или "ПАНЕЛЬ МОНИТОРИНГА" для вновь созданного веб-сайта, чтобы настроить публикацию с помощью Git.  В этом учебнике Git используется для создания, публикации веб-сайта Python и управления им на веб-сайтах Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

После настройки публикации с помощью Git будет создан репозиторий Git и связан с вашим веб-сайтом.  Появится URL-адрес репозитория, который с этого момента может использоваться для передачи данных в облако из локальной среды разработки. Для публикации приложений с помощью Git убедитесь, что клиент Git также установлен, и следуйте предоставленным инструкциям для передачи контента веб-сайта содержимого на веб-сайты Azure.


<h2><a name="application-overview"></a>Обзор приложений</h2>

В дальнейших разделах будут созданы следующие файлы.  Их нужно сохранить в корневую папку репозитория Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


<h2><a name="wsgi-handler"></a>Обработчик WSGI</h2>

WSGI - это стандарт Python, описанный в документе [PEP 3333](http://www.python.org/dev/peps/pep-3333/) и определяющий интерфейс между веб-сервером и Python. Он предоставляет стандартный интерфейс для написания различных веб-приложений и платформ с использованием Python.  Сегодня популярные веб-платформы Python используют WSGI.  Веб-сайты Azure предоставляют поддержку таких платформ. Кроме того, опытные пользователи даже могут создавать собственные платформы, если пользовательский обработчик следует правилам спецификации WSGI.

Ниже приведен пример кода `app.py`, который определяет настраиваемый обработчик.

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

Запустите это приложение локально с помощью  `python app.py`, затем перейдите по адресу `http://localhost:5555`.


<h2><a name="virtual-environment"></a>Виртуальная среда</h2>

Хотя приведенное выше приложение не требует каких-либо внешних пакетов, они, вероятно, потребуются.

Для облегчения управления зависимостями внешних пакетов, развертывание Azure Git поддерживает создание виртуальных сред.

Если платформа Azure обнаруживает в корневой папке репозитория файл requirements.txt, она автоматически создает виртуальную среду с именем `env`.  Это происходит только при первом развертывании или после изменения выбранной среды выполнения Python.

Возможно, потребуется создать виртуальную среду для локальной разработки, но не нужно включать ее в репозиторий Git.


<h2><a name="package-management"></a>Управление пакетами</h2>

Пакеты, перечисленные в файле requirements.txt, устанавливаются автоматически в виртуальной среде с помощью pip.  Это происходит при каждом развертывании, но если пакет уже установлен, pip пропустит установку.

Пример `requirements.txt`:

    azure==0.8.4


<h2><a name="python-version"></a>Версия Python</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

Пример `runtime.txt`:

    python-2.7


<h2><a name="web-config"></a>Web.config</h2>

Чтобы указать, как сервер должен обрабатывать запросы, нужно будет создать файл web.config.

Обратите внимание, что если в репозитории есть файл web.x.y.config, в котором x.y совпадает с выбранной средой выполнения Python, а затем Azure автоматически скопирует соответствующий файл с именем web.config.

Следующие примеры файла web.config зависят от сценария прокси в виртуальной среде, который описан в следующем разделе.  Они работают с обработчиком WSGI, который использовался в приведенном выше примере `app.py`.

Пример `web.config` для Python 2.7:

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
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
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
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Пример `web.config` для Python 3.4:

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
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
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
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Для повышения производительности статические файлы будут непосредственно обрабатываться веб-сервером без использования кода Python.

В приведенных выше примерах расположение статических файлов на диске должно совпадать с расположением в поле URL-адреса.  Это означает, что запрос на `http://pythonapp.azurewebsites.net/static/site.css` будет записан в файл на диске по пути  `\static\site.css`.

Настройте правило `Static Files`, чтобы передавать файлы из расположения на диске, которое отличается от расположения в поле URL-адреса.  В следующем определении правила запрос для `http://pythonapp.azurewebsites.net/static/site.css` будет записан в файл на диске по пути  `\FlaskWebProject\static\site.css`, вместо  `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` - здесь указывается обработчик WSGI.  А в приведенных выше примерах - это  `app.wsgi_app`, так как обработчик является функцией с именем `wsgi_app` в `app.py` в корневой папке.

`PYTHONPATH` можно настраивать, но при установке всех зависимостей в виртуальной среде, указанных как файл requirements.txt, менять его не нужно.


<h2><a name="virtual-environment-proxy"></a>Прокси-сервер с поддержкой виртуальной среды</h2>

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
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
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

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

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


<h2><a name="customize-git-deployment"></a>Настройка развертывания Git</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-deployment.md)]


<h2><a name="troubleshooting-deployment"></a>Устранение неполадок - развертывание</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Устранение неполадок - установка пакета</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Устранение неполадок - виртуальная среда</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Создание веб-сайтов с помощью Bottle]: ../web-sites-python-create-deploy-bottle-app
[Создание веб-сайтов с помощью Django]: ../web-sites-python-create-deploy-django-app
[Создание веб-сайтов с помощью Flask]: ../web-sites-python-create-deploy-flask-app


<!--HONumber=42-->

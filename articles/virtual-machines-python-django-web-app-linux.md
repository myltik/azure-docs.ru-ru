<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based website on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Веб-приложение Hello World на Django (Mac и Linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/ru-ru/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

В этом учебнике описывается, как разместить веб-сайт на основе Django в Windows
Azure с помощью виртуальной машины Linux. В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

-   Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Linux**, эти сведения актуальны для виртуальных машин под управлением Windows Server, размещенных в Windows Azure.
-   Создайте новое приложение Django в Linux.

Руководствуясь этим учебником, вы создадите простое веб-приложение
HelloWorld. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1.  Следуйте приведенным [здесь][здесь] указаниям, чтобы создать виртуальную машину с дистрибутивом *Ubuntu Server 14.04*.

 **Примечание.** Необходимо *только* создать виртуальную машину. Остановитесь у раздела с заголовком *Как войти в систему на виртуальной машине после ее создания*.

1.  Дайте Azure команду для перенаправления трафика порта **80** из Интернета на порт **80** на виртуальной машине:

    -   Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
    -   Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
        ![добавление конечной точки][добавление конечной точки]
    -   Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*.
        ![port80][port80]

## <span id="setup"></span> </a>Настройка среды разработки

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python][руководство по установке Python].

Виртуальная машина Linux Ubuntu уже поставляется с предварительно установленной программой Python 2.7, но в ней отсутствует Apache или django. Выполните следующие действия, чтобы подключиться к своей виртуальной машине и установить Apache и django.

1.  Запустите новое окно **Терминал**.

2.  Введите следующую команду для подключения к виртуальной машине Azure.

        $ ssh yourusername@yourVmUrl

3.  Введите следующие команды для установки Django:

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Введите следующую команду для установки Apache с mod-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi

## Создание нового приложения Django

1.  Откройте окно **Терминал**, использовавшееся в предыдущем разделе для входа на виртуальную машину по протоколу SSH.

2.  Введите следующие команды для создания нового проекта Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Сценарий **Django admin.py** создает базовую структуру для веб-сайтов на основе Django:

    -   **helloworld/manage.py** помогает вам начать и остановить размещение веб-сайта Django
    -   **helloworld/helloworld/settings.py** содержит параметры Django для приложения.
    -   **helloworld/helloworld/urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

3.  Создайте новый файл с именем **views.py** в каталоге **/var/www/helloworld/helloworld** (как элемент одного уровня с **urls.py**). Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

4.  Замените содержимое файла **urls.py** на код, приведенный ниже:

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

## Настройка Apache

1.  Создайте файл конфигурации виртуального узла Apache **/etc/apache2/sites-available/helloworld.conf**. Установите для содержимого следующее значение и обязательно замените *yourVmUrl* фактическим URL-адресом машины, которую вы используете (например, *pyubuntu.cloudapp.net*).

        <VirtualHost *:80>
        ServerName yourVmUrl
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

2.  Включите сайт, выполнив следующую команду:

        $ sudo a2ensite helloworld

3.  Перезапустите Apache с помощью следующей команды:

        $ sudo service apache2 reload

4.  Наконец, загрузите веб-страницу в браузере.

    ![В окне браузера в Azure отображается страница hello world.][В окне браузера в Azure отображается страница hello world.]

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

  [В окне браузера в Azure отображается страница hello world.]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png
  [здесь]: /ru-ru/manage/linux/tutorials/virtual-machine-from-gallery/
  [добавление конечной точки]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png
  [port80]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png
  [руководство по установке Python]: ../python-how-to-install/

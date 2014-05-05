<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Веб-сайт с Django" pageTitle="Веб-приложение на Python с Django на Mac — учебник по Azure" metaKeywords="" description="В этом руководстве описывается, как разместить веб-сайт на основе Django в Azure с помощью виртуальной машины Linux." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Веб-приложение Hello World на Django (mac-linux)" authors="" solutions="" manager="" editor="" />





# Веб-приложение Hello World на Django (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/ru-ru/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

В этом руководстве описывается, как разместить веб-сайт на основе Django в Azure с помощью виртуальной машины Linux. В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

* Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Linux**, эти сведения актуальны для виртуальных машин под управлением Windows Server, размещенных в Azure. 
* Создайте новое приложение Django в Linux.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello
World. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1. Следуйте приведенным [здесь][portal-vm] инструкциям, чтобы создать виртуальную машину с дистрибутивом *Ubuntu Server 12.04*.

  **Примечание.** Необходимо *только* создать виртуальную машину. Остановитесь у раздела с заголовком *Как войти в систему на виртуальной машине после ее создания*.

1. Дайте Azure команду для перенаправления трафика порта **80** из Интернета на порт **80** на виртуальной машине:
	* Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
	* Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
	![добавление конечной точки](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*.
	![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>Настройка среды разработки

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](../python-how-to-install/).

Виртуальная машина Linux Ubuntu уже поставляется с предварительно установленной программой Python 2.7, но в ней отсутствует Apache или django.  Выполните следующие действия, чтобы подключиться к своей виртуальной машине и установить Apache и django.

1.  Запустите новое окно **Терминал**.
    
1.  Введите следующую команду для подключения к виртуальной машине Azure.

		$ ssh yourusername@yourVmUrl

1.  Введите следующие команды для установки django:

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Введите следующую команду для установки Apache с mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Создание нового приложения Django

1.  Откройте окно **Терминал**, использовавшееся в предыдущем разделе для входа на виртуальную машину по протоколу SSH.
    
1.  Введите следующие команды для создания нового проекта Django:

    ![Результат выполнения команды django-admin](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    Скрипт **Django admin.py** создает базовую структуру для веб-сайтов на базе Django:
    -   **manage.py** помогает вам начать и остановить размещение веб-сайта Django
    -   **helloworld\settings.py** содержит параметры Django для приложения.
    -   **helloworld\urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

1.  Создайте новый файл с именем **views.py** в подкаталоге *helloworld* каталога *django\helloworld* как элемент одного уровня с **urls.py**. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:
		
		from django.http import HttpResponse
		def hello(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Замените содержимое файла **urls.py** на код, приведенный ниже:

		from django.conf.urls.defaults import patterns, include, url
		from helloworld.views import hello
		urlpatterns = patterns('',
			(r'^$',hello),
		)


## Развертывание и запуск веб-сайта

1.  Измените файл конфигурации apache **/etc/apache2/httpd.conf** и добавьте следующий код, заменив *username* на имя пользователя, указанное при создании виртуальной машины:

		WSGIScriptAlias / /home/*username*/django/helloworld/helloworld/wsgi.py
		WSGIPythonPath /home/*username*/django/helloworld

		<Directory /home/*username*/django/helloworld/helloworld>
		<Files wsgi.py>
		Order deny,allow
		Allow from all
		</Files>
		</Directory>

1.  Перезапустите apache с помощью следующей команды:

        $ sudo apachectl restart

1.  Наконец, загрузите веб-страницу в браузере.

	![В окне браузера в Azure отображается страница hello world.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.


[portal-vm]: /ru-ru/manage/linux/tutorials/virtual-machine-from-gallery/


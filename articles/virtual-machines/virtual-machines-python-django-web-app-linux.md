<properties 
	pageTitle="Веб-приложение Python на Django на платформе Mac — учебник по Azure" 
	description="В этом учебнике описывается, как разместить веб-сайт на основе Django в Azure с помощью виртуальной машины Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>





# Веб-приложение Hello World на Django (Mac и Linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

В этом учебнике описывается, как разместить веб-сайт на основе Django в Microsoft Azure с помощью виртуальной машины Linux. В данном учебнике предполагается, что у вас нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Django, выполняемое в облаке.

Вы научитесь:

* Настройте виртуальную машину Azure для размещения Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в **Linux**, эти сведения актуальны для виртуальных машин под управлением Windows Server, размещенных в Windows Azure. 
* Создайте новое приложение Django в Linux.

Руководствуясь этим учебником, вы создадите простое веб-приложение Hello World. Приложение будет размещаться в виртуальной машине Azure.

Снимок экрана завершенного приложения приведен ниже:

![В окне браузера в Azure отображается страница hello world.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Создание и настройка виртуальной машины Azure для размещения Django

1. Следуйте приведенным [здесь][portal-vm] указаниям, чтобы создать виртуальную машину с дистрибутивом *Ubuntu Server 14.04 LTS*.

  **Примечание.** Вам необходимо *только* создать виртуальную машину. Остановитесь у раздела с заголовком *Как войти в систему на виртуальной машине после ее создания*.

1. Дайте Azure команду для перенаправления трафика порта **80** из Интернета на порт **80** на виртуальной машине:
	* Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
	* В нижней части страницы нажмите кнопку *ДОБАВИТЬ*. ![добавление конечной точки](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Откройте *ОБЩИЙ ПОРТ 80* протокола *TCP* как *ЧАСТНЫЙ ПОРТ 80*. ![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"></a>Настройка среды разработки

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](../python-how-to-install.md).

Виртуальная машина Linux Ubuntu уже поставляется с предварительно установленной программой Python 2.7, но в ней отсутствует Apache или django. Выполните следующие действия, чтобы подключиться к своей виртуальной машине и установить Apache и django.

1.  Запустите новое окно **Терминал**.
    
1.  Введите следующую команду для подключения к виртуальной машине Azure.

		$ ssh yourusername@yourVmUrl

1.  Введите следующие команды для установки Django:

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Введите следующую команду для установки Apache с mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Создание нового приложения Django

1.  Откройте окно **Терминал**, использовавшееся в предыдущем разделе для входа на виртуальную машину по протоколу SSH.
    
1.  Введите следующие команды для создания нового проекта Django:

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    Сценарий **django admin.py** создает базовую структуру для веб-сайтов на базе Django: - **helloworld/manage.py** помогает вам начать и остановить размещение вашего веб-сайта на базе Django; - **helloworld/helloworld/settings.py** содержит параметры Django для приложения; - **helloworld/helloworld/urls.py** содержит код сопоставления между каждым URL-адресом и его представлением.

1.  Создайте новый файл с именем **views.py** в каталоге **/var/www/helloworld/helloworld**. Он будет содержать представление, которое осуществляет отрисовку страницы "hello world". Запустите редактор и введите следующую команду:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Замените содержимое файла **urls.py** на код, приведенный ниже:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Настройка Apache

1.  Создайте файл конфигурации виртуального узла Apache **/etc/apache2/sites-available/helloworld.conf**. Установите для содержимого следующее значение и обязательно замените *yourVmUrl* фактическим URL-адресом машины, которую вы используете (например, *pyubuntu.cloudapp.net*).

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Включите сайт, выполнив следующую команду:

        $ sudo a2ensite helloworld

1.  Перезапустите Apache с помощью следующей команды:

        $ sudo service apache2 reload

1.  Наконец, загрузите веб-страницу в браузере.

	![В окне браузера в Azure отображается страница hello world.](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/
 

<!---HONumber=July15_HO1-->
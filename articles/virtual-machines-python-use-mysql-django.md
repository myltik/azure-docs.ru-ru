<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Веб-сайт на Django + MySQL" pageTitle="Веб-приложения на Python с Django и MySQL — учебник по Azure" metaKeywords="Azure django веб-приложение Azure Django MySQL, Azure django Python" description="Учебник по использованию MySQL с Django на виртуальной машине Azure. Примеры кода написаны на Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World — выпуск MySQL Windows" authors=""  solutions="" writer="" manager="" editor=""  />







# Django Hello World — выпуск MySQL Windows #
  
В этом учебном курсе описывается использование MySQL в сочетании с Django на одной виртуальной машине Azure. Это руководство разработано для читателей, имеющих определенный опыт использования платформы Azure и Django. Введение в Azure и Django см. в разделе [Django Hello World] [djangohelloworld]. Руководство также предполагает наличие определенных знаний MySQL. Общие сведения о MySQL см. в разделе [Веб-сайт с MySQL][mysqldoc].

Из этого учебника вы узнаете следующее:

* Настройка виртуальной машины Azure для размещения MySQL и Django. Хотя в учебном курсе объясняется, как выполнить эту задачу в Windows Server 2008 R2, эти сведения актуальны для виртуальных машин под управлением Linux, размещенных в Azure.
* Установка [драйвера MySQL] [mysqlpy] для Python.
* Настройка использования базы данных MySQL в приложении Django.
* Использование MySQL непосредственно в Python.
* Размещение и запуск приложения MySQL Django.

Вам предстоит расширить пример приложения [Django Hello World] [djangohelloworld] с использованием базы данных MySQL, размещенной на виртуальной машине Azure, чтобы найти интересную замену для слова *World*. Замена, в свою очередь, будет определяться через приложение *счетчика* на платформе Django с поддержкой MySQL. Как и пример Hello World, это приложение Django также будет размещаться на виртуальной машине Azure.

Файлы проекта для этого учебного курса будут храниться в **C:\django\helloworld**, завершенное приложение будет выглядеть примерно следующим образом:

![][0]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Настройка виртуальной машины для размещения MySQL и Django
1. Следуйте приведенным [здесь ][preview-portal-vm] инструкциям, чтобы создать виртуальную машину Azure для дистрибутива *Windows Server 2008 R2*.

1. Откройте TCP-порт для транзакций MySQL на виртуальной машине:
 * Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
 * Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
 * Добавьте конечную точку с NAME = **mysql**, PROTOCOL = **TCP**, PUBLIC PORT = **3306**, PRIVATE PORT = **3306**.

1. Добавьте еще одну конечную точку с NAME = **web**, PROTOCOL = **TCP**, PUBLIC PORT = **80**, PRIVATE PORT = **80**.  В результате внешние интернет-запросы будут перенаправляться на порт, в котором выполняется Django, а именно *80*.

1. С помощью *Удаленного рабочего стола* Windows удаленно войдите в созданную виртуальную машину Azure.

1. Откройте TCP-порт **80** на виртуальной машине:
 * В меню **Пуск** выберите **Администрирование** и **Брандмауэр Windows в режиме повышенной безопасности**. 
 * На левой панели выберите **Правила для входящих подключений**.  В панели **Действия** справа выберите **Создать правило...**.
 * В окне **Мастер создания правила для нового входящего подключения** выберите **Порт** и нажмите кнопку **Далее**.
 * Выберите **TCP** и **Определенные локальные порты**.  Укажите порт "80" (порт прослушивания Django) и нажмите **Далее**.
 * Выберите **Разрешить подключение** и нажмите **Далее**.
 * Нажмите **Далее** еще раз.
 * Введите имя правила, например "DjangoPort", и нажмите "Готово".

1. Установите последнюю версию [MySQL Community Server] [mysqlcommunity] для Windows на виртуальной машине:  

	**Примечание**. Рекомендуется установить базу данных в разделе данных, отличном от раздела операционной системы. 

 * Щелкните ссылку *Windows (x 86, 64-разрядная), установщик MSI* [здесь] [mysqlcommunity] и загрузите соответствующий установщик MSI с ближайшего к вам зеркала. Полезные советы:
     * Выберите вариант установки *Полная*.
     * Выберите в мастере конфигурации раздел *Подробные настройки*.
     * **Убедитесь, что включена сеть TCP/IP в порте с номером 3306, и добавьте в брандмауэр исключение для этого порта.**
     * Задайте пароль для пользователя root и разрешите доступ с правами root с удаленных компьютеров.
 * Установите примерную базу данных ["мир"] [mysqlworld] (версия MyISAM):
     * Загрузите [этот] [mysqlworlddl] ZIP-файл на виртуальной машине Azure.
     * **Распакуйте его в папку *C:\Users\Administrator\Desktop\world.sql*.**
1. После установки MySQL, щелкните меню Windows *Пуск* и запустите только что установленный *клиент командной строки MySQL 5.5*.  Введите следующие команды:

		CREATE DATABASE world;
		USE world;
		SOURCE C:\Users\Administrator\Desktop\world.sql
		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		SELECT name from country LIMIT 1;

  Вы должны увидеть ответ, аналогичный приведенному ниже: 

  ![][2]

1. Перед началом разработки своего приложения Django, разумеется, необходимо установить Python+Django на виртуальной машине. Это можно сделать с помощью [Установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx). После установки этого установщика с его помощью выполните поиск "Django" и установите продукт Django (Python).    

  **Примечание.** Для работы по этому учебному курсу достаточно просто установить продукт *Django* из WebPI.  Для наших целей **не** требуются *Средства Python для Visual Studio* или даже пакет SDK для Python в Azure.

1. Установите пакет клиента MySQL Python. Его можно установить непосредственно [по этой ссылке] [mysqlpydl]. После завершения выполните следующую команду, чтобы проверить установку:

  ![][1]


## Расширение Django приложения Hello World
1. Следуйте инструкциям, содержащимся в учебном курсе [Django Hello World], [djangohelloworld] чтобы создать стандартное веб-приложение "Hello, World!" на платформе Django.

1. Откройте **C:\django\helloworld\helloworld\settings.py** в предпочитаемом текстовом редакторе.  Измените глобальный словарь **DATABASES** следующим образом:

		DATABASES = {
		    'default': {
			    'ENGINE': 'django.db.backends.mysql',
			    'NAME': 'djangoazure',               
			    'USER': 'testazureuser',  
			    'PASSWORD': 'testazure',
			    'HOST': '127.0.0.1', 
			    'PORT': '3306',
			    },
		    'world': {
			    'ENGINE': 'django.db.backends.mysql',
			    'NAME': 'world',               
			    'USER': 'testazureuser',  
			    'PASSWORD': 'testazure',
			    'HOST': '127.0.0.1', 
			    'PORT': '3306',
			    }
			}

  Как вы видите, мы только что задали для Django инструкции по месту поиска в базе данных MySQL.
 
  **Примечание.** Вы **должны** изменить ключ *HOST* в соответствии с **постоянным** IP-адресом вашей виртуальной машины Azure (MySQL). На этом этапе ключ *HOST* должен соответствовать значению, выводимому командой Windows *ipconfig*. 

  После изменения ключа *HOST* в соответствии с IP-адресом виртуальной машины MySQL сохраните этот файл и закройте его.

1. Теперь, когда мы установили связь с нашей базой данных *djangoazure*, давайте сделаем что-нибудь полезное! С этой целью мы создадим модель для стандартного приложения *Счетчик*.  Чтобы дать Django указание на создание такого приложения, выполните следующие команды:

		cd C:\django\helloworld
		C:\Python27\python.exe manage.py startapp counter

  Если Django не выводит данных после исполнения последней из перечисленных команд, создание выполнено успешно.

1. Добавьте следующий текст в **C:\django\helloworld\counter\models.py**:

		class Counter(models.Model):
		    count = models.IntegerField()
		    def __unicode__(self):
		        return u'%s' % (self.count)

  Все, что здесь выполнено — определен подкласс для класса *модели* Django с именем *Counter* и с одним целочисленным полем, *count*. Эта стандартная модель счетчика будет записывать число обращений к нашему приложению Django. 

1. Затем мы уведомим Django о существовании класса *Counter*:
 * Измените **C:\django\helloworld\helloworld\settings.py** снова. Добавьте *"counter"* в кортеж *INSTALLED_APPS*.
 * Из командной строки выполните следующую команду:

			cd C:\django\helloworld
			C:\Python27\python manage.py sql counter
			C:\Python27\python manage.py syncdb

    Эти команды сохраняют модель *Counter* в рабочей базе данных Django и выводят данные, аналогичные приведенным ниже:

		C:\django\helloworld> C:\Python27\python manage.py sql counter
		BEGIN;
		CREATE TABLE `counter_counter` (
    		`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
		    `count` integer NOT NULL
		)
		;
		COMMIT;
		
		C:\django\helloworld> C:\Python27\python manage.py syncdb
		Creating tables ...
		Creating table auth_permission
		Creating table auth_group_permissions
		Creating table auth_group
		Creating table auth_user_user_permissions
		Creating table auth_user_groups
		Creating table auth_user
		Creating table django_content_type
		Creating table django_session
		Creating table django_site
		Creating table counter_counter
		
		You just installed Django's auth system, which means you don't have any superusers defined.
		Would you like to create one now? (yes/no): no
		Installing custom SQL ...
		Installing indexes ...
		Installed 0 object(s) from 0 fixture(s)

1. Замените содержимое **C:\django\helloworld\helloworld\views.py**. Приведенная ниже новая реализация функции *hello* использует нашу модель *Counter* в сочетании с отдельной образцовой базой данных *world*, установленной ранее, чтобы создать приемлемую замену для строки "*World*":

		from django.http import HttpResponse
		import django.db
		from counter.models import Counter
		
		def getCountry(intId):
		    #Connect to the MySQL sample database 'world'
		    cur = django.db.connections['world'].cursor()
		    #Execute a trivial SQL query which returns the name of 
		    #all countries contained in 'world'
		    cur.execute("SELECT name from country")
		    tmp = cur.fetchall()
		    #Clean-up after ourselves
		    cur.close()
		    if intId >= len(tmp):
		        return "countries exhausted"
		    return tmp[intId][0]
		
		def hello(request):
		    if len(Counter.objects.all())==0:
		        #when the database corresponding to 'helloworld.counter' is 
		        #initially empty...
		        c = Counter(count=0)
		    else:
		        c = Counter.objects.all()[0]
		        c.count += 1
		    c.save()	   
		    world = getCountry(int(c.count))
		    return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")


## Развертывание и запуск веб-сайта Django

Примечание. Ниже показано, как запустить Django в тестовой среде. Чтобы запустить эту платформу в производственной среде, следуйте инструкциям раздела "Настройка IIS с FastCGI" в "Учебном курсе Django Hello World". Использование клиента брандмауэра Windows для открытия порта 80 для интернет-трафика на виртуальной машине под управлением Windows Server 2K8 R2 утрачивает необходимость при использовании FastCGI.



1.  Переключитесь обратно в окно Windows PowerShell и введите следующие команды для публичного развертывания веб-сайта Django:

		PS C:\django\helloworld> $ipPort = 
		PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
		PS C:\django\helloworld> $ipPort += ":80"
		PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    Параметр **runserver** указывает Django, что наш веб-сайт *helloworld* должен выполняться в TCP-порте *80*. Результаты данной команды должны выглядеть следующим образом:

		PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
		Validating models...
		
		0 errors found
		Django version 1.4, using settings 'helloworld.settings'
		Development server is running at http://123.34.56.78:80
		Quit the server with CTRL-BREAK.
 
1. В локальном веб-обозревателе откройте **http://*yourVmName*.cloudapp.net** (где *yourVmName* — имя, которое было указано при создании виртуальной машины). Должно быть выведено значение "Hello ... !", как показано на приведенном ниже снимке экрана. Это означает, что Django выполняется на виртуальной машине и работает правильно.

    ![][5]

  Обновите веб-обозреватель несколько раз, и вы увидите, что сообщение изменится с *"Hello **&lt;country abc&gt;**"* на *"Hello **&lt;другая страна&gt;**"*.

1.  Чтобы остановить размещение веб-сайта в Django, просто перейдите в окно PowerShell и нажмите клавиши **CTRL-C**.


## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

[0]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
[1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
[2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
[5]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png

[djangohelloworld]: http://windowsazure.com/ru-ru/documentation/articles/virtual-machines-python-django-web-app-windows-server

[mysqldoc]: http://dev.mysql.com/doc/
[mysqlpy]: http://pypi.python.org/pypi/MySQL-python/1.2.3

[mysqlpydl]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
[mysqlcommunity]:http://dev.mysql.com/downloads/mysql/

[mysqlworld]:http://dev.mysql.com/doc/index-other.html
[mysqlworlddl]:http://downloads.mysql.com/docs/world.sql.zip


[preview-portal-vm]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/

[Состояние команды Stop-AzureService]: ../Media/django-helloworld-ps-stop.png
[Состояние команды Remove-AzureService]: ../Media/django-helloworld-ps-remove.png

[Руководство по установке]: http://windowsazure.com/ru-ru/documentation/articles/python-how-to-install
 


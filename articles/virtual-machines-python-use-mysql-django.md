<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Django Hello World — выпуск MySQL Windows

В этом учебнике описывается использование MySQL в сочетании с Django на одной виртуальной машине Azure. В этом руководстве предполагается, что вы уже имеете определенный опыт использования платформы Azure и Django. Введение в Azure и Django см. в разделе [Django Hello World][Django Hello World]. Руководство также предполагает наличие определенных знаний MySQL. Общие сведения о MySQL см. в разделе [Веб-сайт с MySQL][Веб-сайт с MySQL].

Из этого учебника вы узнаете следующее:

-   Настройка виртуальной машины Azure для размещения MySQL и Django. Хотя в учебнике объясняется, как выполнить эту задачу в Windows Server 2008 R2, эта информация актуальна для виртуальных машин под управлением Linux, размещенных в Azure.
-   Установка [драйвера MySQL][драйвера MySQL] для Python.
-   Настройка использования базы данных MySQL в приложении Django.
-   Использование MySQL непосредственно в Python.
-   Размещение и запуск приложения MySQL Django.

Вам предстоит расширить пример приложения [Django Hello World][Django Hello World] с использованием базы данных MySQL, размещенной на виртуальной машине Azure, чтобы найти интересную замену для слова *World*. Замена, в свою очередь, будет определяться через приложение *счетчика* на платформе Django с поддержкой MySQL. Как и пример Hello World, это приложение Django также будет размещаться на виртуальной машине Azure.

Файлы проекта для этого учебника будут храниться в папке **C:\\django\\helloworld**, а завершенное приложение будет выглядеть примерно следующим образом:

![][]

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## Настройка виртуальной машины для размещения MySQL и Django

1.  Следуйте приведенным [здесь][здесь] указаниям, чтобы создать виртуальную машину Azure с дистрибутивом *Windows Server 2008 R2*.

2.  Откройте TCP-порт для транзакций MySQL на виртуальной машине:

 -   Перейдите к только что созданной виртуальной машине на портале Azure и откройте вкладку *КОНЕЧНЫЕ ТОЧКИ*.
 -   Нажмите кнопку *ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ* в нижней части экрана.
 -   Добавьте конечную точку с ИМЕНЕМ **mysql**, ПРОТОКОЛОМ **TCP**, ОБЩИМ ПОРТОМ **3306** и ЧАСТНЫМ ПОРТОМ **3306**.

1.  Добавьте еще одну конечную точку с ИМЕНЕМ **web**, ПРОТОКОЛОМ **TCP**, ОБЩИМ ПОРТОМ **80** и ЧАСТНЫМ ПОРТОМ **80**. В результате внешние интернет-запросы будут перенаправляться в порт, в котором выполняется Django, а именно *80*.

2.  С помощью *Удаленного рабочего стола* Windows удаленно войдите в созданную виртуальную машину Azure.

3.  Откройте TCP-порт **80** на виртуальной машине:

 -   В меню **Пуск** выберите **Администрирование** и **Брандмауэр Windows в режиме повышенной безопасности**.
 -   В левой панели выберите **Правила для входящих подключений**. В панели **Действия** справа выберите **Создать правило...**.
 -   В окне **Мастер создания правила для нового входящего подключения** выберите **Порт** и нажмите кнопку **Далее**.
 -   Выберите **TCP** и **Определенные локальные порты**. Укажите порт «80» (порт прослушивания Django) и нажмите кнопку **Далее**.
 -   Выберите **Разрешить подключение** и нажмите **Далее**.
 -   Нажмите **Далее** еще раз.
 -   Введите имя правила, например «DjangoPort», и нажмите кнопку «Готово».

1.  Установите на виртуальной машине последнюю версию [MySQL Community Server][MySQL Community Server] для Windows:

    **Примечание**. Мы рекомендуем установить базу данных в разделе данных, отличном от раздела операционной системы.

 -   Щелкните ссылку *Windows (x86, 64-разрядная), установщик MSI* [здесь][MySQL Community Server] и скачайте соответствующий установщик MSI с ближайшего к вам зеркала. Полезные советы:

    -   Выберите вариант установки *Полная*.
    -   Выберите в мастере конфигурации раздел *Подробные настройки*.
    -   **Убедитесь, что включена сеть TCP/IP в порте с номером 3306, и добавьте в брандмауэр исключение для этого порта.**
    -   Задайте пароль для пользователя root и разрешите доступ с правами корня с удаленных компьютеров.
 -   Установите пример [базы данных «world»][базы данных «world»] (версия MyISAM):

    -   Скачайте [этот][этот] ZIP-файл на виртуальную машину Azure.
    -   **Распакуйте его в папку *C:\\Users\\Administrator\\Desktop\\world.sql*.**

1.  После установки MySQL выберите меню Windows *Пуск* и запустите только что установленный *клиент командной строки MySQL 5.5*. Введите следующие команды:

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

  Вы должны увидеть ответ, аналогичный приведенному ниже:

  ![][1]

1.  Перед началом разработки своего приложения Django, разумеется, необходимо установить Python+Django на виртуальной машине. Это можно сделать с помощью [Установщика веб-платформы][Установщика веб-платформы]. После установки установщика веб-платформы с его помощью выполните поиск «Django» и установите продукт Django (Python).

 **Примечание.** Для работы по этому учебнику достаточно просто установить продукт *Django* из установщика веб-платформы. Для наших целей **не** требуются *инструменты Python для Visual Studio* или даже пакет SDK для Python для Azure.

1.  Установите пакет клиента MySQL Python. Его можно установить непосредственно [по этой ссылке][по этой ссылке]. После завершения выполните следующую команду, чтобы проверить установку:

![][2]

## Расширение приложения Django Hello World

1.  Следуйте указаниям в учебнике [Django Hello World][Django Hello World], чтобы создать стандартное веб-приложение «Hello, World!» на платформе Django.

2.  Откройте **C:\\django\\helloworld\\helloworld\\settings.py** в предпочитаемом текстовом редакторе. Измените глобальный словарь **DATABASES** следующим образом:

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

 Как вы видите, мы только что задали для Django указания по месту поиска в базе данных MySQL.

 **Примечание.** Вы **должны** изменить ключ *HOST* в соответствии с **постоянным** IP-адресом своей виртуальной машины Windows Azure (MySQL). На этом этапе ключ *HOST* должен соответствовать значению, выводимому командой Windows *ipconfig*.

  После изменения ключа *HOST* в соответствии с IP-адресом виртуальной машины MySQL сохраните этот файл и закройте его.

1.  Теперь, когда мы установили связь с нашей базой данных *djangoazure*, давайте сделаем что-нибудь полезное! С этой целью мы создадим модель для стандартного приложения *Счетчик*. Чтобы дать Django указание создать такое приложение, выполните следующие команды:

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

  Если Django не выводит данные после выполнения последней из перечисленных команд, создание выполнено успешно.

1.  Добавьте следующий текст в **C:\\django\\helloworld\\counter\\models.py**:

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

  Все, что мы сделали — определили подкласс для класса *модели* Django с именем *Counter* и с одним целочисленным полем, *count*. Эта стандартная модель счетчика будет записывать число обращений к нашему приложению Django.

1.  Затем мы уведомим Django о существовании класса *Counter*:

  -   Измените **C:\\django\\helloworld\\helloworld\\settings.py** снова. Добавьте *'counter'* в кортеж *INSTALLED\_APPS*.
  -   Из командной строки выполните следующую команду:

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

1.  Замените содержимое **C:\\django\\helloworld\\helloworld\\views.py**. Приведенная ниже новая реализация функции *hello* использует нашу модель *Counter* в сочетании с отдельной образцовой базой данных *world*, установленной ранее, чтобы создать приемлемую замену для строки "*World*":

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

1.  Переключитесь обратно в окно Windows PowerShell и введите следующие команды, чтобы развернуть общедоступный веб-сайт Django:

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

2.  В локальном браузере откройте \*\*<http://*yourVmName>\*.cloudapp.net\*\* (где *yourVmName* — имя, которое было указано при создании виртуальной машины). Должно быть выведено значение «Hello... !" , как показано на приведенном ниже снимке экрана. Это означает, что Django выполняется на виртуальной машине и работает правильно.

    ![][]

  Обновите браузер несколько раз, и вы увидите, что сообщение изменится с *«Hello **\<country abc\>**»* на *«Hello **\<другая страна\>**»*.

1.  Чтобы остановить размещение веб-сайта в Django, просто перейдите в окно PowerShell и нажмите клавиши **CTRL-C**.

## Завершение работы виртуальной машины Azure

После завершения этого учебного курса завершите работу созданной вами виртуальной машины Azure и/или удалите ее, чтобы освободить ресурсы для других учебных курсов и избежать платы за использование Azure.

  [Django Hello World]: http://windowsazure.com/ru-ru/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [Веб-сайт с MySQL]: http://dev.mysql.com/doc/
  [драйвера MySQL]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  []: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [здесь]: /ru-ru/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  [базы данных «world»]: http://dev.mysql.com/doc/index-other.html
  [этот]: http://downloads.mysql.com/docs/world.sql.zip
  [1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
  [Установщика веб-платформы]: http://www.microsoft.com/web/downloads/platform.aspx
  [по этой ссылке]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
  [2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png

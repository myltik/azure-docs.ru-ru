<properties
	pageTitle="Размещение веб-сайта Ruby on Rails в виртуальной машине Linux | Microsoft Azure"
	description="Настройка и размещение веб-сайта на основе Ruby on Rails в Azure с помощью виртуальной машины Linux."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="11/20/2015"
	ms.author="robmcm"/>





#Веб-приложение Ruby on Rails на виртуальной машине Azure

В этом учебнике описано, как разместить веб-сайт Ruby on Rails в Azure с помощью виртуальной машины Linux.

Данный учебник был проверен с использованием Ubuntu Server 14.04 LTS. Если вы используете другой дистрибутив Linux, могут потребоваться другие действия для установления Rails.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


## Создание виртуальной машины Azure

Начните с создания виртуальной машины Azure с помощью образа Linux.

Чтобы создать виртуальную машину, можно использовать классический портал Azure или интерфейс командной строки Azure.

### Портал управления Azure

1. Перейдите на [классический портал Azure](http://manage.windowsazure.com).
2. Щелкните последовательно **Создать** > **Среда выполнения приложений** > **Виртуальная машина** > **Быстрое создание**. Выберите образ Linux.
3. Введите пароль.

После подготовки виртуальной машины щелкните ее имя и нажмите кнопку **Панель мониторинга**. Найдите конечную точку SSH в списке **Сведения об SSH**.

### Инфраструктура CLI Azure

Выполните действия в статье [Создание виртуальной машины под управлением Linux][vm-instructions].

После подготовки виртуальной машины можно получить конечную точку SSH, выполнив следующую команду:

	azure vm endpoint list <vm-name>  

## Установка Ruby on Rails

1. Используйте SSH, чтобы подключиться к виртуальной машине.

2. В сеансе SSH выполните следующие команды, чтобы установить Ruby на виртуальной машине:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	Установка может занять несколько минут. После завершения воспользуйтесь следующей командой, чтобы проверить установку Ruby:

		ruby -v

	Она возвращает версию Ruby, которая была установлена.

3. Используйте следующую команду для установки Rails:

		sudo gem install rails --no-rdoc --no-ri

	С помощью флагов --no-rdoc и --no-ri можно пропустить установку документации, что значительно ускорит процесс.

## Создание и запуск приложения

Войдите в систему через SSH и выполните следующие команды:

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

Команда [new](http://guides.rubyonrails.org/command_line.html#rails-new) создает новое приложение Rails. Команда [server](http://guides.rubyonrails.org/command_line.html#rails-server) запускает веб-сервер WEBrick, входящий в состав Rails. (В рабочей среде вы, вероятно, будете использовать другой сервер, например Unicorn или Passenger).

Вы должны увидеть результат, аналогичный приведенному ниже.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Добавление конечной точки

1. Перейдите на [классический портал Azure][management-portal] и выберите свою виртуальную машину.

	![список виртуальных машин][vmlist]

2. Выберите **КОНЕЧНЫЕ ТОЧКИ** в верхней части страницы, а затем щелкните **+ ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ** в нижней части страницы.

	![страница конечных точек][endpoints]

3. В диалоговом окне **ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ** выберите «Добавить автономную конечную точку» и нажмите стрелку **Далее**.

	![диалоговое окно добавления конечной точки][new-endpoint1]

3. На следующей интерактивной странице введите такие сведения:

	* **ИМЯ**: HTTP

	* **ПРОТОКОЛ**: TCP

	* **ОБЩИЙ ПОРТ**: 80

	* **ЧАСТНЫЙ ПОРТ**: 3000

	После этого будет создан общедоступный порт 80, который будет применяться для маршрутизации трафика на частный порт 3000, прослушиваемый сервером Rails.

	![диалоговое окно добавления конечной точки][new-endpoint]

4. Щелкните флажок, чтобы сохранить конечную точку.

5. Должно появиться сообщение **ВЫПОЛНЯЕТСЯ ОБНОВЛЕНИЕ**. После того как сообщение исчезнет, конечная точка будет активирована. Теперь вы можете протестировать приложение, перейдя к имени DNS виртуальной машины. Веб-сайт должен выглядеть аналогично следующему:

	![страница Rails по умолчанию][default-rails-cloud]


##<a id="next"></a>Дальнейшие действия

В этом учебнике большинство действий вы выполняли вручную. В рабочей среде вы напишете приложение на компьютере для разработки и развернете его на виртуальной машине Azure. Кроме того, большинство рабочих сред размещают приложение Rails вместе с другим серверным процессом, например Apache или NginX, который обрабатывает маршрутизацию запросов к нескольким экземплярам приложения Rails и обслуживает статические ресурсы. Дополнительные сведения см. на веб-сайте http://rubyonrails.org/deploy/.

Дополнительную информацию о Ruby on Rails см.на веб-сайте [Руководства по Ruby on Rails][rails-guides].

Об использовании служб Azure из приложения Ruby можно узнать из следующих материалов:

* [Хранение неструктурированных данных с использованием BLOB-объектов][blobs]

* [Хранение пар «ключ-значение» с помощью таблиц][tables]

* [Передача контента с широкой полосой пропускания с помощью CDN][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!---HONumber=AcomDC_1203_2015-->
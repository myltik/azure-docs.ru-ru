<properties 
	pageTitle="Развертывание веб-приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano - учебник" 
	description="Узнайте, как выполнить развертывание приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano, Unicorn и Nginx." 
	authors="blackmist" 
	manager="wpickett" 
	editor="" 
	services="virtual-machines" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


#Развертывание веб-приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano

В этом руководстве описывается развертывание веб-сайта на основе Ruby on Rails на виртуальной машине Azure с помощью [Capistrano 3](https://github.com/capistrano/capistrano/). После развертывания используются [Nginx](http://nginx.org/) и [Unicorn](https://github.com/blog/517-unicorn) для размещения веб-сайта. [PostgreSQL](https://www.postgresql.org) будет сохранять данные для развернутого приложения.

Руководство предполагает отсутствие предыдущего опыта использования Azure, но построено с учетом того, что вы знакомы с Ruby, Rails, Git и Linux. По завершении изучения этого учебника вы получите приложение на основе Ruby on Rails, выполняемое в облаке.

Вы научитесь:

* настраивать среды разработки и производства

* устанавливать Ruby и Ruby on Rails;

* устанавливать Unicorn, Nginx и PostgreSQL

* создавать новое приложение Rails;

* создавать развертывание Capistrano и использовать его для развертывания приложения

Далее представлен снимок экрана завершенного приложения:

![a browser displaying Listing Posts][blog-rails-cloud]

> [AZURE.NOTE] Приложение, используемое для этого руководства, использует собственные двоичные компоненты. При развертывании ВМ вы можете столкнуться с ошибками, если среда разработки не построена на базе Linux. Файл Gemfile.lock, используемый во время разработки, будет содержать пакеты для конкретной платформы, в которых могут отсутствовать входы к собственным версиям Linux пакетов, необходимых для ВМ.
> 
> Для вызова среды разработки Windows выполняются определенные шаги. Однако при возникновении во время или после развертывания ошибок, которые не описаны в этой статье, вы можете повторить действия в среде разработки на основе Linux.

##Содержание

* [Настройка среды разработки](#setup)

* [Создание приложения Rails](#create)

* [Тестирование приложения](#test)

* [Создание репозитория исходного кода](#repository)

* [Создание виртуальной машины Azure](#createvm)

* [Тестирование Nginx](#nginx)

* [Подготовка к развертыванию](#capify)

* [Развертывание](#deploy)

* [Дальнейшие действия](#next)

##<a id="setup"></a>Настройка среды разработки

1. Установка Ruby в среде разработки. Действия, необходимые для установки, зависят от операционной системы.

	* **Apple OS X** - существует несколько выпусков Ruby для OS X. Данное руководство было проверено на OS X с помощью [Homebrew](http://brew.sh/) для установки **rbenv**, **ruby-build** и **Ruby 2.0.0-p451**. Информацию об установке можно найти на веб-сайте [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - используйте собственную систему управления пакетами распространения. Этот учебник был проверен на Ubuntu 12.10 с использованием **rbenv**, **ruby-build** и **Ruby 2.0.0-p451**.

	* **Windows** - для Windows доступно несколько выпусков Ruby. Этот учебник был проверен с использованием [RubyInstaller](http://RubyInstaller.org/) для установки **Ruby 2.0.0-p451**. Команды выполнялись с использованием командной строки **GitBash**, доступной в [Git для Windows](http://git-scm.com/download/win).

2. Откройте новое окно командной строки или сеанс терминала и введите следующую команду для установки Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] Для выполнения этой команды в некоторых операционных системах могут потребоваться привилегии администратора или корня. Если при выполнении этой команды возникла ошибка, попробуйте использовать  'sudo' следующим образом.
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE] Для этого учебника использовался пакет Rails версии 4.0.4.

3. Кроме того, необходимо установить интерпретатор JavaScript, который будет использоваться Rails для компиляции активов CoffeeScript, используемых приложением Rails. Список поддерживаемых интерпретаторов доступен на веб-сайте [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	> [AZURE.NOTE] [[Node.js]](http://nodejs.org/) использован для этого учебника, так как он доступен для ОС OS X, Linux и Windows.

##<a id="create"></a>Создание приложения Rails

1. В командной строке или в сеансе терминала создайте новое приложение Rails с именем "blog_app", используя следующую команду:

		rails new blog_app

	Эта команда создает новый каталог с именем **blog_app** и заполняет его файлами и подкаталогами, необходимыми приложению Rails.

	> [AZURE.NOTE] Для выполнения этой команды может потребоваться от одной до нескольких минут. Она выполняет автоматическую установку пакетов, необходимых для приложения по умолчанию, и в это время не будет реагировать на запросы.

2. Измените каталоги на **blog_app**, а затем используйте следующую команду, чтобы создать базовый шаблон блога:

		rails generate scaffold Post name:string title:string content:text

	Будут созданы контроллер, представление, модель и переносы базы данных, используемые для хранения записей блога. У каждой записи будет имя автора, заголовок и текстовый контент.

3. Для создания базы данных, в которой будут храниться записи блога, используйте следующую команду:

		rake db:migrate

	В результате создается схема базы данных для сохранения записей с использованием поставщика базы данных по умолчанию для Rails, т. е. [Базы данных SQLite3][sqlite3].

4. Чтобы отобразить индекс записей в качестве домашней страницы, измените файл **config/routes.rb** т добавьте следующее после строки `resources :posts`.

		root 'posts#index'

	В результате, когда пользователи посещают веб-сайт, будет отображаться список записей.

##<a id="test"></a>Тестирование приложения

1. Измените каталоги на **blog_app**, если это еще не сделано, а затем запустите сервер Rails с помощью следующей команды.

		rails s

	Вы должны увидеть результат, аналогичный приведенному ниже. Обратите внимание на порт, который прослушивает веб-сервер. В примере ниже это порт 3000.

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Откройте браузер и перейдите по адресу http://localhost:3000/. Вы должны увидеть страницу, аналогичную показанной ниже.

	![a page listing posts][blog-rails]

	Чтобы остановить серверный процесс, введите CTRL+C в командной строке.

##<a id="repository"></a>Создание репозитория исходного кода

При развертывании приложения с помощью Capistrano файлы должны извлекаться из репозитория. В данном учебнике мы используем [Git](http://git-scm.com/) для управления версиями и [GitHub](https://github.com/) в качестве репозитория.

1.	Создайте новый репозиторий на [GitHub](https://github.com/). Если у вас нет учетной записи GitHub, вы можете зарегистрировать бесплатную учетную запись. В этом учебнике предполагается, что имя репозитория - **blog_app**.

	> [AZURE.NOTE] Чтобы обеспечить поддержку автоматизированных развертываний приложения, необходимо использовать ключи SSH для аутентификации в GitHub. Дополнительную информацию см. в документации GitHub по [созданию ключей SSH](https://help.github.com/articles/generating-ssh-keys).

2.	В командной строке измените каталоги на **blog_app** и выполните следующие команды, чтобы отправить приложение в репозиторий GitHub. Замените **YourGitHubName** именем своей учетной записи GitHub.

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

В следующем разделе мы займемся созданием виртуальной машины, на которой будет развертываться это приложение.

##<a id="createvm"></a>Создание виртуальной машины Azure

Следуйте инструкциям, указанным [здесь][vm-instructions], чтобы создать виртуальную машину Azure с операционной системой Linux.

1. Войдите на [Портал управления Azure][management-portal]. На панели команд щелкните **Создать**.

2. Щелкните **Виртуальная машина** и **Из коллекции**.

3. Из списка **Выбрать образ** выберите **Ubuntu**, а затем выберите версию **12.04 LTS**. Щелкните стрелку для продолжения.

	> [AZURE.NOTE] Шаги в этом учебнике были выполнены на виртуальной машине Azure с Ubuntu 12.04 LTS. При использовании другого дистрибутива Linux для выполнения тех же задач могут потребоваться другие действия.

4. В поле **Имя виртуальной машины** введите имя, которое вы хотите использовать. Это имя будет использоваться для создания имени домена данной виртуальной машины.

5. В поле **Новое имя пользователя** введите имя учетной записи администратора для этого компьютера.

	> [AZURE.NOTE] В этом учебнике для развертывания приложения будет использоваться учетная запись администратора. Информацию о создании отдельной учетной записи для развертывания см. в документации по [Capistrano][capistrano].

6. В разделе **Аутентификация** выберите **Загрузить совместимый ключ SSH для проверки подлинности**, затем перейдите и выберите **PEM**-файл, содержащий сертификат. И наконец, щелкните стрелку для продолжения.

	> [AZURE.NOTE] Если вы не знакомы с созданием или использованием ключа SSH, см. раздел [Использование SSH с Linux на Azure][ssh-on-azure], чтобы ознакомиться с указаниями по созданию ключей SSH.
	> 
	> Кроме этого, можно включить проверку подлинности с использованием пароля, однако ключ SSH также должен быть предоставлен, поскольку он используется для автоматизации развертывания.

7. В разделе **Конечные точки** из раскрывающегося списка **Введите/выберите значение** выберите пункт **HTTP**. В остальных полях этой страницы можно оставить значения по умолчанию. Запишите значение поля **DNS-имя облачной службы**, так как оно будет использоваться на последующих шагах. И наконец, щелкните стрелку для продолжения.

8. На последней странице установите флажок, чтобы выбрать виртуальную машину.

###Установка Git, Ruby и Nginx

После создания виртуальной машины подключитесь к ней, используя SSH, и с помощью следующих команд подготовьте среду размещения для своего приложения Ruby.

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
	echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE] Чтобы избежать опечаток при выполнении команд, можно сохранить указанный выше код как сценарий (SH-файл).


> [AZURE.NOTE] Команда **~/.rbenv/bin/rbenv install 2.0.0-p451** может выполняться несколько минут.

Сценарий **rbenv-install.sh** выполняет следующие действия:
	
* обновляет и модернизирует установленные в настоящее время пакеты
* устанавливает средства построения
* устанавливает Git
* устанавливает Node.js
* устанавливает Nginx
* устанавливает средства построения и другие утилиты, необходимые для Ruby и Rails
* настраивает локальное (пользовательское) развертывание [rbenv];
* устанавливает Ruby 2.0.0-p451
* устанавливает GEM-файл [Bundler].

После установки используйте следующую команду, чтобы проверить установку Ruby:

	ruby -v

Эта команда должна вернуть `ruby 2.0.0p451` в качестве версии.

###Установка PostgreSQL

В качестве базы данных по умолчанию Rails использует для развертывания базу данных SQLite. Обыкновенно при производстве используется что-нибудь другое. При выполнении следующих шагов установите PostgreSQL на виртуальной машине, а затем создайте пользователя и базу данных. Далее выполняется настройка приложения Rails для использования PostgreSQL во время развертывания.

1. Установите PostgreSQL и биты развертывания, используя следующую команду.

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. Чтобы создать пользователя и базу данных для вашего входа, используйте следующие команды. Замените **my_username** и **my_database** своим именем для входа. Например, если для виртуальной машины используется имя для входа **deploy**, замените **my_username** и **my_database** на **deploy**.

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE] Используйте имя пользователя также и для имени базы данных. Это необходимо для пакета capistrano-postgresql, который используется данным приложением.

	При появлении запроса введите пароль для пользователя. При появлении запроса на получение для пользователя разрешения создавать новые роли выберите **y** (да), так как этот пользователь будет использоваться во время развертывания для создания базы данных и имени для входа, которые будет использовать приложение Rails.

7. Чтобы проверить возможность подключения к базе данных с помощью учетной записи пользователя, используйте следующую команду. Замените **my_username** и **my_database** значениями, использованными раньше.

		psql -U my_username -W my_database

	После этого должна появиться командная строка  `database=>`. Чтобы выйти из служебной программы psql, введите `\q` в командной строке.

###<a id="nginx"></a>Тестирование Nginx

Конечная точка HTTP, добавленная во время создания виртуальной машины, позволяет принимать HTTP-запросы через порт 80. Чтобы убедиться в этом, используйте следующие шаги для проверки возможности доступа к сайту, по умолчанию созданному с помощью Nginx.

2.	В сеансе SSH с виртуальной машиной запустите службу Nginx:

		sudo service nginx start

	Эта команда запустит службу Nginx, прослушивающую входящий трафик на порту 80.

6. Теперь вы можете протестировать приложение, перейдя к имени DNS виртуальной машины. Веб-сайт должен выглядеть аналогично следующему:

	![nginx welcome page][nginx-welcome]

	> [AZURE.NOTE] Сценарии развертывания, которые будут выполняться далее в этом учебнике, включат обслуживание веб-сайта по умолчанию blog_app с использованием Nginx.

На этом этапе у вас имеется виртуальная машина Azure с установленными Ruby, Nginx и PostgreSQL, которая готова для развертывания. В следующем разделе мы займемся изменением приложения Rails для добавления скриптов и информации, которые выполняют развертывание.

##<a id="capify"></a>Подготовка к развертыванию

В среде разработки измените приложение для использования веб-сервера Unicorn, PostgreSQL и включите Capistrano для развертывания и создания скриптов, используемых при развертывании и запуске приложения.

1. На компьютере разработки измените **Gemfile** и добавьте следующие строки для того, чтобы добавить пакеты для Unicorn, PostgreSQL и Capistrano, а также связанные GEM-файлы в приложение Rails

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE] Unicorn недоступен в Windows. Если вы используете Windows как среду разработки, измените __Gemfile__, чтобы устанавливать Unicorn только при развертывании на виртуальной машине с использованием следующего кода при указании GEM-файла unicorn:
	> 
	> `platforms :ruby do`
	> `  gem 'unicorn'`
	> `end`

	Большинство пакетов capistraon-* представляют собой вспомогательные приложения, которые работают с определенными объектами, используемыми на рабочем сервере (rbenv,) или в среде (rails).

	Пакет capistrano-unicorn-nginx автоматически создает скрипты, используемые с Unicorn и Nginx, во время развертывания, и нам не приходится создавать их вручную. Пакет capistrano-postgresql автоматически создает базу данных, пользователя и пароль в PostgreSQL для вашего приложения. И хотя их использовать совсем необязательно, оба этих пакета значительно упрощают процесс развертывания.
 
5.	Чтобы установить новые пакеты, используйте следующие команды.
		
		bundle

6. С помощью следующей команды создайте файлы развертывания по умолчанию, используемые Capistrano.

		cap install

	После выполнения команды `cap install` в приложение добавятся следующие файлы и каталоги.

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	Файл **deploy.rb** обеспечивает универсальную настройку и действия для всех типов развертывания, а **production.rb** и **staging.rb** содержат дополнительные настройки для развертывания в рабочей среде и промежуточных развертываний.

	В папке **capistrano** содержатся задачи и другие файлы, которые используются в процессе развертывания.

5. Измените **Capfile** в корне приложения и раскомментируйте следующие строки, удалив символ __#__ в начале строки.

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	После раскомментирования предыдущих строк добавьте следующие строки.

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	Эти строки дают указание Capistrano использовать пакеты, которые ранее были добавлены в Gemfile.

	После выполнения указанных выше изменений сохраните файл.

6.  Измените файл **config/deploy.rb** и замените его содержимое следующим. Замените **YourApplicationName** на имя своего приложения и замените **https://github.com/YourGitHubName/YourRepoName.git** URL-адрес репозитория GitHub для данного проекта.

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	В этом файле содержатся универсальные задачи и информация, общие для всех типов развертывания.

5.  Измените файл **config/deploy/production.rb** и замените его содержимое следующим. Замените **YourVm.cloudapp.net** именем домена своей виртуальной машины. Замените **YourAzureVMUserName** учетной записью входа, созданной ранее для вашей виртуальной машины Azure.

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	В этом файле содержится информация для производственных развертываний.

8.	Выполните следующие команды, чтобы подтвердить изменения в файлах, сделанные на предыдущих этапах, а затем отправьте изменения в GitHub.

		git add .
		git commit -m "adding config files"
		git push

После этого приложение должно быть готово для развертывания.

> [AZURE.NOTE] Для более сложных приложений или для других баз данных и сервера приложений могут понадобиться дополнительные сценарии настройки или развертывания.

##<a id="deploy"></a>Развертывание

2.	На своем локальном компьютере для разработки используйте следующую команду для развертывания файлов конфигурации, используемых приложением для ВМ.

		bundle exec cap production setup

	Capistrano подключается к ВМ с использованием SSH, а затем создает каталог (~/apps), в котором будет развертываться приложение. Если это первое развертывание, пакет capistrano-postgresql также создает роль и базу данных в PostgreSQL на сервере. Кроме этого, он создает файл конфигурации database.yml, который будет использоваться Rails для подключения к базе данных.

	> [AZURE.NOTE] При появлении сообщения об ошибке **Ошибка при чтении из сокета проверки подлинности длины ответа** во время развертывания, может потребоваться запустить агент SSH в среде разработки с помощью команды `ssh-agent`. Например, добавьте `eval $(ssh-agent)` в файл ~/.bash\_profile.
	> 
	> Кроме этого, может понадобиться добавить ключ SSH в кэш агента, используя команду  `ssh-add`.

4.	Выполните производственное развертывание, используя следующую команду. В результате приложение развертывается на виртуальной машине. Запустите службу Unicorn и настройте Nginx для маршрутизации трафика на Unicorn.

		bundle exec cap production deploy

	Эта команда развертывает приложение на ВМ, устанавливает требуемые пакеты, а затем запускает/ перезапускает Unicorn и Nginx.

	> [AZURE.NOTE] Во время обработки процесс может приостанавливаться на несколько минут.

	> [AZURE.NOTE] Некоторые части развертывания могут возвращать "состояние выхода 1 (сбой)". В общем случае на это можно не обращать внимания, если развертывание завершается успешно.

	> [AZURE.NOTE] На некоторых системах можно столкнуться с ситуацией, когда агенту SSH не удается отправить данные для входа на удаленную виртуальную машину во время аутентификации в GitHub. В таких случаях можно обойти ошибку, изменив файл **config/deploy.rb** и изменив строку `set :repo_url` для использования HTTPS во время доступа к Github. При использовании HTTPS необходимо назначить имя пользователя и пароль GitHub (или маркер проверки подлинности) как часть URL-адреса. Например:
	> 
	> `set :repo_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
	> 
	> Хотя это позволит обойти ошибку и завершить руководство, такое решение не рекомендуется использовать при производственном развертывании, так как оно сохраняет данные для проверки подлинности в виде простого текста как часть приложения. Вам необходимо познакомиться с документацией своей операционной системы в части использования передачи с помощью агента SSH

На этом этапе приложение Ruby on Rails должно работать на виртуальной машине Azure. Чтобы проверить, так ли это, введите DNS-имя виртуальной машины в браузере. Например, http://railsvm.cloudapp.net. Должен появиться индекс записей, а вы теперь должны иметь возможность создавать, изменять и удалять записи.

##<a id="next"></a>Дальнейшие действия

В этой статье вы изучили, как создать и опубликовать базовое приложение Rails на виртуальной машине Azure с помощью Capistrano. Работа с базовым приложением, подобным рассматриваемому в данной статье, затрагивает лишь небольшую часть возможностей Capistrano при использовании для развертывания. Дополнительные сведения об использовании Capistrano см. по следующим ссылкам:

* [Capistranorb.com](http://capistranorb.com) - сайт Capistrano.
* [Azure, Ruby on Rails, Capistrano 3, и PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql) - альтернативный подход к развертыванию на Azure с использованием настраиваемых скриптов развертывания.
* [Руководство по Capistrano 3](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/) - руководство по работе с Capistrano 3.

Более простой пример создания и развертывания приложения Rails на виртуальной машине Azure только с использованием SSH см. в разделе [Размещение веб-приложения Ruby on Rails с использованием виртуальной машины Linux][ruby-vm].

Дополнительные сведения о Ruby on Rails см. на веб-сайте [Руководства по Ruby on Rails][rails-guides].

Сведения об использовании пакета Azure SDK для Ruby для доступа к службам Azure из приложения Ruby см. в статьях:

* [Хранение неструктурированных данных с использованием больших двоичных объектов][blobs]

* [Хранение пар "ключ-значение" с использованием таблиц][tables]

* [Обслуживание содержимого для каналов с высокой пропускной способностью с помощью сети CDN][cdn-howto]

[vm-instructions]: /ru-ru/manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /ru-ru/develop/ruby/how-to-guides/blob-storage/
[tables]: /ru-ru/develop/ruby/how-to-guides/table-service/
[cdn-howto]: /ru-ru/develop/ruby/app-services/
[ruby-vm]: /ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com

<!--HONumber=45--> 

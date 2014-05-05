<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails на ВМ Azure с помощью Capistrano" pageTitle="Развертывание веб-приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano - учебник" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails вм, capistrano azure вм, capistrano azure rails, unicorn azure вм, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Узнайте, как выполнить развертывание приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano, Unicorn и Nginx" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Развертывание веб-приложения Ruby on Rails на ВМ Azure с помощью Capistrano" authors="" />



#Развертывание веб-приложения Ruby on Rails на виртуальной машине Azure с помощью Capistrano

В этом руководстве описывается развертывание веб-сайта на основе Ruby on Rails на виртуальной машине Azure с помощью [Capistrano](https://github.com/capistrano/capistrano/). Здесь также описывается, как использовать [Nginx](http://nginx.org/) и [Unicorn](https://github.com/blog/517-unicorn) для размещения приложения на виртуальной машине.

В данном учебнике предполагается, что у читателя нет опыта использования платформы Azure. По завершении изучения этого учебника вы получите приложение на основе Ruby on Rails, выполняемое в облаке.

Вы научитесь:

* настраивать среду разработки;

* устанавливать Ruby и Ruby on Rails;

* устанавливать и настраивать Nginx и Unicorn;

* создавать новое приложение Rails;

* развертывать приложение Rails на виртуальной машине Azure с помощью Capistrano.

Далее представлен снимок экрана завершенного приложения:

![записи с описанием приложения в браузере][blog-rails-cloud]

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Приложение, используемое для этого руководства, использует собственные двоичные компоненты. Поэтому если среда разработки работает не в Linux-подобной системе, могут возникнуть проблемы, так как файл Gemfile.lock, созданный на компьютере разработчика, может не содержать записи для Linux-совместимой версии необходимых пакетов приложения.</p>
<p>Особые действия необходимы для использования среды разработки Windows, поскольку она сильнее всего отличается от целевой среды развертывания. Однако при возникновении ошибок во время или после развертывания, которые не описаны в этой статье, вы можете повторить действия в среде разработки на основе Linux.</p>

</div>

##В этой статье

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

	* **Apple OS X** - для OS X существует несколько выпусков Ruby. Данное руководство было проверено на OS X с использованием [Homebrew](http://brew.sh/) для установки **rbenv** и **ruby-build**. Сведения об установке можно найти в [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - используйте собственную систему управления пакетами распространения. Данное руководство было проверено на системе Ubuntu 12.10 с использованием пакетов ruby1.9.1 и ruby1.9.1-dev.

	* **Windows** - для Windows доступно несколько выпусков Ruby. Данное руководство было проверено с использованием [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2. Откройте новое окно командной строки или сеанс терминала и введите следующую команду для установки Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Для выполнения этой команды в некоторых операционных системах могут потребоваться права администратора или учетной записи root. Если при выполнении этой команды возникла ошибка, попробуйте использовать "sudo" следующим образом:</p>
	<pre class="prettyprint">sudo gem install rails</pre>
	</div>

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Для этого руководства использовался пакет Rails версии 3.2.12.</p>
	</div>

3. Кроме того, необходимо установить интерпретатор JavaScript, который будет использоваться Rails для компиляции активов CoffeeScript, используемых приложением Rails. Список поддерживаемых интерпретаторов доступен на веб-сайте [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	При проверке этого руководства использовалась библиотека [Node.js](http://nodejs.org/), поскольку она доступна для OS X, Linux и Windows.

##<a id="create"></a>Создание приложения Rails

1. В командной строке или в сеансе терминала создайте новое приложение Rails с именем "blog_app", используя следующую команду:

		rails new blog_app

	Эта команда создает новый каталог **blog_app** и заполняет его файлами и вложенными каталогами, необходимыми приложению Rails.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Для выполнения этой команды может потребоваться одна или несколько минут. Она выполняет автоматическую установку пакетов, необходимых для приложения по умолчанию. В это время может показаться, что система зависла.</p>
	</div>

2. Измените каталоги на **blog_app**, а затем используйте следующую команду, чтобы создать базовый шаблон блога:

		rails generate scaffold Post name:string title:string content:text

	Будут созданы контроллер, представление, модель и переносы базы данных, используемые для хранения записей блога. У каждой записи будут имя автора, заголовок и текстовый контент.

3. Для создания базы данных, в которой будут храниться записи блога, используйте следующую команду:

		rake db:migrate

	Эта команда использует поставщика базы данных по умолчанию для Rails, т. е. [базу данных SQLite3][sqlite3]. Вы можете использовать другую базу данных для рабочего приложения, однако для этого руководства SQLite вполне достаточно.

##<a id="test"></a>Тестирование приложения

Выполните следующие действия, чтобы запустить сервер Rails в среде разработки.

1. Измените каталоги на **blog_app**, если это еще не сделано, а затем запустите сервер Rails с помощью следующей команды:

		rails s

	Вы должны увидеть результат, аналогичный приведенному ниже. Обратите внимание на порт, который прослушивает веб-сервер. В примере ниже это порт 3000.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Откройте браузер и перейдите по адресу http://localhost:3000/. Вы должны увидеть страницу, аналогичную показанной ниже:

	![страница Rails по умолчанию][default-rails]

	Это статическая страница приветствия. Чтобы просмотреть формы, создаваемые командой формирования шаблонов, перейдите по адресу http://localhost:3000/posts. Вы должны увидеть страницу, аналогичную показанной ниже:

	![страница со списком записей][blog-rails]

	Чтобы остановить серверный процесс, введите CTRL+C в командной строке.

##<a id="repository"></a>Создание репозитория исходного кода

В этом руководстве мы будем использовать [Git](http://git-scm.com/) и [GitHub](https://github.com/) для управления версиями и централизованного хранения кода.

1. 	Создайте новый репозиторий на [GitHub](https://github.com/). Если у вас нет учетной записи GitHub, вы можете зарегистрировать бесплатную учетную запись. В этом руководстве предполагается, что имя репозитория — **blog_app**.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Скрипты, созданные в последующих разделах этого документа, будут содержать адрес виртуальной машины и имя пользователя, применяемое для развертывания приложения по протоколу SSH. Поэтому мы рекомендуем использовать частный репозиторий GitHub, если это возможно.</p>
	</div>

2. 	В командной строке измените каталоги на **blog_app** и выполните следующие команды, чтобы отправить первоначальную версию приложения в репозиторий GitHub:

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
		git push -u origin master


##<a id="createvm"></a>Создание виртуальной машины Azure

Следуйте инструкциям, указанным [здесь][vm-instructions], чтобы создать виртуальную машину Azure с операционной системой Linux.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Действия, описанные в этом учебнике, были выполнены на виртуальной машине Azure с Ubuntu 12.10. Если вы используете другой дистрибутив Linux, для выполнения тех же заданий могут потребоваться другие действия.</p>
</div>

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Необходимо <strong>только</strong> создать виртуальную машину. Остановитесь, когда узнаете, как подключиться к виртуальной машине с помощью SSH.</p>
</div>

После создания виртуальной машины Azure с помощью SSH выполните следующие действия, чтобы установить Ruby и Rails на виртуальной машине:

1. Подключитесь к виртуальной машине с помощью SSH и выполните следующие команды, чтобы обновить существующие пакеты и установить среду Ruby:

		sudo apt-get -y update
		sudo apt-get -y upgrade
		sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

	После установки используйте следующую команду, чтобы проверить установку Ruby:

		ruby -v

	Эта команда должна вернуть версию Ruby, установленную на виртуальной машине, которая может отличаться от 1.9.1. Например, **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]**.

2. Используйте следующую команду для установки Bundler:

		sudo gem install bundler

	Bundler используется для установки пакетов, необходимых приложению Rails, после его копирования на сервер.

##<a id="nginx"></a>Открытие порта 80 и тестирование Nginx

Nginx предоставляет веб-сайт по умолчанию, который мы можем использовать, чтобы убедиться, что наша виртуальная машина принимает интернет-трафик. Выполните следующие действия, чтобы разрешить прием трафика по порту 80 и проверить веб-сайт Nginx по умолчанию.

2. 	В сеансе SSH с виртуальной машиной запустите службу Nginx:

		sudo service nginx start

	Эта команда запустит службу Nginx, прослушивающую входящий трафик на порту 80.

2. В браузере перейдите на [портал управления Azure][management-portal] и выберите свою виртуальную машину.

	![список виртуальных машин][vmlist]

3. Выберите **КОНЕЧНЫЕ ТОЧКИ** в верхней части страницы, а затем щелкните **+ ДОБАВИТЬ КОНЕЧНУЮ ТОЧКУ** в нижней части страницы.

	![страница конечных точек][endpoints]

4. В диалоговом окне **ДОБАВЛЕНИЕ КОНЕЧНОЙ ТОЧКИ** щелкните стрелку в нижнем левом углу для перехода на вторую страницу и введите следующие данные в форме:

	* **NAME**: HTTP

	* **PROTOCOL**: TCP

	* **PUBLIC PORT**: 80

	* **PRIVATE PORT**: 80

	После этого будет создан общий порт 80, который применяется для маршрутизации трафика на частный порт 80, - который прослушивает служба Nginx.

	![диалоговое окно добавления конечной точки][new-endpoint]

5. Щелкните значок галочки, чтобы сохранить конечную точку.

6. Должно появиться сообщение **ВЫПОЛНЯЕТСЯ ОБНОВЛЕНИЕ**. После того как сообщение исчезнет, конечная точка будет активирована. Теперь вы можете протестировать приложение, перейдя к имени DNS виртуальной машины. Веб-сайт должен выглядеть аналогично следующему рисунку:

	![страница приветствия nginx][nginx-welcome]

2. 	Остановите и удалите веб-сайт Nginx по умолчанию с помощью следующих команд:

		sudo service nginx stop
		sudo rm /etc/nginx/sites-enabled/default

	Скрипты развертывания, которые будут выполняться далее в этом руководстве, включат обслуживание веб-сайта по умолчанию blog_app с использованием Nginx.

##<a id="capify"></a>Подготовка к развертыванию

В этом разделе вы измените приложение, чтобы использовать веб-сервер Unicorn, включите Capistrano для развертывания, включите доступ к GitHub из виртуальной машины и создадите скрипты, используемые для развертывания и запуска приложения.

1. Авторизуйте виртуальную машину для проверки подлинности вашей учетной записи GitHub с помощью сертификата, выполнив действия, описанные на странице [Создание ключей SSH](https://help.github.com/articles/generating-ssh-keys#platform-all). Это необходимо, чтобы получить доступ к репозиторию GitHub из скриптов развертывания.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Хотя ключ SSH необходимо создать на виртуальной машине, вы можете добавить его в учетную запись GitHub с помощью браузера в среде разработки.</p>
	<p>Чтобы просмотреть содержимое сертификата SSH и скопировать и вставить его в GitHub, используйте следующую команду:</p>
	<pre>cat ~/.ssh/id_rsa.pub</pre>
	</div>


1. На компьютере разработчика измените **Gemfile** и раскомментируйте строки для **Capistrano** и **Unicorn**, удалив символ "#" в начале следующих строк:

		# gem 'unicorn'
		# gem 'capistrano'

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Unicorn недоступен в Windows. Если вы используете Windows как среду разработки, измените <strong>Gemfile</strong>, чтобы устанавливать Unicorn только при развертывании на виртуальной машине:</p>
	<pre class="prettyprint">platforms :ruby do<br />  gem 'unicorn'<br />end</pre>
	</div>
 
5. 	Выполните следующие команды, чтобы установить новые пакеты и настроить Capistrano для вашего проекта:
		
		bundle
		capify .

6. 	Добавьте следующие файлы в каталог **blog_app/config** и вставьте в каждый файл содержимое, описанное по ссылкам ниже:

	* [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) - настройка Nginx для работы с Unicorn и обслуживания статических файлов, входящих в приложение Rails
	* [unicorn_init.sh](https://gist.github.com/3272994) - скрипт оболочки, используемый для запуска серверного процесса Unicorn
	* [unicorn.rb](https://gist.github.com/3273014) - файл конфигурации для Unicorn

	В каждом файле замените слово **blogger** на имя пользователя, применяемое для входа на виртуальную машину. Это имя пользователя будет использоваться при развертывании приложения.

7. В каталоге **blog_app/config** замените существующее содержимое файла **deploy.rb** на следующий код:

		require "bundler/capistrano"

		set :application, "blog_app"
		set :user, "blogger"

		set :scm, :git
		set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
		set :branch, "master"
		set :use_sudo, true

		server "VMDNSName", :web, :app, :db, primary: true

		set :deploy_to, "/home/#{user}/apps/#{application}"
		default_run_options[:pty] = true
		ssh_options[:forward_agent] = true
		ssh_options[:port] = SSHPort

		namespace :deploy do
		  desc "Fix permissions"
		  task :fix_permissions, :roles => [ :app, :db, :web ] do
		  	run "chmod +x #{release_path}/config/unicorn_init.sh"
		  end

		  %w[start stop restart].each do |command|
		    desc "#{command} unicorn server"
		    task command, roles: :app, except: {no_release: true} do
		      run "service unicorn_#{application} #{command}"
		    end
		  end

		  task :setup_config, roles: :app do
		    sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
		    sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
		    sudo "mkdir -p #{shared_path}/config"
		  end
		  after "deploy:setup", "deploy:setup_config"

		  task :symlink_config, roles: :app do
		    # Add database config here
		  end
		  after "deploy:finalize_update", "deploy:fix_permissions"
		  after "deploy:finalize_update", "deploy:symlink_config"
		end

	В приведенном выше тексте замените следующее:

	* **YourGitHubAccount** — на имя вашей учетной записи GitHub;
	* **VMDNSName** — на DNS виртуальной машины Azure
	* **blogger** — на имя пользователя, используемое для входа на виртуальную машину;
	* **SSHPort** — на внешний порт SSH для вашей виртуальной машины.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Если среда разработки — это система на основе Windows, необходимо добавить следующую строку в файл <b>deploy.rb</b>. Данный код следует разместить с другими инструкциями <b>set</b> в начале файла:</p>
	<pre>set :bundle_flags, "--no-deployment --quiet"</pre>
	<p>Это не является рекомендуемым методом, так как приводит к загрузке пакетов из Gemfile вместо Gemfile.lock во время развертывания, но это необходимо, поскольку целевая система (Linux) отличается от системы разработки (Windows).</p>
	</div>

7.	Раскомментируйте строку активов в файле **Capfile**, расположенном в каталоге **blog_app**.

		load 'deploy/assets'

8.	Выполните следующие команды, чтобы применить изменения к проекту и передать их в GitHub.

		git add .
		git commit -m "adding config files"
		git push

##<a id="deploy"></a>Развертывание

2.	На локальном компьютере разработки выполните следующую команду, чтобы настроить удаленную виртуальную машину Azure для развертывания.

		cap deploy:setup

	При появлении запроса введите пароль учетной записи пользователя виртуальной машины. Capistrano подключится к виртуальной машине и создаст каталог **apps** в домашнем каталоге учетной записи пользователя.

3.	С помощью SSH-подключения к виртуальной машине Azure измените разрешения каталога **app** с использованием следующей команды:
		
		sudo chmod -R 777 apps

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Не рекомендуется выполнять это действие для рабочих сред, оно используется сейчас только для демонстрационных целей.</p>
	</div>

4.	Выполните "холодное" развертывание с помощью следующей команды в вашей среде разработки. Команда выполняет развертывание приложения на виртуальной машине и запускает службу Unicorn.

		cap deploy:cold

3.	Запустите службу Nginx, которая начнет перенаправлять трафик в службу Unicorn и обрабатывать статический контент:

		sudo service nginx start

На этом этапе приложение Ruby on Rails должно работать на виртуальной машине Azure. Чтобы проверить, так ли это, введите DNS-имя виртуальной машины в браузере. Например, http://railsvm.cloudapp.net. Должен открыться экран "Welcome aboard" (Добро пожаловать):

![страница "Welcome aboard"][default-rails-cloud]

Если добавить "/posts" к URL-адресу, должен отобразиться индекс записей, а вы сможете создавать, изменять и удалять записи.

##<a id="next"></a>Дальнейшие действия

В этой статье вы изучили, как создать и опубликовать базовое приложение Rails на основе форм на виртуальной машине Azure с помощью Capistrano. Виртуальная машина использовала службы Unicorn и Nginx для обработки веб-запросов для приложения.

Более простой пример создания и развертывания приложения Rails на виртуальной машине Azure только с использованием SSH см. в разделе [Размещение веб-приложения Ruby on Rails с использованием виртуальной машины Linux][ruby-vm].

Дополнительные сведения о Ruby on Rails см. на веб-сайте [Руководства по Ruby on Rails][rails-guides].

Сведения об использовании пакета Azure SDK для Ruby для доступа к службам Azure из приложения Ruby см. в статьях:

* [Хранение неструктурированных данных с использованием BLOB-объектов][blobs]

* [Хранение пар "ключ/значение" с использованием таблиц][tables]

* [Обслуживание контента, передаваемого по каналам с высокой пропускной способностью, с помощью сети CDN][cdn-howto]

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


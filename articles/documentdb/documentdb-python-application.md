<properties
    pageTitle="Разработка веб-приложений Python Flask с использованием DocumentDB | Microsoft Azure"
    description="Изучите учебник по использованию DocumentDB для хранения и применения данных из веб-приложения Python Flask, размещенного в Azure. Найдите решения для разработки приложений." 
	keywords="Application development, database tutorial, python flask, python web application, python web development, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="python"
    authors="ryancrawcour"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="09/21/2015"
    ms.author="ryancraw"/>

# Разработка веб-приложений Python Flask с использованием DocumentDB

Чтобы показать, как клиенты могут эффективно использовать Azure DocumentDB для хранения и запроса документов JSON, в настоящем документе приведен комплексный учебник по созданию веб-приложения Python голосования с использованием Azure DocumentDB.

В этом учебнике показан пример использования службы DocumentDB, предоставляемой Azure, для хранения данных и доступа к ним из веб-приложения Python, размещенного в Azure. Этот пример предполагает, что у вас уже имеется некоторый опыт использования Python и веб-сайтов Azure.

В этом учебнике по базам данных рассматриваются следующие процедуры.

1. Создание и подготовка учетной записи DocumentDB.
2. Создание приложения Python MVC.
3. Подключение и использование Azure DocumentDB из веб-приложения.
4. Развертывание веб-приложения на веб-сайтах Azure.

В ходе учебника вы создадите простое веб-приложение для голосования, позволяющее проводить голосование на выборах.

![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого учебника](./media/documentdb-python-application/image1.png)


## Предварительные требования для учебника по базам данных

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

- [Visual Studio 2013](http://www.visualstudio.com/) или более поздней версии или Visual Studio Express (бесплатная версия Visual Studio).
- Средства Python для Visual Studio [отсюда][].
- Пакет SDK Azure для Visual Studio 2013 версии 2.4 и выше, который доступен [здесь][1].
- Python 2.7 [отсюда][2].
- Компилятор Microsoft Visual C++ для Python 2.7 [отсюда][3].

## Шаг 1. Создание учетной записи базы данных DocumentDB

Начнем с создания учетной записи DocumentDB. Если у вас уже есть учетная запись, можно сразу перейти к [Шагу 2. Создание нового веб-приложения Python Flask](#Step-2:-Create-a-new-Python-Flask-Web-Application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/> Теперь мы рассмотрим создание нового веб-приложения Python Flask с нуля.

## Шаг 2. Создание нового веб-приложения Python Flask

1. Откройте Visual Studio, выберите **Файл** -> **Новый проект** -> **Python** ->, **Веб-проект Flask** и создайте новый проект с именем **tutorial**.

	Для тех, кто не знаком с Python Flask: это платформа, ускоряющая создание веб-приложений в Python. [Учебники по Flask доступны здесь][].

	![Снимок экрана: окно нового проекта в Visual Studio с выделенным типом Python в левой части, выбранным веб-проектом Python Flask в центральной части и названием учебника в поле "Имя"](./media/documentdb-python-application/image9.png)

2. Будет выведен запрос на установку внешних пакетов. Выберите вариант **Установить в виртуальной среде**. В качестве базовой среды необходимо использовать Python 2.7, поскольку PyDocumentDB на данный момент не поддерживает Python 3.x. Для вашего проекта будет настроена требуемая виртуальная среда Python.

	![Снимок экрана: учебник — окно средств Python для Visual Studio](./media/documentdb-python-application/image10.png)


## Шаг 3. Изменение веб-приложения Python Flask

### Добавление пакетов Python Flask к вашему проекту

После настройки параметров проекта нужно добавить к проекту ряд необходимых пакетов Flask, включая pydocumentdb — пакет Python для DocumentDB.

1. Откройте файл с именем **requirements.txt** и замените его содержимое следующим:

    	flask==0.9
    	flask-mail==0.7.6
    	sqlalchemy==0.7.9
    	flask-sqlalchemy==0.16
    	sqlalchemy-migrate==0.7.2
    	flask-whooshalchemy==0.55a
    	flask-wtf==0.8.4
    	pytz==2013b
    	flask-babel==0.8
    	flup
    	pydocumentdb>=1.0.0

2. Нажмите правой кнопкой мыши **env** и выберите пункт **Установить из файла requirements.txt**.

	![Снимок экрана: отображение env (Python 2.7) с выбранной командой "Установить из requirements.txt", выделенной в списке](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE]В редких случаях в окне вывода появляется ошибка. В этом случае проверьте, связана ли ошибка с очисткой. Иногда происходит сбой очистки, но установка все равно завершается успешно (прокрутите окно вывода вверх, чтобы убедиться в этом). <a name="verify-the-virtual-environment"></a> Если это произойдет, нажмите кнопку "ОК" для продолжения.


### Проверка виртуальной среды

Убедимся, что все установлено правильно.

- Запустите веб-сайт, нажав клавишу **F5**. При этом будут запущены сервер разработки Flask и веб-браузер. Вы должны увидеть следующую страницу:

	![Пустой проект веб-приложения Python Flask отображается в браузере](./media/documentdb-python-application/image12.png)

### Создание определений базы данных, коллекции и документа

Теперь создадим наше приложение для голосования.

- Добавьте файл Python, щелкнув правой кнопкой мыши папку с именем **tutorial** в обозревателе решений. Задайте имя файла **forms.py**.  

    	from flask.ext.wtf import Form
    	from wtforms import RadioField

    	class VoteForm(Form):
        	deploy_preference  = RadioField('Deployment Preference', choices=[
            	('Web Site', 'Web Site'),
            	('Cloud Service', 'Cloud Service'),
            	('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Добавьте необходимые файлы импорта в views.py.

- Добавьте следующие инструкции import в начало файла **views.py**. Они выполнят импорт пакета SDK для Python и пакетов Flask DocumentDB.

    	from forms import VoteForm
    	import config
    	import pydocumentdb.document_client as document_client


### Создание базы данных, коллекции и документа

- Добавьте следующий код в файл **views.py**. Он отвечает за создание базы данных, используемой формой. Не удаляйте код, который уже есть в файле **views.py**. Просто переместите его в конец.

    	@app.route('/create')
    	def create():
        	"""Renders the contact page."""
        	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        	# Attempt to delete the database.  This allows this to be used to recreate as well as create
        	try:
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            	client.DeleteDatabase(db['_self'])
        	except:
            	pass

       		# Create database
        	db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        	# Create collection
        	collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION }, { 'offerType': 'S1' })
        	# Create document
        	document = client.CreateDocument(collection['_self'],
            	{ 'id': config.DOCUMENTDB_DOCUMENT,
            	'Web Site': 0,
            	'Cloud Service': 0,
            	'Virtual Machine': 0,
            	'name': config.DOCUMENTDB_DOCUMENT })

        	return render_template(
            	'create.html',
            	title='Create Page',
            	year=datetime.now().year,
            	message='You just created a new database, collection, and document.  Your old votes have been deleted')

> [AZURE.TIP]Метод **CreateCollection** имеет третий необязательный параметр **RequestOptions**. Его можно использовать для указания типа предложения коллекции. Если значение типа предложения не указано, будет создана коллекция с использованием типа предложения по умолчанию. Дополнительные сведения о типах предложений DocumentDB см. в статье [Уровни производительности DocumentDB](documentdb-performance-levels.md).
>
### Чтение базы данных, коллекции и документа и отправка формы

- Добавьте следующий код в файл **views.py**. Он служит для настройки формы, чтения базы данных, коллекции и документа. Не удаляйте код, существующий в файле **views.py**. Просто переместите его в конец.

    	@app.route('/vote', methods=['GET', 'POST'])
    	def vote():
        	form = VoteForm()
        	replaced_document ={}
        	if form.validate_on_submit(): # is user submitted vote  
            	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

            	# Read databases and take the first since the id should not be duplicated.
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

            	# Read collections and take the first since the id should not be duplicated.
            	coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

            	# Read documents and take the first since the id should not be duplicated.
            	doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

            	# Take the data from the deploy_preference and increment your database
            	doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            	replaced_document = client.ReplaceDocument(doc['_self'], doc)

            	# Create a model to pass to results.html
            	class VoteObject:
                	choices = dict()
                	total_votes = 0

            	vote_object = VoteObject()
            	vote_object.choices = {
                	"Web Site" : doc['Web Site'],
                	"Cloud Service" : doc['Cloud Service'],
                	"Virtual Machine" : doc['Virtual Machine']
            	}
            	vote_object.total_votes = sum(vote_object.choices.values())

            	return render_template(
                	'results.html',
                	year=datetime.now().year,
                	vote_object = vote_object)

        	else :
            	return render_template(
                	'vote.html',
                	title = 'Vote',
                	year=datetime.now().year,
                	form = form)


### Создание HTML-файлов

В папку "Шаблоны" добавьте такие HTML-файлы: create.html, results.html, vote.html.

1. Добавьте следующий код в файл **create.html**. Он служит для отображения сообщения о создании новой базы данных, коллекции и документа.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>{{ title }}.</h2>
    	<h3>{{ message }}</h3>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}

2. Добавьте следующий код в файл **results.html**. Он служит для отображения результатов опроса.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Results of the vote</h2>
   	 	<br />

    	{% for choice in vote_object.choices %}
    	<div class="row">
        	<div class="col-sm-5">{{choice}}</div>
        	<div class="col-sm-5">
            	<div class="progress">
                	<div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0"
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    	{{vote_object.choices[choice]}}
                	</div>
            	</div>
        	</div>
    	</div>
    	{% endfor %}

    	<br />
    	<a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    	{% endblock %}

3. Добавьте следующий код в файл **vote.html**. Он служит для отображения опроса и принимает голоса. При регистрации голосов управление передается файлу views.py, в котором голоса будут учитываться и, соответственно, добавляться в документ.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>What is your favorite way to host an application on Azure?</h2>
    	<form action="" method="post" name="vote">
        	{{form.hidden_tag()}}
        	{{form.deploy_preference}}
        	<button class="btn btn-primary" type="submit">Vote</button>
    	</form>
    	{% endblock %}

4. Замените содержимое файла **index.html** на приведенный ниже код. Он будет служить главной страницей приложения.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Python + DocumentDB Voting Application.</h2>
    	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}


### Добавьте файл конфигурации и измените файл \_\_init\_\_.py

1. Нажмите правой кнопкой мыши по имени проекта и добавьте файл **config.py**. Этот файл необходим для работы форм в Flask. Кроме того, его можно использовать для предоставления секретного ключа. В этом учебнике секретный ключ не требуется.

2. Добавьте следующий код в файл config.py. Измените значения **DOCUMENTDB\_HOST** и **DOCUMENTDB\_KEY**.

    	CSRF_ENABLED = True
    	SECRET_KEY = 'you-will-never-guess'

    	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='

    	DOCUMENTDB_DATABASE = 'voting database'
    	DOCUMENTDB_COLLECTION = 'voting collection'
    	DOCUMENTDB_DOCUMENT = 'voting document'

3. Аналогичным образом замените содержимое файла **\_\_init\_\_.py** на следующий код.

    	from flask import Flask
    	app = Flask(__name__)
    	app.config.from_object('config')
    	import tutorial.views

4. После выполнения указанных выше шагов ваш обозреватель решений должен выглядеть примерно так:

	![Снимок экрана: окно обозревателя решений Visual Studio](./media/documentdb-python-application/image15.png)


## Шаг 4. Локальный запуск веб-приложения

1. Нажмите клавишу F5 или кнопку **Запустить** в Visual Studio, после чего на экране должно появиться следующее.

	![Снимок экрана: Python + приложение для голосования DocumentDB в веб-браузере](./media/documentdb-python-application/image16.png)

2. Нажмите **Создать или очистить базу данных голосования**, чтобы создать базу данных.

	![Снимок экрана: страница создания веб-приложения — сведения о разработке](./media/documentdb-python-application/image17.png)

3. Затем нажмите кнопку **Голосовать** и сделайте свой выбор.

	![Снимок экрана: веб-приложение с заданным вопросом для голосования](./media/documentdb-python-application/image18.png)

4. Для каждого учтенного голоса будет увеличиваться соответствующий счетчик.

	![Снимок экрана: страница с результатами голосования](./media/documentdb-python-application/image19.png)


## Шаг 5. Развертывание веб-приложения на веб-сайтах Azure

Теперь, когда у вас есть готовое приложение и оно корректно работает в DocumentDB, мы развернем его на веб-сайтах Azure.

1. Правой кнопкой мыши нажмите проект в обозревателе решений (убедившись, что он не запущен локально) и выберите пункт **Опубликовать**. Затем выберите **Веб-сайты Microsoft Azure**.

 	![Снимок экрана: проект tutorial, выбранный в обозревателе решений и с выделенным параметром "Опубликовать"](./media/documentdb-python-application/image20.png)

2. Настройте веб-сайт Azure, введя свои учетные данные, и нажмите кнопку **Опубликовать**.

	![Снимок экрана: окно "Публикация веб-сайта"](./media/documentdb-python-application/image21.png)

3. Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

## Дальнейшие действия

Поздравляем! Вы только что закончили свое первое веб-приложение на Python с помощью Azure DocumentDB и опубликовали его в службе веб-сайтов Azure.

Мы часто обновляем и улучшаем этот раздел на основе ваших отзывов. После завершения учебника воспользуйтесь кнопками голосования в верхней и нижней части страницы, а также оставьте отзыв о том, что следует улучшить по вашему мнению. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

Для добавления дополнительной функциональности в веб-приложение ознакомьтесь с доступными интерфейсами API в [пакете SDK Python для DocumentDB](https://pypi.python.org/pypi/pydocumentdb).

Дополнительные сведения см. в [Центре разработчика Python](/develop/python/).


  [Учебники по Flask доступны здесь]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [отсюда]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure portal]: http://portal.azure.com

<!---HONumber=Oct15_HO1-->
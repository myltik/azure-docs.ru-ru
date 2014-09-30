<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong"></tags>

# <a name="_Toc395888515"></a><a name="_Toc395809324">Создание веб-приложения Python и Flask (MVC) с использованием DocumentDB</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Обзор</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">Сценарий</a>

Чтобы показать, как клиенты могут эффективно использовать Azure DocumentDB для
хранения и запросов документов JSON, в настоящем документе приведена пошаговая
инструкция от начала до конца для построения веб-приложения «голосование», используя Azure Document
DB.

В этом пошаговом руководстве показан пример использования служб DocumentDB, предоставляемых
Azure, для хранения и доступа к данным из веб-приложения Python
, размещенного в Azure. Предполагается, что у вас имеется некоторый опыт использования
Python и веб-сайтов Azure.

Вы узнаете:

1. Создание и подготовка учетной записи DocumentDB

2. Создание приложения Python MVC

3. Подключение и использование Azure DocumentDB из веб-приложения

4. Развертывание веб-приложения на веб-сайтах Azure

В ходе учебного курса вы создадите простое веб-приложение
для голосования, позволяющее проводить голосование на выборах.

![Alt text][]

<a name="_Toc395888520"></a><a name="_Toc395809329">Предварительные требования</a>

Перед выполнением инструкций, приведенных в этой статье, следует убедиться,
что установлены следующие компоненты:

Visual Studio 2013 (или [Visual Studio Express][], бесплатная
версия)

Средства Python для Visual Studio [отсюда][]

Пакет Azure SDK для Visual Studio 2013, версия 2.4 и выше
[отсюда][1]

Межплатформенные средства командной строки Azure, доступны в [Установщике веб-платформы
Майкрософт][]

# <a name="_Toc395888519"></a><a name="_Toc395809328">Создание учетной записи базы данных DocumentDB</a>

Для подготовки учетной записи базы данных DocumentDB в Azure, откройте
портал управления Azure и либо щелкните плитку «Коллекция Azure» на
домашней странице, либо нажмите «+» в левом нижнем углу экрана.

![Alt text][2]

Откроется коллекция Azure, где можно будет выбрать из множества
доступных служб Azure. В коллекции выберите «Данные, хранение и
резервное копирование» из списка категорий.

![Alt text][3]

Здесь, выберите параметр для Azure DocumentDB

![Alt text][4]

Затем нажмите «Создать» в нижней части экрана

![Alt text][5]

Откроется выноска «Новая DocumentDB», где можно будет задать
имя, регион, масштаб, группу ресурсов и прочие настройки новой
учетной записи.

![Alt text][6]

Как только вы закончите ввод значений для вашей учетной записи, нажмите кнопку «Создать»,
и начнется процесс подготовки создания учетной записи базы данных.
Когда процесс подготовки будет завершен, вы увидите уведомление,
которое появится в области уведомлений портала, а плитка на
экране запуска (если вы выбрали ее для создания) изменится, чтобы отобразить
выполненное действие.

![Alt text][7]

После завершения подготовки при нажатии плитки DocumentDB на
экране запуска появится главная выноска для этой вновь созданной
DocumentDB.

![Alt text][8]
![Alt text][9]

При помощи кнопки «Ключи», получите доступ к URL вашей конечной точки и первичному ключу,
скопируйте их в буфер обмена и держите их под рукой, так как мы будем использовать
эти значения в веб-приложении, которое создадим в последующем.

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">Создание нового веб-приложения Python Flask</a>

Откройте Visual Studio, выберите «Файл»/«Новый проект», веб-приложение Flask
 с именем **tutorial**. Вам будет задан вопрос, хотите ли вы
установить внешние пакеты? Выберите «Установить в виртуальную среду»
и нажмите «Создать» Будет настроена требуемая виртуальная среда Python
для вашего проекта.

Для тех, кто плохо знаком с Flask: это веб-фреймворк, который позволяет ускорить разработку веб
-приложений на Python. [Учебники по Flask доступны здесь][].

![Alt text][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">Добавление пакетов Flask в ваш проект</a>

После того как ваш проект будет настроен, необходимо добавить определенные пакеты Flask, которые понадобятся
нам для нашего проекта (например, формы). Щелкните правой кнопкой мыши на **env**
 и выберите **Установить пакеты Python (этот шаг очень
важен)**:

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text][11]

**Примечание.**Примечание. В редких случаях в окне вывода появляется ошибка. В
этом случае проверьте, связана ли ошибка с очисткой. Иногда происходит сбой
очистки, но установка по-прежнему успешно выполняется (прокрутите вверх
окно вывода, чтобы убедиться в этом).
<a name="verify-the-virtual-environment"></a> Если это произошло, то можно
продолжать.

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">Проверка виртуальной среды</a>

Убедимся, что все установлено правильно. Запустите веб-сайт
, нажав клавишу **F5**. Запустится сервер разработки flask
, а также веб-браузер. Вы должны увидеть следующую страницу:

![Alt text][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">Добавление в проект DocumentDB</a>

Пакет Python SDK для DocumentDB размещен в PyPi и может быть установлен с помощью
pip.

В обозревателе решений щелкните правой кнопкой мыши «Среды Python»
и выберите «Установить пакет Python...».

![Alt text][13]

Введите «--pre pydocumentdb», это имя пакета PyPi. При необходимости можно
указать известную версию, если вы хотите контролировать, какую версию
вы хотите установить, если версия не указана, то будет установлена последняя
стабильная версия. Обратите внимание, что необходимо ввести
имя «--pre pydocumentdb» полностью.

![Alt text][14]

Будет загружен и установлен пакет pydocumentdb в вашу
среду разработки, после завершения вы должны увидеть pydocumentdb в списке
модулей среды.

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">Создание базы данных, коллекции и определения документа</a>

Добавьте в папку
tutorial в обозревателе решений новый файл **forms.py**. Добавьте следующий код в
forms.py. Мы создаем приложение для голосования. Это будет реализовано путем
создания трех логических полей, которые будут переключаться в зависимости от пользовательского ввода.


    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">Создание базы данных, коллекции и документа</a>

Примечание. Считается более безопасным хранить учетные данные для проверки подлинности
в конфигурационном файле, нежели непосредственно в приложении. В целях упрощения мы
разместим их в исходном коде. Создайте новую функцию в файле views.py и назовите ее
Добавьте в файл**views.py**. Не удаляйте
существующий код.


    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

# <a name="_Toc395888529"></a><a name="_Toc395809338">Учитываем голос пользователя и обновляем документ</a>

### <a name="_Toc395809338">Добавление в раздел импорта требуемых элементов

# <a name="_Toc395888529"></a>

Добавьте следующие инструкции import в начало файла **views.py**. Они служат для импорта
пакетов Python SDK и Flask для DocumentDB.

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Чтение базы данных, коллекции и документа</a>

Добавьте следующий код в файл **views.py**. Он служит для настройки
формы, чтения из базы данных, коллекции и документа. Не удаляйте существующий
код в views.py. Просто добавьте это в конец.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document

### <a name="_Toc395809338">Регистрация голоса и модификация документа</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Отображение результатов</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')

### <a name="_Toc395809338">Создание файлов html</a>

В папку с шаблонами добавьте следующие файлы html.
create.html, results.html, vote.html

Добавьте следующий код в файл **create.html**. Он служит для отображения
сообщения о создании новой базы данных, коллекции и документа.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Добавьте следующий код в файл **results.html**. Он отображает результаты
опроса.

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Добавьте следующий код в файл **vote.html**. Он отображает результаты
опроса и принимает голоса. При регистрации голоса управление
передается к views.py, где мы будем признавать голосование состоявшимся и
добавлять данные в документ.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}

# <a name="_Toc395888529"></a>

Удалите код в файле **layout.html** и замените на приведенный ниже. Он
служит для настройки панели навигации и соответствующих ссылок для маршрутизации.

Удалите код в файле index.html и замените на приведенный ниже. Он
будет служить главной страницей вашего приложения

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">Добавьте файл конфигурации и измените \_\_init\_\_.py</a>.

Щелкните правой кнопкой мыши на имени проекта и добавьте файл **config.py**.
Этот файл необходим для работы форм Flask. Также можно предоставить
секретный ключ. Добавьте следующий код в файл config.py

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Таким же образом допишите файл **\_\_init\_\_.py**. Он находится в
папке tutorial. Замените код на приведенный ниже. Он
обеспечивает соединение между представлениями и файлом config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

После выполнения указанных выше шагов ваш Обозреватель
решений должен выглядеть примерно так:

![Alt text][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Локальный запуск приложения</a>

Нажмите клавишу F5 или кнопку запуска в visual studio, и вы увидите
на экране следующую картину.

![Alt text][16]

Щелкните на голосе и сделайте выбор

![Alt text][17]

Для каждого учтенного голоса будет увеличиваться на единицу соответствующий счетчик. При
следующем голосовании можно будет просмотреть результаты.

![Alt text][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Разверните приложение на веб-сайтах Azure</a>

Теперь, когда у вас есть законченное приложение и оно работает правильно в
DocumentDB, мы собираемся развернуть его на веб-сайтах Azure. Нажмите правой кнопкой мыши
на проекте в обозревателе решений (убедитесь, что оно не запущено
локально) и выберите пункт «Опубликовать».

![Alt text][19]

Настройте веб-сайты Azure, введя свои учетные данные и создав
новый сайт либо повторно использовав существующий.

![Alt text][20]

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения
 и запустит браузер, где можно увидеть ваше творение,
запущенное в Azure!

</h1>
# <a name="_Toc395809338">Заключение</a>

Поздравляем! Вы только что создали свое первое приложение Python с использованием
Azure DocumentDB и опубликовали его на веб-сайтах Azure.

Скачать завершенное решение можно [здесь][]. (Примечание. Вам по-прежнему необходимо
добавить виртуальную среду, установить средства и
пакеты Python, как упоминалось выше)

</h1>

  [Alt text]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [отсюда]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Установщике веб-платформы Майкрософт]: http://www.microsoft.com/web/downloads/platform.aspx
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [Учебники по Flask доступны здесь]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [здесь]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409

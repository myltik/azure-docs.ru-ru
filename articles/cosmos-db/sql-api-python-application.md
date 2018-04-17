---
title: Руководство по веб-приложению Python Flask для Azure Cosmos DB | Документация Майкрософт
description: Изучите руководство по использованию Azure Cosmos DB для хранения и применения данных из веб-приложения Python Flask, размещенного в Azure. Найдите решения для разработки приложений.
keywords: Разработка приложений, Python Flask, веб-приложение Python, разработка веб-приложения Python
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/23/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 333d34e2c7b07b1ec09d1375d4d05075ed7dde0d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Создание веб-приложения Python Flask с использованием Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

В этом руководстве показано, как использовать Azure Cosmos DB для хранения данных и обеспечения доступа к ним из веб-приложения Python Flask, размещенного в службе приложений Azure. Предполагается, что у вас есть некоторый опыт использования Python и веб-сайтов Azure.

В этом учебнике по базам данных рассматриваются следующие процедуры.

1. Создание и подготовка учетной записи Azure Cosmos DB.
2. Создание приложения Python Flask.
3. Подключение и использование Azure Cosmos DB из веб-приложения.
4. Развертывание веб-приложения в службе приложений Azure.

В ходе учебника вы создадите простое веб-приложение для голосования, позволяющее проводить голосование на выборах.

![Снимок экрана: веб-приложение для голосования, созданное с помощью этого учебника](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Предварительные требования для учебника по базам данных
Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* [Подписка Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) с включенными компонентами **Разработка для Azure** и **Разработка на Python**. Можно проверить, установлены ли необходимые компоненты, и установить их, открыв **установщик Visual Studio** локально.   
* [Пакет Microsoft Azure SDK для Python 2.7](https://azure.microsoft.com/downloads/) 
* [Python 2.7](https://www.python.org/downloads/windows/) Можно использовать 32-разрядную или 64-разрядную установку.

> [!IMPORTANT]
> Если вы устанавливаете Python 2.7 впервые, убедитесь, что на экране Customize Python 2.7.13 (Настройка Python 2.7.13) вы выбрали **Add python.exe to Path** (Добавить файл python.exe к пути).
> 
> ![Снимок экрана: окно «Настройка Python 2.7.11», где необходимо добавить файл python.exe к пути](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Компилятор Microsoft Visual C++ для Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266)

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Шаг 1. Создание учетной записи базы данных Azure Cosmos DB
Давайте сначала создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись или вы используете эмулятор Azure Cosmos DB в этом руководстве, можно перейти к разделу [Шаг 2. Создание веб-приложения Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Теперь мы рассмотрим создание веб-приложения Python Flask с нуля.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Шаг 2. Создание веб-приложения Python Flask
1. В меню Visual Studio **Файл** выберите **Создать**, а затем щелкните **Проект**.
   
    Откроется диалоговое окно **Новый проект** .
2. На панели слева разверните пункты **Шаблоны** и **Python**, а затем выберите **Веб**. 
3. На центральной панели выберите **веб-проект Flask**, в поле **Имя** введите **tutorial**, а затем нажмите кнопку **ОК**. Помните, что в именах пакетов Python используется только нижний регистр, как описано в [руководстве по стилю для кода Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Для тех, кто не знаком с Python Flask: это платформа, ускоряющая создание веб-приложений в Python.
   
    ![Снимок экрана: окно нового проекта в Visual Studio с выделенным типом Python в левой части, выбранным веб-проектом Python Flask в центральной части и названием учебника в поле "Имя"](./media/sql-api-python-application/image9.png)
4. В окне **Инструменты Python для Visual Studio** щелкните **Install into a virtual environment** (Установить в виртуальной среде). 
   
    ![Снимок экрана: учебник — окно средств Python для Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. В окне **Добавление виртуального окружения** выберите Python 2.7 или 3.5 в поле выбора интерпретатора, примите остальные значения по умолчанию и нажмите кнопку **Создать**. Для вашего проекта будет настроена требуемая виртуальная среда Python.
   
    ![Снимок экрана: учебник — окно средств Python для Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    Когда среда будет успешно установлена, в окне вывода отобразится сообщение `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`

## <a name="step-3-modify-the-python-flask-web-application"></a>Шаг 3. Изменение веб-приложения Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Добавление пакетов Python Flask в проект
После настройки параметров проекта нужно добавить к нему ряд необходимых пакетов Flask, включая pydocumentdb — пакет Python для API SQL Azure Cosmos DB.

1. В обозревателе решений откройте файл **requirements.txt** и замените его содержимое приведенным ниже кодом.
   
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
2. Сохраните файл **requirements.txt** . 
3. В обозревателе решений щелкните элемент **env** правой кнопкой мыши и выберите пункт **Install from requirements.txt** (Установить из файла requirements.txt).
   
    ![Снимок экрана: отображение env (Python 2.7) с выбранной командой "Установить из requirements.txt", выделенной в списке](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    После успешной установки в окне вывода отобразятся следующие сведения:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > В редких случаях в окне вывода появляется ошибка. В этом случае проверьте, связана ли ошибка с очисткой. Иногда происходит сбой очистки, но установка все равно завершается успешно (прокрутите окно вывода вверх, чтобы убедиться в этом). Чтобы определить успешность установки, воспользуйтесь [проверкой виртуальной среды](#verify-the-virtual-environment). Если установка завершилась ошибкой, но проверка прошла успешно, можно продолжать работу.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Проверка виртуальной среды
Убедимся, что все установлено правильно.

1. Выполните сборку решения, нажав клавиши **CTRL**+**SHIFT**+**B**.
2. После успешной сборки запустите веб-сайт, нажав клавишу **F5**. Будут запущены сервер разработки Flask и веб-браузер. Вы должны увидеть следующую страницу:
   
    ![Пустой проект веб-приложения Python Flask отображается в браузере](./media/sql-api-python-application/image12.png)
3. Остановите отладку веб-сайта в Visual Studio. Для этого нажмите клавиши **SHIFT**+**F5**.

### <a name="create-database-collection-and-document-definitions"></a>Создание определений базы данных, коллекции и документа
Теперь давайте создадим приложение для голосования, добавив новые файлы и обновив остальные.

1. В обозревателе решений щелкните правой кнопкой мыши проект **tutorial**, выберите пункт **Добавить**, а затем щелкните **Новый элемент**. Выберите элемент **Empty Python File** (Пустой файл Python) и присвойте файлу имя **forms.py**.  
2. Добавьте следующий код в файл forms.py и сохраните файл.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Добавьте необходимые файлы импорта в views.py.
1. В обозревателе решений разверните папку **tutorial** и откройте файл **views.py**. 
2. Добавьте в начало файла **views.py** приведенные ниже инструкции import и сохраните файл. Они выполнят импорт пакета SDK для Python и пакетов Flask Azure Cosmos DB.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Создание базы данных, коллекции и документа
* Добавьте в конец файла **views.py** приведенный ниже код. Он отвечает за создание базы данных, используемой формой. Не удаляйте существующий код в файле **views.py**. Просто переместите его в конец.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Чтение базы данных, коллекции и документа и отправка формы
* Добавьте в конец файла **views.py** приведенный ниже код. Он служит для настройки формы, чтения базы данных, коллекции и документа. Не удаляйте существующий код в файле **views.py**. Просто переместите его в конец.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
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
```


### <a name="create-the-html-files"></a>Создание HTML-файлов
1. В обозревателе решений в папке **tutorial** щелкните правой кнопкой мыши папку **templates**, выберите пункт **Добавить**, а затем — **Новый элемент**. 
2. Выберите **HTML-страница**, а затем в поле "Имя" введите **create.html**. 
3. Повторите шаги 1 и 2, чтобы создать два дополнительных HTML-файла: results.html и vote.html.
4. Добавьте следующий код в файл **create.html** in the `<body>` . В нем будет отображаться сообщение о создании новой базы данных, коллекции и документа.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Добавьте следующий код в элемент `<body`>. файла **results.html**. В нем будут отображаться результаты опроса.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Добавьте следующий код в элемент `<body`>. файла **vote.html**. В нем будет отображаться опрос и приниматься голоса. При регистрации голосов управление передается файлу views.py, в котором Azure Cosmos DB будет учитывать голоса и, соответственно, добавлять в документ.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. В папке **templates** замените содержимое файла **index.html** приведенным ниже кодом. Он будет служить главной страницей приложения.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Добавьте файл конфигурации и измените файл \_\_init\_\_.py
1. В обозревателе решений щелкните правой кнопкой мыши проект **tutorial**, выберите пункты **Добавить**, **Новый элемент** и **Empty Python File** (Пустой файл Python), а затем введите имя файла **config_cosmos.py**. Этот файл необходим для работы форм в Flask. Кроме того, его можно использовать для предоставления секретного ключа. В этом учебнике секретный ключ не требуется.
2. Добавьте приведенный ниже код в файл config_cosmos.py. На следующем этапе вам потребуется изменить значения свойств **COSMOSDB\_HOST** и **COSMOSDB\_KEY**.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. На [портале Azure](https://portal.azure.com/) перейдите на страницу **Ключи**, щелкните **Обзор**, **Azure Cosmos DB Accounts** (Учетные записи Azure Cosmos DB), дважды щелкните имя учетной записи и нажмите кнопку **Ключи** в области **Основные компоненты**. На странице **Ключи** скопируйте значение **URI** и вставьте его в файл **config.py** как значение для свойства **COSMOSDB\_HOST**. 
4. Вернитесь на портал Azure, перейдите на страницу **Ключи**, скопируйте значение **первичного** или **вторичного ключа** и вставьте его в файл **config_cosmos.py** как значение для свойства **COSMOSDB\_KEY**.
5. Добавьте следующие строки в файл **\_\_init\_\_.py**, чтобы включить чтение файла конфигурации и основное ведение файлов: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    В результате содержимое файла будет выглядеть так:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. После добавления всех файлов обозреватель решений должен выглядеть следующим образом:
   
    ![Снимок экрана: окно обозревателя решений Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Шаг 4. Локальный запуск веб-приложения
1. Выполните сборку решения, нажав клавиши **CTRL**+**SHIFT**+**B**.
2. После успешной сборки запустите веб-сайт, нажав клавишу **F5**. На экране должно отображаться следующее:
   
    ![Снимок экрана: Python + приложение для голосования Azure Cosmos DB в веб-браузере](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Щелкните **Создать или очистить базу данных голосования** , чтобы создать базу данных.
   
    ![Снимок экрана: страница создания веб-приложения — сведения о разработке](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Затем нажмите кнопку **Голосовать** и сделайте свой выбор.
   
    ![Снимок экрана: веб-приложение с заданным вопросом для голосования](./media/sql-api-python-application/cosmos-db-vote.png)
5. Для каждого учтенного голоса будет увеличиваться соответствующий счетчик.
   
    ![Снимок экрана: страница с результатами голосования](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Остановите отладку проекта, нажав клавиши SHIFT+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Шаг 5. Развертывание веб-приложения в Azure
Теперь, когда у вас есть готовое приложение и оно корректно работает в Azure Cosmos DB локально, необходимо создать файл web.config, обновить файлы на сервере в соответствии с локальной средой и просмотреть завершенное приложение в Azure. Эта процедура относится только к Visual Studio 2017. Если вы используете другую версию Visual Studio, ознакомьтесь со статьей [Публикация в службу приложений Azure](/visualstudio/python/publishing-to-azure.md).

1. В **обозревателе решений** Visual Studio щелкните проект правой кнопкой мыши и выберите **Добавить > Новый элемент...** В появившемся диалоговом окне выберите шаблон **Azure web.config (FastCGI)**, а затем нажмите кнопку **ОК**. В корневом каталоге проекта будет создан файл `web.config`. 

2. Измените раздел `<system.webServer>` в файле `web.config` так, чтобы путь совпадал с установкой Python. Например, для Python 2.7 (64-разрядная версия) запись должна выглядеть следующим образом:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Задайте для записи `WSGI_HANDLER` в файле `web.config` значение `tutorial.app` в соответствии с именем проекта. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. В **обозревателе решений** Visual Studio разверните папку **tutorial**, щелкните правой кнопкой мыши папку `static`, выберите **Добавить > Новый элемент...**, а затем — шаблон "Файл web.config статических файлов Azure" и нажмите кнопку **ОК**. Это действие создает еще один файл `web.config` в папке `static`, который отключает обработку Python для этой папки. Эта конфигурация отправляет запросы к статическим файлам на веб-сервер по умолчанию, а не с помощью приложения Python.

5. Сохраните файлы, а затем правой кнопкой мыши щелкните проект в обозревателе решений (перед этим убедитесь, что он не запущен локально) и выберите пункт **Опубликовать**.  
   
     ![Снимок экрана: проект tutorial, выбранный в обозревателе решений и с выделенным параметром "Опубликовать"](./media/sql-api-python-application/image20.png)
6. В диалоговом окне **Публикация** установите флажок **Служба приложений Microsoft Azure**, выберите **Создать** и нажмите **Опубликовать**.
   
    ![Снимок экрана с диалоговым окном "Публикация веб-сайта" с выделенным флажком "Служба приложений Microsoft Azure"](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. В окне **Создать службу приложений** введите имя веб-приложения и соответствующую **подписку**, **группу ресурсов** и **план службы приложений**, а затем нажмите кнопку **Создать**.
   
    ![Снимок экрана окна "Веб-приложения Microsoft Azure"](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. Через несколько секунд Visual Studio завершит копирование файлов на сервер и отобразит сообщение "Невозможно отобразить эту страницу ввиду того, что произошла внутренняя ошибка сервера" на странице `http://<your app service>.azurewebsites.net/`.

9. На портале Azure откройте новую учетную запись службы приложений, а затем в меню навигации прокрутите вниз до раздела **Средства разработки**, выберите **Расширения** и нажмите кнопку **+ Добавить**.

10. На странице **Выберите расширение** прокрутите вниз к последней установке Python 2.7 и выберите параметр x86 или x64 бит, а затем нажмите кнопку **ОК**, чтобы принять условия использования.  
   
11. С помощью консоли Kudu, к которой можно перейти по адресу `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, установите пакеты, указанные в файле `requirements.txt` приложения. Для этого в консоли диагностики Kudu перейдите к папке Python `D:\home\Python27`, а затем выполните следующую команду, как описано в разделе о [консоли Kudu](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Перезапустите службу приложений на портале Azure после установки новых пакетов, нажав кнопку **Перезагрузить**. 

    > [!Tip] 
    > Если какие-либо изменения внесены в файл `requirements.txt` приложения, не забудьте снова использовать консоль Kudu для установки новых пакетов, перечисленных в файле. 

13. После полной настройки среды сервера обновите страницу в браузере. Вы должны увидеть веб-приложение.

    ![Результаты публикации приложений Bottle, Flask и Django в службе приложений](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Если веб-страница не появляется или появляется сообщение "Невозможно отобразить эту страницу ввиду того, что произошла внутренняя ошибка сервера", откройте файл web.config в Kudo и в разделе system.webServer добавьте ` <httpErrors errorMode="Detailed"></httpErrors>`, а затем обновите страницу. После этого в браузере будут выведены подробные сведения об ошибке. 

## <a name="troubleshooting"></a>Устранение неполадок
Если это первое приложение Python, которое вы запускаете на компьютере, убедитесь, что следующие папки (или эквивалентные расположения установки) включены в переменную PATH:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Если на странице голосования появляется сообщение об ошибке, а имя проекта отличается от **tutorial**, убедитесь, что файл **\_\_init\_\_.py** ссылается на имя нужного проекта в строке `import tutorial.view`.

## <a name="next-steps"></a>Дополнительная информация
Поздравляем! Вы только что закончили создание первого веб-приложения на Python с помощью Azure Cosmos DB и опубликовали его в Azure.

Чтобы узнать, как добавить дополнительные функции в веб-приложение, ознакомьтесь с доступными API в [пакете SDK для Python в Azure Cosmos DB](sql-api-sdk-python.md).

Дополнительные сведения об Azure, Visual Studio и Python см. в [центре по разработке для Python](https://azure.microsoft.com/develop/python/). 

Дополнительные руководства по Python Flask: [Мегаруководство по Flask, часть I. Привет, мир!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com

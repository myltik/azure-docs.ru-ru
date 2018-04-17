---
title: Создание веб-приложения Flask с использованием Python и API MongoDB в Azure Cosmos DB | Документация Майкрософт
description: В этой статье представлен пример кода Python Flask, который можно использовать для подключения и выполнения запросов к API MongoDB в Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: heatherbshapiro
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: 7fd25a1181e1a4d69bf8233b34a4c416378e0181
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB. Создание приложения Flask с использованием API MongoDB

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

В этом кратком руководстве используется следующий [пример Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) и демонстрируется, как создать простое приложение Flask с помощью [эмулятора Azure Cosmos DB](/local-emulator.md) и [API MongoDB](mongodb-introduction.md) Azure Cosmos DB вместо MongoDB.

## <a name="prerequisites"></a>предварительным требованиям

- Скачайте [эмулятор Azure Cosmos DB](/local-emulator.md). Сейчас эмулятор поддерживается только для Windows. В примере показано, как использовать образец с рабочим ключом от Azure, и это можно сделать на любой платформе.

- Если у вас еще не установлено средство Visual Studio Code, [его](https://code.visualstudio.com/Download) можно быстро установить для вашей платформы (Windows, Mac, Linux).

- Не забудьте добавить поддержку языка Python, установив одно из распространенных расширений Python.
    1. Выберите расширение.
    2. Установите расширение, введя `ext install` в палитре команд (`Ctrl+Shift+P`).

    Примеры из этого документа используют все возможности популярного [расширения Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) от пользователя DonJayamanne.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение Flask-MongoDB API с GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом.

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.
2. Выполните команду ниже, чтобы клонировать репозиторий с примером.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Для установки модулей python выполните следующую команду.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Откройте папку в Visual Studio Code.

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл **app.py** в корневом каталоге, и вы увидите, что эти строки кода создают соединения Azure Cosmos DB. Следующий код использует строку соединения для локального эмулятора Azure Cosmos DB. Как показано ниже, пароль необходимо разделить, чтобы адаптироваться к косым чертам, которые невозможно проанализировать иным образом.

* Запустите клиент MongoDB, извлеките базу данных и пройдите проверку подлинности.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Получите коллекцию или создайте ее, если ее еще не существует.

    ```python
    todos = db.todo #Select the collection
    ```

* Создание приложения

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Запуск веб-приложения

1. Убедитесь, что эмулятор Azure Cosmos DB работает.

2. Откройте окно терминала и подключитесь (`cd`) к каталогу, в котором будет сохранено приложение.

3. Если вы используете Mac, задайте переменную среды для приложения Flask `set FLASK_APP=app.py` или `export FLASK_APP=app.py`.

4. Запустите приложение с `flask run` и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Добавьте и удалите задачи в коллекции и отследите их добавление и изменение в коллекции.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Обновление строки подключения

Если вы хотите проверить код в работающей учетной записи Azure Cosmos DB, тогда перейдите на портал Azure, чтобы создать учетную запись и получить данные строк подключения. Затем скопируйте их в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Строка подключения**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать имя пользователя, пароль и узел в файл Dal.cs.

2. Откройте в корневом каталоге файл**app.py**.

3. Скопируйте **имя пользователя** c портала (с помощью кнопки копирования) и добавьте его в качестве значения параметра **name** в файле **app.py**.

4. Затем скопируйте **строку подключения** с портала и настройте ее в качестве значения MongoClient в файле **app.py**.

5. И наконец, скопируйте **пароль** с портала и добавьте его в качестве значения параметра **password** в файле **app.py**.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. Это приложение можно запустить так же, как и прежде.

## <a name="deploy-to-azure"></a>Развернуть в Azure

Чтобы развернуть это приложение, можно создать веб-приложение в Azure и включить непрерывное развертывание с ветвления в репозитории github. Для настройки непрерывного развертывания с Github в Azure используйте руководство [Непрерывное развертывание в службе приложений Azure](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment).

При развертывании в Azure следует удалить ключи приложения и убедиться, что приведенный ниже раздел не закомментирован:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Затем к параметрам приложения необходимо добавить MONGOURL, MONGO_PASSWORD и MONGO_USERNAME. Дополнительные сведения о параметрах приложений в веб-приложениях Azure см. в разделе [Параметры приложения](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings).

Если вы не хотите создавать вилку этого репозитория, можете нажать кнопку "Развертывание в Azure" ниже. Затем перейдите в Azure и настройте параметры приложения с информацией о своей учетной записи Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Если вы планируете хранить в Github код или другие параметры системы управления версиями, удалите строки подключения из кода. Их можно задать с помощью параметров приложения вместо веб-приложения.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса.
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB и запустить приложение Flask, используя API MongoDB. Теперь вы можете импортировать дополнительные данные в учетную запись Cosmos DB.

> [!div class="nextstepaction"]
> [Перенос данных в DocumentDB с помощью mongoimport и mongorestore](mongodb-migrate.md)

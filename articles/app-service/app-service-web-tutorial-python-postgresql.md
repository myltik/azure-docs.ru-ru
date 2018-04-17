---
title: Создание веб-приложения Python с подключением к базе данных PostgreSQL в Azure | Документация Майкрософт
description: Узнайте, как создать приложение Python с подключением к базе данных PostgreSQL в Azure.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 9694dcfec7b3297e925bb45bee5e74b69609063d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>Руководство. Создание веб-приложения Python с подключением к базе данных PostgreSQL в Azure

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть приложение в службе приложений на платформе _Linux_, см. статью [Build a Docker Python and PostgreSQL web app in Azure](./containers/tutorial-docker-python-postgresql-app.md) (Создание веб-приложения Docker Python с подключением к базе данных PostgreSQL в Azure).
>

[Служба приложений Azure](app-service-web-overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом руководстве показано, как создать базовое веб-приложение Python в Azure. Вы также подключите это приложение к базе данных PostgreSQL. После выполнения всех действий у вас будет приложение Python Flask, работающее в службе приложений.

![Приложение Python Flask в службе приложений на платформе Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * Подключение приложения Python к MySQL
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Управление приложением на портале Azure.

Следуйте инструкциям этого руководства по macOS. Инструкции для Linux и Windows в большей степени совпадают, но различия не описаны в этом руководстве.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

1. [установите Git](https://git-scm.com/);
1. [установите Python](https://www.python.org/downloads/).
1. [установите и запустите PostgreSQL](https://www.postgresql.org/download/);

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Проверка локальной установки PostgreSQL и создание базы данных

Откройте окно терминала и выполните команду `psql` для подключения к локальному серверу PostgreSQL.

```bash
sudo -u postgres psql
```

Если подключение успешно установлено, это означает, что база данных PostgreSQL запущена. В противном случае запустите локальную базу данных PostgreSQL, выполнив инструкции по [скачиванию ядра PostgreSQL](https://www.postgresql.org/download/).

Создайте базу данных *eventregistration* и настройте отдельного пользователя базы данных пользователя *manager* с паролем *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Введите `\q`, чтобы выйти из клиента PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Создание локального приложения Python Flask

На этом шаге вы настроите локальный проект Python Flask.

### <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала и c помощью команды `CD` перейдите в рабочий каталог.

С помощью следующих команд клонируйте пример репозитория, а затем вернитесь к фиксации первоначального приложения (до фиксации `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Этот пример репозитория содержит приложение [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Выполнение приложения

Установите необходимые пакеты и запустите приложение.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

После полной загрузки приложения вы увидите следующее сообщение:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу `http://localhost:5000`. Щелкните **Зарегистрировать** и создайте тестового пользователя.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Пример приложения Flask хранит данные пользователей в базе данных. Если вам удалось зарегистрировать пользователя, значит, ваше приложение записывает данные в локальную базу данных PostgreSQL.

Чтобы остановить Flask в любое время, введите Ctrl+C в окне терминала. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Создание базы данных PostgreSQL в Azure

На этом шаге вы создадите базу данных PostgreSQL в Azure. При развертывании приложения в Azure используется эта облачная база данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Создание сервера PostgreSQL

Создайте сервер PostgreSQL с помощью команды [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

В следующей команде замените заполнитель *\<postgresql_name>* уникальным именем сервера, а заполнитель *\<admin_username>* — именем пользователя. Это имя используется как часть конечной точки PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure. Имя пользователя необходимо для создания учетной записи администратора исходной базы данных. Вам будет предложено выбрать пароль для этого пользователя.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

После создания сервера базы данных Azure для PostgreSQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

Чтобы разрешить доступ к базе данных со всех IP-адресов, выполните следующую команду Azure CLI.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Azure CLI подтверждает создание правила брандмауэра, выводя результат, аналогичный следующему:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

### <a name="create-a-production-database-and-user"></a>Создание рабочей базы данных и пользователя

Создайте пользователя базы данных с доступом только к отдельной базе данных. Эти учетные данные позволяют избежать предоставления приложению полного доступа к серверу.

Подключитесь к базе данных (вам будет предложено ввести пароль администратора).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Создайте базу данных и пользователя с помощью интерфейса командной строки PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

### <a name="test-app-locally-with-azure-postgresql"></a>Тестирование приложения локально с помощью Azure PostgreSQL

Теперь вернемся в папку *app* клонированного репозитория GitHub. Вы можете запустить приложение Python Flask, обновив переменные среды базы данных.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

После полной загрузки приложения вы увидите следующее сообщение:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу http://localhost:5000. Щелкните **Зарегистрировать** и создайте тестовую регистрацию. Теперь приложение записывает данные в базу данных Azure.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Развернуть в Azure

На этом шаге вы развернете приложение Python, подключенное к базе данных PostgreSQL, в службе приложений.

Репозиторий Git уже содержит файлы, необходимые для запуска веб-приложения Flask в службе приложений. К ним относятся:

- `.deployment` — указывает пользовательский скрипт развертывания для запуска.
- `deploy.cmd` — скрипт развертывания. Он используется для запуска `pip install`.
- `web.config` — указывает сценарий точки входа для запуска `httpPlatformHandler` в IIS.
- `run_waitress_server.py` — скрипт точки входа. Запускает веб-приложение Flask в сервере [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Установка Python

На этом шаге вы установите Python 3.6.2 с [расширением сайта](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) в службе приложений. Для проверки подлинности конечной точки REST вы будете использовать учетные данные, настроенные ранее на шаге [Настойка пользователя развертывания](#configure-a-deployment-user).

Чтобы получить сведения о пакете Python 3.6.2, выполните следующую команду в Cloud Shell. Замените значение *\<deployment_user>* именем пользователя развертывания, а *\<app_name>* — именем приложения. При появлении запроса введите пароль развертывания, который вы настроили ранее.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

Чтобы установить пакет Python, выполните следующую команду в Cloud Shell. Замените значение *\<deployment_user>* именем пользователя развертывания, а *\<app_name>* — именем приложения. При появлении запроса введите пароль развертывания, который вы настроили ранее.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

Из выходных данных команды видно, что Python установлен в `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Настройка параметров базы данных

Ранее в этом руководстве вы определили переменные среды для подключения к базе данных PostgreSQL.

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Код ниже указывает сведения о подключении к базе данных как параметры приложения. Замените значение *\<app_name>* именем приложения, а *\<postgresql_name>* — именем сервера PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 

Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
```

Вы увидите зарегистрированных ранее гостей, которые были сохранены в рабочей базе данных Azure на предыдущем шаге.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Поздравляем!** Вы запустили приложение Python Flask в службе приложений.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы добавите несколько участников для каждой регистрации событий, обновив модель `Guest`.

Извлеките файлы, отмеченные фиксацией `modelChange`.

```bash
git checkout modelChange -- *
```

В этом выпуске уже внесены необходимые изменения в представления, контроллеры и модель. Он также включает в себя перенос базы данных с использованием *Alembic* (`flask db migrate`). Увидеть изменения можно в [представлении фиксации на сайте GitHub](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Выполните приведенные ниже команды, чтобы проверить изменения в локальной среде, запустив сервер Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Перейдите по адресу http://localhost:5000 в браузере, чтобы просмотреть изменения. Создайте тестовую регистрацию.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала на локальном компьютере зафиксируйте все изменения в Git, а затем отправьте изменение кода в Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Перейдите в веб-приложение Azure и еще раз проверьте новые функции. Создайте еще одну регистрацию событий.

```bash 
http://<app_name>.azurewebsites.net 
```

![Приложение Python Flask в службе приложений](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное веб-приложение.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

По умолчанию на портале отображается страница **Обзор** веб-приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)

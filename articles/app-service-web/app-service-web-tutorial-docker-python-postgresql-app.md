---
title: "Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL | Документация Майкрософт"
description: "Узнайте, как создать приложение Docker Python в Azure с подключением к базе данных PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL
В этом руководстве показано, как создать базовое веб-приложение Docker Python в Azure. Это приложение также будет подключено к базе данных PostgreSQL. После выполнения всех действий у вас будет приложение Python Flask, работающее в контейнере Docker в [веб-приложениях службы приложений Azure](app-service-web-overview.md).

![Приложение Docker Python Flask в службе приложений Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>Перед началом работы

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Python.](https://www.python.org/downloads/)
1. [PostgreSQL](https://www.postgresql.org/download/) (этот компонент потребуется запустить);
1. [Docker Community Edition](https://www.docker.com/community-edition);
1. [Скачивание и установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Проверка локальной установки PostgreSQL и создание базы данных
На этом шаге нужно убедиться, что локальная база данных PostgreSQL запущена.

Откройте окно терминала и выполните команду `psql postgres` для подключения к локальному серверу PostgreSQL.

```bash
psql postgres
```

Если подключение успешно установлено, это означает, что локальная база данных PostgreSQL запущена. В противном случае запустите локальную базу данных PostgreSQL, выполнив инструкции по [скачиванию, установке и запуску PostgreSQL](https://www.postgresql.org/download/).

Создайте базу данных `eventregistration` и настройте отдельного пользователя базы данных `manager` с паролем `supersecretpass`.

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

Откройте окно терминала и `CD` в рабочий каталог.  

Выполните следующие команды, чтобы клонировать пример репозитория и перейти к выпуску `0.1-initialapp`.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Этот пример репозитория содержит приложение [Flask](http://http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Выполнение приложения

> [!NOTE] 
> На последующем шаге мы упростим этот процесс, создав контейнер Docker для рабочей базы данных.

Установите необходимые пакеты и запустите приложение.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

После полной загрузки приложения вы увидите следующее сообщение:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу `http://127.0.0.1:5000`. Щелкните **Зарегистрировать** и попробуйте создать фиктивную регистрацию.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

Пример приложения Flask хранит данные пользователей в базе данных. Если вам удалось зарегистрироваться и вы видите регистрацию в приложении, значит, ваше приложение записывает данные в локальную базу данных PostgreSQL.

Чтобы остановить Flask в любое время, введите `Ctrl`+`C` в окне терминала. 

## <a name="create-a-production-postgresql-database"></a>Создание рабочей базы данных PostgreSQL

На этом шаге вы создадите базу данных PostgreSQL в Azure. При развертывании приложения в Azure мы укажем эту базу данных для его рабочей нагрузки.

### <a name="log-in-to-azure"></a>Вход в Azure

Теперь воспользуемся Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Python в службе приложений Azure.  Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli-interactive 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере создается группа ресурсов в регионе "Западная часть США".

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Чтобы увидеть доступные значения для `--location`, используйте команду `az appservice list-locations` Azure CLI.

### <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL

Создайте сервер PostgreSQL с помощью команды [az postgres server create](/cli/azure/documentdb#create).

В следующей команде замените `<postgresql_name>` уникальным именем своего сервера PostgreSQL везде, где встречается этот заполнитель. Это уникальное имя используется как часть конечной точки PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

Параметр `--admin-user` необходим для создания учетной записи администратора исходной базы данных. Вам будет предложено выбрать пароль для этого пользователя.

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

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Создание правила брандмауэра для сервера базы данных Azure для PostgreSQL

Прежде чем можно будет получить доступ к базе данных, необходимо разрешить к ней доступ со всех IP-адресов. Это можно сделать с помощью следующей команды Azure CLI.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

При создании брандмауэра Azure CLI подтверждает наличие правила следующим образом.

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

## <a name="connect-your-python-flask-application-to-the-database"></a>Подключение приложения Python Flask к базе данных

На этом шаге вы подключаете пример приложения Python Flask к серверу базы данных Azure для PostgreSQL, который вы создали.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>Создание пустой базы данных и настройка нового пользователя приложения базы данных

Мы создаем пользователя базы данных с доступом только к отдельной базе данных. Этот шаг позволяет избежать предоставления приложению полного доступа к серверу с помощью учетных данных администратора.

Подключитесь к базе данных (вам будет предложено ввести пароль администратора).
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Затем создайте базу данных и пользователя с помощью интерфейса командной строки PostgreSQL.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Тестирование приложения локально с базой данных Azure для PostgreSQL 

Теперь вернемся в папку `app` клонированного репозитория GitHub. Мы можем запустить приложение Python Flask, просто обновив переменные среды базы данных.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

После полной загрузки приложения вы увидите сообщение следующего вида.

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу `http://127.0.0.1:5000`. Щелкните **Зарегистрировать** и попробуйте создать фиктивную регистрацию. Теперь данные записываются в рабочую базу данных в Azure.

![Приложение Python Flask, выполняемое в локальной среде](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Запуск приложения из контейнера Docker

Теперь мы создадим образ контейнера Docker и локально запустим приложение из контейнера Docker, сохранив подключение к рабочей базе данных PostgreSQL в Azure.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker отображает подтверждение успешного создания контейнера.

```
Successfully built 7548f983a36b
```

Давайте добавим переменные среды базы данных в файл переменных среды `db.env`.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Теперь запустим приложение из контейнера Docker. Мы указываем файл переменных среды и сопоставляем порт Flask по умолчанию 5000 с нашим локальным портом 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Не удивительно, что результат аналогичен предыдущему. Тем не менее перенос исходной базы данных больше не требуется. Поэтому он пропускается.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 База данных уже содержит регистрацию, созданную ранее.

 ![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Передача контейнера Docker в реестр контейнеров
На этом шаге вы передадите созданный контейнер Docker в реестр контейнеров. Мы будем использовать реестр контейнеров Azure, однако можно также использовать другие популярные реестры контейнеров, например Docker Hub.

### <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

В приведенной команде замените `<registry_name>` уникальным именем реестра контейнеров Azure по своему усмотрению, чтобы создать реестр контейнеров.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Выходные данные
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Получение учетных данных реестра для передачи и извлечения образов Docker

Чтобы получить доступ к учетным данным, необходимо включить режим администратора.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Будут показаны два пароля. Запишите имя пользователя и первый пароль на будущее.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Передача контейнера Docker в реестр контейнеров Azure

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Развертывание приложения Docker Python Flask в Azure
На этом шаге вы развернете приложение Docker Python Flask из контейнера Docker в службе приложений Azure.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

В следующем примере создается план службы приложений на основе Linux `myAppServicePlan` в ценовой категории S1.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

После создания плана службы приложений в Azure CLI отображаются следующие сведения:

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений `myAppServicePlan` создайте веб-приложение в рамках этого плана. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В следующей команде замените `<app_name>` на уникальное имя своего приложения. Это уникальное имя будет использоваться в доменном имени веб-приложения по умолчанию, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli-interactive
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

После создания веб-приложения в Azure CLI отображаются следующие сведения: 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>Настройка переменных среды базы данных

Ранее в этом руководстве вы вручную определили переменные среды для подключения к базе данных PostgreSQL.

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

Ниже описывается, как указать сведения о подключении к базе данных в качестве параметров приложения. Мы дополнительно используем переменную `PORT`, чтобы указать, что требуется сопоставить порт 5000 из контейнера Docker для получения трафика HTTP через порт 80.

```azurecli-interactive
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Настройка развертывания контейнера Docker 

Служба приложений может автоматически скачать и запустить контейнер Docker.

Для их создания используйте команду [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set). 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

При каждом обновлении контейнера Docker или изменении указанных выше параметров следует перезапустить приложение, чтобы применить все параметры и извлечь из реестра последний контейнер.

```azurecli-interactive
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 
Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> При самом первом обращении к веб-приложению после внесения изменений в конфигурацию контейнера может потребоваться дополнительное время для скачивания и запуска контейнера.

Вы увидите зарегистрированные ранее гости, которые были сохранены в рабочей базе данных Azure на предыдущем шаге.

 ![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Поздравляем!** Вы запустили приложение Python Flask из контейнера Docker в службе приложений Azure.


## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге мы добавим несколько участников для каждой регистрации событий, обновив модель гостя.

Получите для изменения выпуск `0.2-migration` с помощью следующей команды Git.
```bash
git checkout tags/0.2-migration
```

В этом выпуске уже внесены необходимые изменения в представления, контроллеры и нашу модель. Он также включает в себя перенос базы данных с использованием *Alembic* (`flask db migrate`). Все внесенные изменения можно просмотреть с помощью следующей команды Git.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Выполните приведенные ниже команды, чтобы проверить изменения в локальной среде, запустив сервер Flask.

MAC и Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Перейдите по адресу `http://127.0.0.1:5000` в браузере, чтобы просмотреть изменения. Создайте фиктивную регистрацию.

![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

Создайте образ Docker, отправьте его в реестр контейнеров и перезапустите приложение.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Перейдите в веб-приложение Azure и еще раз проверьте новые функции. Создайте еще одну регистрацию событий.

```bash 
http://<app_name>.azurewebsites.net 
```

![Приложение Docker Python Flask в службе приложений Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейти на портал Azure, чтобы увидеть созданное веб-приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Вы попадете в _колонку_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части колонки отображаются различные страницы конфигурации, которые можно открыть.

![Колонка службы приложений на портале Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.



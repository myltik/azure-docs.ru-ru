---
title: Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL | Документация Майкрософт
description: Узнайте, как создать приложение Docker Python в Azure с подключением к базе данных PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 2728c354a84c4b13b0ad8509d038837733251975
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788438"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL

Платформа "Веб-приложения для контейнеров" — это веб-служба размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом руководстве показано, как создать базовое веб-приложение Docker Python в Azure. Вы также подключите это приложение к базе данных PostgreSQL. После выполнения всех действий у вас будет приложение Python Flask, работающее в контейнере Docker в [службе приложений на платформе Linux](app-service-linux-intro.md).

![Приложение Docker Python Flask в службе приложений на платформе Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * Подключение приложения Python к PostgreSQL
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Управление приложением на портале Azure.

Следуйте инструкциям в этой статье о macOS. Инструкции для Linux и Windows в большей степени совпадают, но различия не описаны в этом руководстве.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

1. [установите Git](https://git-scm.com/);
1. [установите Python](https://www.python.org/downloads/).
1. [установите и запустите PostgreSQL](https://www.postgresql.org/download/);
1. [установите Docker Community Edition](https://www.docker.com/community-edition).

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

Выполните следующие команды, чтобы клонировать пример репозитория и перейти к выпуску *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Этот пример репозитория содержит приложение [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Выполнение приложения

> [!NOTE] 
> На последующем шаге вы упростите этот процесс, создав контейнер Docker для рабочей базы данных.

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

![Приложение Python Flask, выполняемое в локальной среде](./media/tutorial-docker-python-postgresql-app/local-app.png)

Пример приложения Flask хранит данные пользователей в базе данных. Если вам удалось зарегистрировать пользователя, значит, ваше приложение записывает данные в локальную базу данных PostgreSQL.

Чтобы остановить Flask в любое время, введите Ctrl+C в окне терминала. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Создание рабочей базы данных PostgreSQL

На этом шаге вы создадите базу данных PostgreSQL в Azure. При развертывании приложения в Azure используется эта облачная база данных.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL

Создайте сервер PostgreSQL с помощью команды [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

В следующей команде замените заполнитель *\<postgresql_name>* уникальным именем сервера, а заполнитель *\<admin_username>*  — именем пользователя. Это имя используется как часть конечной точки PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure. Имя пользователя необходимо для создания учетной записи администратора исходной базы данных. Вам будет предложено выбрать пароль для этого пользователя.

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Создание правила брандмауэра для сервера базы данных Azure для PostgreSQL

Чтобы разрешить доступ к базе данных со всех IP-адресов, выполните следующую команду Azure CLI. Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

Azure CLI подтверждает создание правила брандмауэра, выводя результат, аналогичный следующему:

```json
{
  "endIpAddress": "0.0.0.0",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAzureIPs",
  "name": "AllowAzureIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> Вы можете применять еще более строгие ограничения в правиле брандмауэра, [разрешая только исходящие IP-адреса, используемые приложением](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

## <a name="connect-your-python-flask-application-to-the-database"></a>Подключение приложения Python Flask к базе данных

На этом шаге вы подключаете пример приложения Python Flask к серверу базы данных Azure для PostgreSQL, который вы создали.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Создание пустой базы данных и настройка нового пользователя приложения базы данных

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

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Тестирование приложения локально с базой данных Azure для PostgreSQL

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

![Приложение Python Flask, выполняемое в локальной среде](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Запуск приложения из контейнера Docker

Создайте образ контейнера Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker отображает подтверждение успешного создания контейнера.

```bash
Successfully built 7548f983a36b
```

В корне репозитория добавьте файл переменной среды с именем _db.env_, а затем добавьте в него следующие переменные среды базы данных. Приложение подключается к рабочей базе данных, выполняемой в Базе данных Azure для PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Запустите приложение из контейнера Docker. Следующая команда указывает файл переменных среды и сопоставляет порт Flask по умолчанию 5000 с локальным портом 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Выходные данные аналогичны показанным ранее. Тем не менее перенос исходной базы данных больше не требуется. Поэтому он пропускается.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

База данных уже содержит регистрацию, созданную ранее.

![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Передача контейнера Docker в реестр контейнеров

На этом шаге вы передадите контейнер Docker в реестр контейнеров. Используйте реестр контейнеров Azure, однако можно также использовать другие популярные реестры контейнеров, например Docker Hub.

### <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

В приведенной команде замените заполнитель *\<registry_name>* уникальным именем реестра контейнеров Azure по своему усмотрению, чтобы создать реестр контейнеров.

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

Чтобы отобразить учетные данные реестра, сначала необходимо включить режим администратора.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Вы увидите два пароля. Запишите имя пользователя и первый пароль.

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

Войдите в системный реестр. В ответ на запрос введите полученный пароль.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Отправьте образ Docker в реестр.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Развертывание приложения Docker Python Flask в Azure

На этом шаге вы развернете приложение Docker Python Flask из контейнера Docker в службе приложений Azure.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

Создайте веб-приложение в рамках плана *myAppServicePlan* службы приложений с помощью команды [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Создайте веб-приложение.

В следующей команде замените заполнитель *\<app_name>* уникальным именем приложения. Это имя используется в URL-адресе по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в службе приложений Azure.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

После создания веб-приложения в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Настройка переменных среды базы данных

Ранее в этом руководстве вы определили переменные среды для подключения к базе данных PostgreSQL.

В службе приложений переменные среды задаются в качестве _параметров приложения_ с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Код ниже указывает сведения о подключении к базе данных как параметры приложения. Кроме того, в нем определена переменная *PORT*, которая сопоставляет порт 5000 из контейнера Docker для получения трафика HTTP через порт 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Настройка развертывания контейнера Docker

Служба приложений может автоматически скачать и запустить контейнер Docker.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

При обновлении контейнера Docker или изменении параметров следует перезапустить приложение. Это позволяет применить все параметры и извлечь из реестра последний контейнер.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 

Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Веб-приложению после внесения изменений в конфигурацию контейнера может потребоваться дополнительное время для скачивания и запуска контейнера.

Вы увидите зарегистрированных ранее гостей, которые были сохранены в рабочей базе данных Azure на предыдущем шаге.

![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Поздравляем!** Вы запустили приложение Python Flask из контейнера Docker в службе приложений Azure.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы добавите несколько участников для каждой регистрации событий, обновив модель гостя.

Получите для изменения выпуск *0.2-migration* с помощью следующей команды Git:

```bash
git checkout tags/0.2-migration
```

В этом выпуске уже внесены необходимые изменения в представления, контроллеры и модель. Он также включает в себя перенос базы данных с использованием *Alembic* (`flask db migrate`). Все внесенные изменения можно просмотреть с помощью следующей команды Git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Выполните приведенные ниже команды, чтобы проверить изменения в локальной среде, запустив сервер Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Перейдите по адресу http://localhost:5000 в браузере, чтобы просмотреть изменения. Создайте тестовую регистрацию.

![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

Создайте образ Docker, отправьте его в реестр контейнеров и перезапустите приложение.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Перейдите в веб-приложение Azure и еще раз проверьте новые функции. Создайте еще одну регистрацию событий.

```bash 
http://<app_name>.azurewebsites.net 
```

![Приложение Docker Python Flask в службе приложений Azure](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное веб-приложение.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/tutorial-docker-python-postgresql-app/app-resource.png)

По умолчанию на портале отображается страница **Обзор** веб-приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../app-service-web-tutorial-custom-domain.md)

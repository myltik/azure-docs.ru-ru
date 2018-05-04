---
title: Создание веб-приложения PHP в Azure и его подключение к базе данных MySQL | Документация Майкрософт
description: Узнайте, как создать приложение PHP, работающее в Azure, с подключением к базе данных MySQL в Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6e2803410c50b47fdaa80654e5e6e61a3807fb43
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-build-a-php-and-mysql-web-app-in-azure"></a>Руководство. Создание веб-приложения PHP в Azure с подключением к базе данных MySQL

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть приложение в службе приложений на платформе _Linux_, см. руководство по [созданию веб-приложений PHP и MySQL в службе приложений Azure на платформе Linux](./containers/tutorial-php-mysql-app.md).
>

[Веб-приложения Azure](app-service-web-overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом руководстве показано, как создать веб-приложение PHP в Azure и подключить его к базе данных MySQL. По завершении вы получите приложение [Laravel](https://laravel.com/), работающее в веб-приложениях службы приложений Azure.

![Приложение PHP, работающее в службе приложений Azure](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение приложения PHP к MySQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

* [установите Git](https://git-scm.com/);
* [PHP 5.6.4 или более поздней версии](http://php.net/downloads.php);
* [Composer](https://getcomposer.org/doc/00-intro.md);
* включите следующие расширения PHP, требуемые для Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML;
* [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) (этот компонент потребуется запустить). 

## <a name="prepare-local-mysql"></a>Подготовка локальной базы данных MySQL

На этом шаге вы создадите базу данных на локальном сервере MySQL для использования в этом руководстве.

### <a name="connect-to-local-mysql-server"></a>Подключение к локальному серверу MySQL

В окне терминала подключитесь к локальному серверу MySQL. Используйте это окно терминала для выполнения всех команд в рамках этого руководства.

```bash
mysql -u root -p
```

Если появится предложение ввести пароль, введите пароль для учетной записи `root`. Если вы не помните пароль учетной записи привилегированного пользователя, ознакомьтесь с разделом [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: как сбросить пароль привилегированного пользователя).

Если команда выполнена успешно, значит, сервер MySQL работает. В противном случае обеспечьте запуск локального сервера MySQL, выполнив соответствующие [действия после установки MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Локальное создание базы данных

В командной строке `mysql` создайте базу данных.

```sql 
CREATE DATABASE sampledb;
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Локальное создание приложения PHP
На этом шаге вы создадите пример приложения Laravel, настроите его подключение к базе данных и запустите это приложение в локальной среде. 

### <a name="clone-the-sample"></a>Клонирования репозитория

Откройте окно терминала и c помощью команды `cd` перейдите в рабочий каталог.

Выполните команду ниже, чтобы клонировать репозиторий с примером.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

С помощью команды `cd` перейдите в клонированный каталог.
Установите необходимые пакеты.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Настройка подключения к MySQL

В корне репозитория создайте текстовый файл *.env*. Скопируйте в файл *.env* приведенные ниже переменные. Замените заполнитель _&lt;root_password >_ паролем привилегированного пользователя MySQL.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Сведения о том, как Laravel использует файл _.env_, см. в разделе [Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Конфигурация среды).

### <a name="run-the-sample-locally"></a>Локальный запуск примера

Выполните [перенос базы данных Laravel](https://laravel.com/docs/5.4/migrations), чтобы создать таблицы, необходимые для приложения. Чтобы узнать, какие таблицы создаются при переносе, просмотрите каталог _database/migrations_ в этом репозитории Git.

```bash
php artisan migrate
```

Создайте ключ приложения Laravel.

```bash
php artisan key:generate
```

Запустите приложение.

```bash
php artisan serve
```

Откройте браузер и перейдите по адресу `http://localhost:8000`. Добавьте несколько задач на странице.

![Приложение PHP успешно подключается к базе данных MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Чтобы остановить сервер PHP, введите `Ctrl + C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Создание базы данных MySQL в Azure

На этом шаге вы создадите базу данных MySQL в [Базе данных Azure для MySQL](/azure/mysql). Позже вы настроите приложение PHP для подключения к этой базе данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

В Cloud Shell создайте сервер в службе "База данных Azure для MySQL", выполнив команду [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

В следующей команде замените заполнитель *\<mysql_server_name>* уникальным именем сервера, заполнитель *\<admin_user>* — именем пользователя, а заполнитель *\<admin_password>* — паролем. Это имя используется как часть конечной точки PostgreSQL (`https://<mysql_server_name>.mysql.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name mydemoserver --location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Так как в этом руководстве рассматриваются несколько учетных данных, чтобы избежать путаницы для `--admin-user` и `--admin-password` задаются фиктивные значения. В рабочей среде выберите имя пользователя и пароль для сервера MySQL в Azure, следуя рекомендациям по безопасности.
>
>

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
  "additionalProperties": {},
  "administratorLogin": "<admin_user>",
  "earliestRestoreDate": "2018-04-19T22:56:40.990000+00:00",
  "fullyQualifiedDomainName": "<mysql_server_name>.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

В Cloud Shell создайте правило брандмауэра для сервера MySQL, чтобы разрешить подключения клиентов, выполнив команду [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Вы можете применять еще более строгие ограничения в правиле брандмауэра, [разрешая только исходящие IP-адреса, используемые приложением](app-service-ip-addresses.md#find-outbound-ips).
>

### <a name="connect-to-production-mysql-server-locally"></a>Локальное подключение к серверу рабочей базы данных MySQL

В окне терминала на локальном компьютере подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для заполнителя _&lt;mysql_server_name>_. При появлении запроса на ввод пароля используйте пароль, указанный во время создания базы данных в Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com-P 3306 -p
```

### <a name="create-a-production-database"></a>Создание рабочей базы данных

В командной строке `mysql` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных с именем _phpappuser_ и предоставьте ему все привилегии в базе данных `sampledb`. Для простоты в этом руководстве используется пароль _MySQLAzure2017_.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Подключение приложения к базе данных Azure MySQL

На этом шаге вы подключите приложение PHP к базе данных MySQL, созданной в Базе данных Azure для MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Настройка подключения к базе данных

В корневой папке репозитория создайте файл _.env.production_ и скопируйте в него следующие переменные. Замените заполнитель _&lt;mysql_server_name >_ в *DB_HOST* и *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Сохраните изменения.

> [!TIP]
> Чтобы защитить сведения о подключении к MySQL, этот файл извлекается из репозитория Git (см. файл _.gitignore_ в корне репозитория). Позже вы узнаете, как настроить переменные среды в службе приложений для подключения к базе данных в Базе данных Azure для MySQL. Благодаря этим переменным вам не нужен файл *.env* в службе приложений.
>

### <a name="configure-ssl-certificate"></a>Настройка SSL-сертификата

По умолчанию база данных Azure для MySQL ограничивает SSL-соединений из клиентов. Чтобы подключиться к базе данных MySQL в Azure, вам потребуется SSL-сертификат с расширением [_PEM_, предоставляемый службой "База данных Azure для MySQL"](../mysql/howto-configure-ssl.md).

Откройте файл _config/database.php_ и добавьте в `connections.mysql` параметры `sslmode` и `options`, как показано в следующем коде.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

В рамках этого руководства сертификат `BaltimoreCyberTrustRoot.crt.pem` предоставляется в репозитории для удобства. 

### <a name="test-the-application-locally"></a>Локальное тестирование приложения

Выполните перенос базы данных Laravel с помощью файла _.env.production_, указав его как файл среды, чтобы создать таблицы в базе данных MySQL, размещенной в Базе данных Azure для MySQL. Помните, что файл _.env.production_ содержит сведения о подключении к базе данных MySQL в Azure.

```bash
php artisan migrate --env=production --force
```

Файл _.env.production_ еще не содержит действительный ключ приложения. Создайте для него новый ключ в окне терминала.

```bash
php artisan key:generate --env=production --force
```

Запустите пример приложения, указав файл _.env.production_ как файл среды.

```bash
php artisan serve --env=production
```

Перейдите на страницу `http://localhost:8000`. Если страница загрузилась без ошибок, значит, приложение PHP подключается к базе данных MySQL в Azure.

Добавьте несколько задач на странице.

![Подключение приложения PHP к Базе данных Azure для MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Чтобы остановить приложение PHP, введите `Ctrl + C` в окне терминала.

### <a name="commit-your-changes"></a>Фиксация изменений

Чтобы зафиксировать изменения, выполните следующие команды Git:

```bash
git add .
git commit -m "database.php updates"
```

Ваше приложение готово к развертыванию.

## <a name="deploy-to-azure"></a>Развернуть в Azure

На этом шаге вы развернете приложение PHP, подключенное к базе данных MySQL, в службе приложений Azure.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Настройка параметров базы данных

Как указывалось ранее, к базе данных MySQL в Azure можно подключиться с помощью переменных среды в службе приложений.

В Cloud Shell переменные среды задаются в качестве _параметров приложения_ с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Команда ниже позволяет настроить параметры приложения `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` и `DB_PASSWORD`. Замените заполнители _&lt;appname>_ и _&lt;mysql_server_name>_ собственными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Для доступа к параметрам можно использовать метод PHP [getenv](http://www.php.net/manual/function.getenv.php). В коде Laravel используется программа-оболочка [env](https://laravel.com/docs/5.4/helpers#method-env) для PHP `getenv`. Например, конфигурация MySQL в файле _config/database.php_ выглядит следующим образом:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Настройка переменных среды Laravel

В среде Laravel требуется ключ приложения из службы приложений. Его можно настроить с помощью параметров приложения.

В окне терминала на локальном компьютере используйте `php artisan` для создания ключа приложения без сохранения в файле _.env_.

```bash
php artisan key:generate --show
```

В Cloud Shell задайте ключ приложения в веб-приложении службы приложений с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set). Замените заполнители _&lt;appname>_ и _&lt;outputofphpartisankey:generate>_ собственными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Значение `APP_DEBUG="true"` указывает Laravel возвращать отладочную информацию в случае ошибок в работе развернутого веб-приложения. При выполнении рабочего приложения задайте для него более безопасное значение `false`.

### <a name="set-the-virtual-application-path"></a>Задание пути виртуального приложения

Задайте путь виртуального приложения для веб-приложения. Этот шаг требуется из-за того, что [жизненный цикл приложения Laravel](https://laravel.com/docs/5.4/lifecycle) начинается в _общем_ каталоге, а не в корневом каталоге приложения. Другие платформы PHP, жизненный цикл которых начинается в корневом каталоге, могут работать без ручной настройки пути виртуального приложения.

В Cloud Shell задайте путь виртуального приложения с помощью команды [`az resource update`](/cli/azure/resource#az_resource_update). Замените заполнитель _&lt;appname>_ собственным значением.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

По умолчанию служба приложений Azure указывает корневой путь виртуального приложения (_/_) к корневому каталогу файлов развернутого приложения (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Вы можете заметить, что в конце процесс развертывания устанавливает пакеты [Composer](https://getcomposer.org/). Служба приложений не запускает эти сценарии автоматизации во время развертывания по умолчанию. Для этого в данном примере в корневом каталоге репозитория расположены три дополнительных файла.
>
> - `.deployment` — этот файл означает, что служба приложений должна запустить `bash deploy.sh` как пользовательский скрипт развертывания.
> - `deploy.sh` — пользовательский скрипт развертывания. Если открыть этот файл, вы увидите, что он запускает `php composer.phar install` после `npm install`.
> - `composer.phar` — диспетчер пакетов Composer.
>
> Точно так же можно добавить любые действия развертывания в службе приложений на основе Git. Дополнительные сведения см. в статье [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Пользовательский сценарий развертывания).
>

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите по адресу `http://<app_name>.azurewebsites.net` и добавьте несколько задач в список.

![Приложение PHP, работающее в службе приложений Azure](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Вы запустили управляемое данными приложение PHP в службе приложений Azure.

## <a name="update-model-locally-and-redeploy"></a>Локальное обновление и повторное развертывание модели

На этом шаге вы внесете некоторые изменения в модель данных `task` и веб-приложение, а затем опубликуете обновления в Azure.

Для сценария с задачами необходимо изменить приложение, чтобы можно было пометить задачу как выполненную.

### <a name="add-a-column"></a>Добавление столбца

В окне терминала на локальном компьютере перейдите к корневой папке репозитория Git.

Создайте перенос базы данных для таблицы `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Эта команда отображает имя файла переноса, который создается. Найдите этот файл в каталоге _database/migrations_ и откройте его.

Замените метод `up` следующим кодом:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Предыдущий код добавляет столбец логических значений `complete` в таблицу `tasks`.

Замените метод `down` следующим кодом действия отката:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

В окне терминала на локальном компьютере выполните перенос базы данных Laravel, чтобы внести изменения в локальную базу данных.

```bash
php artisan migrate
```

В соответствии с условиями [соглашения об именовании Laravel](https://laravel.com/docs/5.4/eloquent#defining-models) модель `Task` (см. _app/Task.php_) сопоставляется с таблицей `tasks` по умолчанию.

### <a name="update-application-logic"></a>Обновление логики приложения

Откройте файл *routes/web.php*. В нем приложение определяет свои маршруты и бизнес-логику.

В конце файла добавьте маршрут с помощью следующего кода.

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Предыдущий код выполняет простое обновление модели данных, переключая значение `complete`.

### <a name="update-the-view"></a>Обновление представления

Откройте файл *resources/views/tasks.blade.php*. Найдите открывающий тег `<tr>` и замените его приведенным ниже значением.

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Предыдущий код изменяет цвет строк в зависимости от того, завершена ли задача.

Следующая строка содержит приведенный ниже код.

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Замените всю эту строку следующим кодом.

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Предыдущий код добавляет кнопку "Submit" (Отправить), которая ссылается на маршрут, определенный ранее.

### <a name="test-the-changes-locally"></a>Локальная проверка изменений

В окне терминала на локальном компьютере запустите сервер разработки из корневого каталога репозитория Git.

```bash
php artisan serve
```

Чтобы увидеть, как изменится состояние задачи, перейдите по адресу `http://localhost:8000` и установите флажок.

![Добавлен флажок для задачи](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

Чтобы остановить приложение PHP, введите `Ctrl + C` в окне терминала.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала на локальном компьютере выполните перенос базы данных Laravel со строкой подключения рабочей базы данных, чтобы внести изменения в базу данных в Azure.

```bash
php artisan migrate --env=production --force
```

Зафиксируйте все изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

После выполнения команды `git push` перейдите в веб-приложение Azure и еще раз проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Добавленные задачи сохраняются в базе данных. Изменения в схеме данных не влияют на имеющиеся данные.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

При запуске приложения PHP в службе приложений Azure можно передавать журналы консоли в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) в Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

После настройки потоков обновите веб-приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал. Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

Чтобы отменить потоки для журналов, выполните команду `Ctrl`+`C`.

> [!TIP]
> Приложение PHP может использовать стандартный метод [error_log()](http://php.net/manual/function.error-log.php) для вывода в консоль. Пример приложения использует этот подход в файле конфигурации _app/Http/routes.php_.
>
> Являясь веб-платформой, [Laravel использует Monolog](https://laravel.com/docs/5.4/errors) в качестве поставщика службы ведения журнала. Чтобы узнать, как настроить Monolog для вывода сообщений в консоль, ознакомьтесь с разделом [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP: как использовать Monolog для вывода журналов в консоль (php://out)).
>
>

## <a name="manage-the-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com) для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

В меню слева доступны страницы для настройки приложения.

![Страница службы приложений на портале Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение приложения PHP к MySQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)

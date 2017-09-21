---
title: "Создание веб-приложения PHP в Azure и его подключение к базе данных MySQL | Документация Майкрософт"
description: "Узнайте, как создать приложение PHP, работающее в Azure, с подключением к базе данных MySQL в Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/21/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: c6981b8059f11afbce0492aac5acdf06b3ca4858
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Создание веб-приложения PHP в Azure с подключением к базе данных MySQL

Платформа [Веб-приложения для контейнеров](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом руководстве показано, как создать веб-приложение PHP и подключить его к базе данных MySQL. По завершении вы получите приложение [Laravel](https://laravel.com/), работающее на платформе "Веб-приложения для контейнеров".

![Приложение PHP, работающее в службе приложений Azure](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение приложения PHP к MySQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* [установите Git](https://git-scm.com/);
* [PHP 5.6.4 или более поздней версии](http://php.net/downloads.php);
* [Composer](https://getcomposer.org/doc/00-intro.md);
* включите следующие расширения PHP, требуемые для Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML;
* [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) (этот компонент потребуется запустить). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

В корне репозитория создайте файл *.env*. Скопируйте в файл *.env* приведенные ниже переменные. Замените заполнитель _&lt;root_password >_ паролем привилегированного пользователя MySQL.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

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

![Приложение PHP успешно подключается к базе данных MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Чтобы остановить приложение PHP, введите `Ctrl + C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Создание базы данных MySQL в Azure

На этом шаге вы создадите базу данных MySQL в [базе данных Azure для MySQL (предварительная версия)](/azure/mysql). Позже вы настроите приложение PHP для подключения к этой базе данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

Создайте сервер в базе данных Azure для MySQL (предварительная версия), выполнив команду [az mysql server create](/cli/azure/mysql/server#create).

В следующей команде замените _&lt;mysql_server_name>_ именем своего сервера MySQL везде, где встречается этот заполнитель. Допустимые символы: `a-z`, `0-9` и `-`. Это имя является частью имени узла сервера MySQL (`<mysql_server_name>.database.windows.net`). Оно должно быть глобально уникальным.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password MySQLAzure2017 --ssl-enforcement Disabled
```

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

Создайте правило брандмауэра для сервера MySQL, чтобы разрешить подключения клиентов, выполнив команду [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> База данных Azure для MySQL (предварительная версия) пока еще не ограничивает подключения только к службам Azure. Так как IP-адреса в Azure назначаются динамически, мы рекомендуем включить все IP-адреса. Служба находится на этапе предварительной версии. В ближайшем будущем будут добавлены более надежные методы защиты базы данных.
>
>

### <a name="connect-to-production-mysql-server-locally"></a>Локальное подключение к серверу рабочей базы данных MySQL

В окне терминала подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для заполнителя _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

При появлении запроса на ввод пароля используйте пароль _$tr0ngPa$ w0rd!_, указанный во время создания базы данных.

### <a name="create-a-production-database"></a>Создание рабочей базы данных

В командной строке `mysql` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных с именем _phpappuser_ и предоставьте ему все привилегии в базе данных `sampledb`.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Подключение приложения к базе данных Azure MySQL

На этом шаге вы подключите приложение PHP к базе данных MySQL, созданной в базе данных Azure для MySQL (предварительная версия).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Настройка подключения к базе данных

В корневой папке репозитория создайте файл _.env.production_ и скопируйте в него следующие переменные. Замените заполнитель _&lt;mysql_server_name>_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
```
<!-- MYSQL_SSL=true -->

Сохраните изменения.

> [!TIP]
> Чтобы защитить сведения о подключении к MySQL, этот файл извлекается из репозитория Git (см. файл _.gitignore_ в корне репозитория). Позже вы узнаете, как настроить переменные среды в службе приложений для подключения к базе данных в базе данных Azure для MySQL (предварительная версия). Благодаря этим переменным вам не нужен файл *.env* в службе приложений.
>

<!-- ### Configure SSL certificate

By default, Azure Database for MySQL enforces SSL connections from clients. To connect to your MySQL database in Azure, you must use a _.pem_ SSL certificate.

Open _config/database.php_ and add the _sslmode_ and _options_ parameters to `connections.mysql`, as shown in the following code.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

To learn how to generate this _certificate.pem_, see [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](../../mysql/howto-configure-ssl.md).

> [!TIP]
> The path _/ssl/certificate.pem_ points to an existing _certificate.pem_ file in the Git repository. This file is provided for convenience in this tutorial. For best practice, you should not commit your _.pem_ certificates into source control. 
> -->

### <a name="test-the-application-locally"></a>Локальное тестирование приложения

Выполните перенос базы данных Laravel с помощью файла _.env.production_, указав его как файл среды, чтобы создать таблицы в базе данных MySQL, размещенной в базе данных Azure для MySQL (предварительная версия). Помните, что файл _.env.production_ содержит сведения о подключении к базе данных MySQL в Azure.

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

![Приложение PHP успешно подключается к базе данных Azure для MySQL (предварительная версия)](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-linux-php-no-h.md)] 

### <a name="configure-database-settings"></a>Настройка параметров базы данных

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set).

Команда ниже позволяет настроить параметры приложения `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` и `DB_PASSWORD`. Замените заполнители _&lt;appname>_ и _&lt;mysql_server_name>_ собственными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```
 <!-- MYSQL_SSL="true" -->

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

Используйте `php artisan` для создания ключа приложения без сохранения в файле _.env_.

```bash
php artisan key:generate --show
```

Задайте ключ приложения в веб-приложении службы приложений с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set). Замените заполнители _&lt;appname>_ и _&lt;outputofphpartisankey:generate>_ собственными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Значение `APP_DEBUG="true"` указывает Laravel возвращать отладочную информацию в случае ошибок в работе развернутого веб-приложения. При выполнении рабочего приложения задайте для него более безопасное значение `false`.

### <a name="set-the-virtual-application-path"></a>Задание пути виртуального приложения

Задайте путь виртуального приложения для веб-приложения. Этот шаг требуется из-за того, что [жизненный цикл приложения Laravel](https://laravel.com/docs/5.4/lifecycle) начинается в _общем_ каталоге, а не в корневом каталоге приложения. Другие платформы PHP, жизненный цикл которых начинается в корневом каталоге, могут работать без ручной настройки пути виртуального приложения.

Задайте путь виртуального приложения с помощью команды [az resource update](/cli/azure/resource#update). Замените заполнитель _&lt;appname>_ собственным значением.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

По умолчанию служба приложений Azure указывает корневой путь виртуального приложения (_/_) к корневому каталогу файлов развернутого приложения (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

Добавьте удаленную службу приложений Azure в локальный репозиторий Git.

```bash
git remote add azure <paste_copied_url_here>
```

Выполните публикацию в удаленную службу приложений Azure, чтобы развернуть приложение PHP. После этого введите пароль, указанный ранее в процессе создания пользователя развертывания.

```bash
git push azure master
```

Во время развертывания служба приложений Azure будет взаимодействовать с Git.

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

![Приложение PHP, работающее в службе приложений Azure](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

Вы запустили управляемое данными приложение PHP в службе приложений Azure.

## <a name="update-model-locally-and-redeploy"></a>Локальное обновление и повторное развертывание модели

На этом шаге вы внесете некоторые изменения в модель данных `task` и веб-приложение, а затем опубликуете обновления в Azure.

Для сценария с задачами необходимо изменить приложение, чтобы можно было пометить задачу как выполненную.

### <a name="add-a-column"></a>Добавление столбца

В окне терминала перейдите к корневой папке репозитория Git.

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

В окне терминала выполните перенос базы данных Laravel, чтобы внести изменения в локальную базу данных.

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

Из корневого каталога репозитория Git запустите сервер разработки.

```bash
php artisan serve
```

Чтобы увидеть, как изменится состояние задачи, перейдите по адресу `http://localhost:8000` и установите флажок.

![Добавлен флажок для задачи](./media/tutorial-php-mysql-app/complete-checkbox.png)

Чтобы остановить приложение PHP, введите `Ctrl + C` в окне терминала.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала выполните перенос базы данных Laravel со строкой подключения рабочей базы данных, чтобы внести изменения в базу данных в Azure.

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

![Изменения модели и базы данных, опубликованные в Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Добавленные задачи сохраняются в базе данных. Изменения в схеме данных не влияют на имеющиеся данные.

## <a name="manage-the-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com) для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/tutorial-php-mysql-app/access-portal.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

В меню слева доступны страницы для настройки приложения.

![Страница службы приложений на портале Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение приложения PHP к MySQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../../app-service-web/app-service-web-tutorial-custom-domain.md)


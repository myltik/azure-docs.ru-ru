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
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ae0b63bc338cb3e96eae4593b96265aafbcbc029
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Создание веб-приложения PHP в Azure с подключением к базе данных MySQL
В этом руководстве показано, как создать веб-приложение PHP в Azure и подключить его к базе данных MySQL. По завершении вы получите приложение [Laravel](https://laravel.com/), работающее в [веб-приложениях службы приложений Azure](app-service-web-overview.md).

![Приложение PHP, работающее в службе приложений Azure](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение приложения PHP к MySQL.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [PHP 5.6.4 или более поздней версии](http://php.net/downloads.php);
1. [Composer](https://getcomposer.org/doc/00-intro.md);
1. включите следующие расширения PHP, требуемые для Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML;
1. [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) (этот компонент потребуется запустить); 
1. [Скачивание и установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Подготовка локальной базы данных MySQL

На этом шаге вы создадите базу данных на локальном сервере MySQL для использования в этом руководстве.

### <a name="connect-to-mysql-server"></a>Подключение к серверу MySQL
В окне терминала подключитесь к локальному серверу MySQL.

```bash
mysql -u root -p
```

Если появится предложение ввести пароль, введите пароль для учетной записи `root`. Если вы не помните пароль учетной записи привилегированного пользователя, ознакомьтесь с разделом [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: как сбросить пароль привилегированного пользователя).

Если команда выполнена успешно, значит, сервер MySQL уже работает. В противном случае обеспечьте запуск локального сервера MySQL, выполнив соответствующие [действия после установки MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Создание базы данных

В командной строке `mysql` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>Создание локального приложения PHP
На этом шаге вы создадите пример приложения Laravel, настроите его подключение к базе данных и запустите это приложение в локальной среде. 

### <a name="clone-the-sample"></a>Клонирования репозитория

Откройте окно терминала и `cd` в рабочий каталог.  

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

С помощью команды `cd` перейдите в клонированный каталог и установите необходимые пакеты.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Настройка подключения к MySQL

В корневой папке репозитория создайте файл _.env_ и скопируйте в него следующие переменные. Замените заполнитель _&lt;root_password >_ паролем привилегированного пользователя.

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

> [!NOTE]
> Сведения о том, как Laravel использует этот файл _.env_, см. в разделе [Laravel Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Конфигурация Laravel).
>
>

### <a name="run-the-sample"></a>Запуск примера

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

Чтобы остановить приложение PHP в любое время, введите `Ctrl`+`C` в окне терминала. 

## <a name="create-production-mysql-in-azure"></a>Создание рабочей базы данных MySQL в Azure

На этом шаге вы создадите базу данных MySQL в [базе данных Azure для MySQL (предварительная версия)](/azure/mysql). Позже вы настроите приложение PHP для подключения к этой базе данных.

### <a name="log-in-to-azure"></a>Вход в Azure

Теперь воспользуемся Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения PHP в службе приложений Azure. Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере создается группа ресурсов в регионе "Северная Европа".

```azurecli
az group create --name myResourceGroup --location "North Europe"
```

Чтобы увидеть доступные значения для `--location`, выполните команду [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

Создайте сервер в базе данных Azure для MySQL (предварительная версия), выполнив команду [az mysql server create](/cli/azure/mysql/server#create).

В следующей команде замените _&lt;mysql_server_name>_ уникальным именем своего сервера MySQL везде, где встречается этот заполнитель. Это имя является частью имени узла сервера MySQL (`<mysql_server_name>.database.windows.net`), поэтому оно должно быть глобально уникальным. Аналогичным образом замените _&lt;admin_user>_ и _&lt;admin_password>_ собственными значениями.

```azurecli
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "<admin_user>",
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

```azurecli
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> База данных Azure для MySQL (предварительная версия) пока еще не позволяет использовать только подключения из служб Azure. Так как IP-адреса в Azure назначаются динамически, на данный момент рекомендуется включить все IP-адреса. Так как служба находится на этапе предварительной версии, в ближайшем будущем будут добавлены более надежные методы защиты базы данных.
>
>

### <a name="connect-to-production-mysql-server"></a>Подключение к серверу рабочей базы данных MySQL

В окне терминала подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для _&lt;admin_user>_ и _&lt;mysql_server_name>_.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Создание рабочей базы данных

В командной строке `mysql` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных и предоставьте ему все привилегии в базе данных `sampledb`. Замените заполнители _&lt;phpapp_user>_ и _&lt;phpapp_password>_ уникальными значениями для своего приложения.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>Подключение приложения к рабочей базе данных MySQL

На этом шаге вы подключите приложение PHP к базе данных MySQL, только что созданной в базе данных Azure для MySQL (предварительная версия). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Настройка подключения 

В корневой папке репозитория создайте файл _.env.production_ и скопируйте в него следующие переменные. Замените заполнители _&lt;mysql_server_name>_, _&lt;phpapp_user>_ и _&lt;phpapp_password>_ собственными значениями.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

Сохраните изменения.

### <a name="test-the-application"></a>Тестирование приложения

Выполните перенос базы данных Laravel с помощью файла _.env.production_, указав его как файл среды, чтобы создать таблицы в базе данных MySQL, размещенной в базе данных Azure для MySQL (предварительная версия).

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

Откройте браузер и перейдите по адресу `http://localhost:8000`. Если страница загрузилась без ошибок, значит, приложение PHP подключается к базе данных MySQL в Azure. 

Добавьте несколько задач на странице.

![Приложение PHP успешно подключается к базе данных Azure для MySQL (предварительная версия)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Чтобы остановить приложение PHP в любое время, введите `Ctrl`+`C` в окне терминала. 

### <a name="secure-sensitive-data"></a>Защита конфиденциальных данных

Необходимо убедиться, что конфиденциальные данные в файле _.env.production_ не фиксируются в Git.

Для этого откройте файл _.gitignore_ в корневой папке репозитория и добавьте имя файла в новой строке.

```
.env.production
```

Сохраните изменения, а затем зафиксируйте их в Git.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

Позже вы узнаете, как настроить переменные среды в службе приложений для подключения к базе данных в базе данных Azure для MySQL (предварительная версия), поэтому вам не нужен файл `.env` в службе приложений. 

## <a name="deploy-php-app-to-azure"></a>Развертывание приложения PHP в Azure
На этом шаге вы развернете приложение PHP, подключенное к базе данных MySQL, в службе приложений Azure.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

> [!NOTE] 
> План службы приложений представляет собой коллекцию физических ресурсов, используемых для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, определенные в нем. Поэтому, разместив несколько приложений, вы сможете сэкономить. 
> 
> Планы службы приложений определяют такие компоненты: 
> 
> * регион (Северная Европа, восточная часть США, Юго-Восточная Азия); 
> * размер экземпляра (небольшой, средний, крупный); 
> * число масштабируемых элементов (один, два, три экземпляра и т. д.); 
> * SKU ("Бесплатный", "Общий", "Базовый", "Стандартный", "Премиум"). 
> 

В следующем примере создается план службы приложений _myAppServicePlan_ в ценовой категории **Бесплатный**.

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

После создания плана службы приложений в Azure CLI отображаются следующие сведения:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений _myAppServicePlan_ создайте в нем веб-приложение. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В следующей команде замените заполнитель _&lt;appname>_ уникальным именем своего приложения. Это уникальное имя используется в доменном имени по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

### <a name="set-the-php-version"></a>Задание версии PHP

Задайте версию PHP, которая необходима для приложения, с помощью команды [az appservice web config update](/cli/azure/appservice/web/config#update).

Следующая команда задает версию PHP _7.0_.

```azurecli
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Настройка параметров базы данных

Как указывалось ранее, к базе данных MySQL в Azure можно подключиться с помощью переменных среды в службе приложений.

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

Следующая команда позволяет настроить параметры приложения `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` и `DB_PASSWORD`. Замените заполнители _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ и _&lt;phpapp_password>_ собственными значениями.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

Для доступа к параметрам можно использовать метод PHP [getenv()](http://www.php.net/manual/function.getenv.php). В вашем коде Laravel используется программа-оболочка [env()](https://laravel.com/docs/5.4/helpers#method-env) для PHP `getenv()`. Например, конфигурация MySQL в _config/database.php_ выглядит следующим образом.

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

Как на локальном компьютере, в среде Laravel требуется ключ приложения из службы приложений. Его также можно настроить с помощью параметров приложения.

Используйте `php artisan` для создания ключа приложения без сохранения в файле _.env_.

```bash
php artisan key:generate --show
```

Задайте ключ приложения в веб-приложении службы приложений с помощью команды [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). Замените заполнители _&lt;appname>_ и _&lt;outputofphpartisankey:generate>_ собственными значениями.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` указывает Laravel возвращать отладочную информацию в случае ошибок в работе развернутого веб-приложения. При выполнении рабочего приложения для него следует задать `false`, чтобы повысить безопасность.
>
>

### <a name="set-the-virtual-application-path"></a>Задание пути виртуального приложения

Задайте путь виртуального приложения для веб-приложения. Этот шаг требуется только из-за того, что [жизненный цикл приложения Laravel](https://laravel.com/docs/5.4/lifecycle) начинается в _общем_ каталоге, а не в корневом каталоге приложения. Другие платформы PHP, жизненный цикл которых начинается в корневом каталоге, могут работать без ручной настройки пути виртуального приложения.

Задайте путь виртуального приложения с помощью команды [az resource update](/cli/azure/resource#update). Замените заполнитель _&lt;appname>_ собственным значением.

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> По умолчанию служба приложений Azure указывает корневой путь виртуального приложения (_/_) к корневому каталогу файлов развернутого приложения (_sites\wwwroot_). 
>
>

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

Для использования FTP и локального репозитория Git на сервере необходимо настроить пользователя развертывания, который потребуется, чтобы выполнить проверку подлинности развертывания. 

> [!NOTE] 
> Пользователь развертывания необходим для развертывания FTP и локального репозитория Git в веб-приложении. Имя пользователя и пароль являются учетными данными уровня учетной записи и отличаются от учетных данных подписки Azure.

Если вы ранее создали имя пользователя и пароль для развертывания, то можете отобразить это имя пользователя, выполнив следующую команду.

```azurecli
az appservice web deployment user show
```

Если у вас еще нет пользователя развертывания, выполните команду [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set), чтобы создать учетные данные развертывания. 

```azurecli
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Имя пользователя должно быть уникальным, а пароль — надежным. Если появляется сообщение об ошибке `'Conflict'. Details: 409`, измените имя пользователя. Если появляется сообщение об ошибке `'Bad Request'. Details: 400`, используйте более надежный пароль.

Пользователь развертывания создается один раз. Его можно использовать для всех развертываний Azure.

Запишите имя пользователя и пароль, так как они понадобятся позже, при развертывании приложения.

### <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git 

Для развертывания веб-приложения в службе приложений Azure можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и BitBucket. 

Чтобы настроить доступ локального репозитория Git к веб-приложению Azure, выполните команду [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git). 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

После настройки пользователя развертывания в Azure CLI будет показан URL-адрес развертывания для веб-приложения Azure в следующем формате:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Скопируйте выходные данные из терминала, так как они понадобятся на следующем этапе. 

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
>

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите по адресу `http://<app_name>.azurewebsites.net` и добавьте несколько задач в список. 

![Приложение PHP, работающее в службе приложений Azure](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**Поздравляем!** Вы запустили управляемое данными приложение PHP в службе приложений Azure.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы внесете некоторые изменения в модель данных `task` и опубликуете свои изменения в Azure.

Для сценария с задачами необходимо изменить приложение, чтобы можно было пометить задачу как выполненную. 

### <a name="add-a-column"></a>Добавление столбца

В окне терминала убедитесь, что вы находитесь в корневой папке репозитория Git, а затем создайте перенос базы данных для таблицы `tasks`.

```bash
php artisan make:migration add_complete_column --table=tasks
```

Эта команда отображает имя файла переноса, который создается. Найдите этот файл в каталоге _database/migrations_ и откройте в текстовом редакторе.

Замените метод up() приведенным ниже кодом.

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Этот код добавляет столбец логических значений `complete` в таблицу `tasks`.

Замените метод down() приведенным ниже кодом действия отката.

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

В окне терминала выполните перенос базы данных Laravel в локальной среде, чтобы внести изменения в локальную базу данных.

```bash
php artisan migrate
```

Согласно [соглашению об именовании Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), модель `Task` (см. _app/Task.php_) сопоставляется с таблицей `tasks` по умолчанию. Следовательно, вы завершили обновление модели данных.

### <a name="update-application-logic"></a>Обновление логики приложения

Откройте файл _routes/web.php_. В нем пример приложения определяет свои маршруты и бизнес-логику.

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

Этот код выполняет простое обновление модели данных, переключая значение `complete`.

### <a name="update-the-view"></a>Обновление представления

Откройте файл _resources/views/tasks.blade.php_. Найдите открывающий тег `<tr>` и замените его приведенным ниже значением.

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Это позволит изменять цвет строк в зависимости от того, завершена ли задача.

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

Данный код добавляет кнопку "Submit" (Отправить), которая ссылается на маршрут, определенный ранее.

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Из корневого каталога репозитория Git повторно запустите сервер разработки.

```bash
php artisan serve
```

Перейдите по адресу `http://localhost:8000` в браузере и установите флажок, чтобы увидеть, как соответствующим образом изменится состояние задачи.

![Добавлен флажок для задачи](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала выполните перенос базы данных Laravel со строкой подключения рабочей базы данных, чтобы внести изменения в рабочую базу данных в Azure.

```bash
php artisan migrate --env=production --force
```

Зафиксируйте все изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

После окончания `git push` перейдите в веб-приложение Azure и еще раз проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> Если вы добавили задачи ранее, то вы увидите, что они по-прежнему отображаются. Изменения в схеме данных не влияют на существующие данные.
>
>

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики 

При запуске приложения PHP в службе приложений Azure можно передавать журналы консоли прямо в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

После настройки потоков обновите веб-приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал.

Чтобы отменить потоки для журналов, выполните команду `Ctrl`+`C`. 

> [!TIP]
> Приложение PHP может использовать стандартный метод [error_log()](http://php.net/manual/function.error-log.php) для вывода в консоль. Пример приложения использует этот подход в файле конфигурации _app/Http/routes.php_.
>
> Являясь веб-платформой, [Laravel использует Monolog](https://laravel.com/docs/5.4/errors) в качестве поставщика службы ведения журнала. Чтобы узнать, как настроить Monolog для вывода сообщений в консоль, ознакомьтесь с разделом [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP: как использовать Monolog для вывода журналов в консоль (php://out)).
>
>

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейти на портал Azure, чтобы увидеть созданное веб-приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Вы попадете в _колонку_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части колонки отображаются различные страницы конфигурации, которые можно открыть.

![Колонка службы приложений на портале Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

## <a name="clean-up-resources"></a>Очистка ресурсов
 
Если эти ресурсы не требуются для изучения другого руководства (см. раздел [Дальнейшие действия](#next)), их можно удалить, выполнив следующую команду. 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

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

Перейдите к следующему руководству, чтобы научиться сопоставлять настраиваемое DNS-имя с приложением.

> [!div class="nextstepaction"] 
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)


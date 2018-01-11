---
title: "Построение Ruby и MySQL веб-приложения в службе приложений Azure для Linux | Документы Microsoft"
description: "Узнайте, как получить Ruby приложения работают в Azure, с подключением к базе данных MySQL в Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Построение Ruby и MySQL веб-приложения в службе приложений Azure в Linux

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. Этот учебник демонстрирует создание Ruby веб-приложение и подключить его к базе данных MySQL. После завершения вы получите [Ruby на направляющие](http://rubyonrails.org/) приложение, работающее на службы приложений на платформе Linux.

![Ruby на направляющие приложения, запущенные в службе приложений Azure](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение к MySQL Ruby на направляющие приложения
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством:

* [установите Git](https://git-scm.com/);
* [Установка Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Установите Ruby на направляющие 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
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

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Создайте транскрипции на направляющие приложения локально
На этом шаге получения транскрипции на направляющие образца приложения, его подключение к базе данных и запустить его локально. 

### <a name="clone-the-sample"></a>Клонирования репозитория

Откройте окно терминала и c помощью команды `cd` перейдите в рабочий каталог.

Выполните команду ниже, чтобы клонировать репозиторий с примером.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

С помощью команды `cd` перейдите в клонированный каталог. Установите необходимые пакеты.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Настройка подключения к MySQL

В репозитории, откройте _config/database.yml_ и указать локальный MySQL корневого имени пользователя и пароль (строка 13). Если вы установили MySQL с помощью таких средств, как [Homebrew](https://brew.sh/), то учетные данные в файле уже присвоено значение по умолчанию (который является `root` и пустой пароль).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Локальный запуск примера

Запустите [миграций направляющие](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) для создания приложения в таблицах необходимы. Миграция создаются, можно найти _или переноса db_ каталог в репозитории Git.

```bash
rake db:create
rake db:migrate
```

Запустите приложение.

```bash
rails server
```

Откройте браузер и перейдите по адресу `http://localhost:3000`. Добавьте несколько задач на странице.

![Ruby на направляющие успешно выполнено подключение к MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Чтобы остановить сервер направляющие, введите `Ctrl + C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Создание базы данных MySQL в Azure

На этом шаге вы создадите базу данных MySQL в [базе данных Azure для MySQL (предварительная версия)](/azure/mysql). Впоследствии можно настроить Ruby на направляющие приложения для подключения к этой базе данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

Создайте сервер в базе данных Azure для MySQL (предварительная версия), выполнив команду [az mysql server create](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

В следующей команде замените _&lt;mysql_server_name>_ именем своего сервера MySQL везде, где встречается этот заполнитель. Допустимые символы: `a-z`, `0-9` и `-`. Это имя является частью имени узла сервера MySQL (`<mysql_server_name>.mysql.database.azure.com`). Оно должно быть глобально уникальным.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

Создайте правило брандмауэра для сервера MySQL, чтобы разрешить подключения клиентов, выполнив команду [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> База данных Azure для MySQL (предварительная версия) пока еще не ограничивает подключения только к службам Azure. Так как IP-адреса в Azure назначаются динамически, мы рекомендуем включить все IP-адреса. Служба находится на этапе предварительной версии. В ближайшем будущем будут добавлены более надежные методы защиты базы данных.
>

### <a name="connect-to-production-mysql-server-locally"></a>Локальное подключение к серверу рабочей базы данных MySQL

В окне терминала подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для заполнителя _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

При появлении запроса пароля используйте _My5up3r$ tr0ngPa$ w0rd!_, который был указан при создании сервера базы данных.

### <a name="create-a-production-database"></a>Создание рабочей базы данных

В командной строке `mysql` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создание пользователя базы данных с именем _railsappuser_ и присвойте ему все привилегии в `sampledb` базы данных.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Подключение приложения к базе данных Azure MySQL

На этом шаге подключении Ruby в приложении, направляющие базы данных MySQL, созданных в базе данных Azure для MySQL (Предварительная версия).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Настройка подключения к базе данных

В репозитории, откройте _config/database.yml_. В нижней части файла Замените переменные производственного следующий код. Для  _&lt;mysql_server_name >_ заполнителя, используйте имя созданный сервер MySQL.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Сохраните изменения.

> [!NOTE]
> `sslca` Добавляется и указывает на существующий _.pem_ файла в репозитории примеров. По умолчанию база данных Azure для MySQL ограничивает SSL-соединений из клиентов. Это `.pem` сертификат является способ установки SSL-подключения к базе данных Azure для MySQL. Дополнительные сведения см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Локальное тестирование приложения

В локальном терминалов установите следующие переменные среды:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Запустите направляющие миграции базы данных со значениями производства, настроенных для создания таблиц в базе данных MySQL в базе данных Azure для MySQL (Предварительная версия). 

```bash
rake db:migrate RAILS_ENV=production
```

При работе в рабочей среде, направляющие приложению предкомпилированного активы. Создание необходимых средств, с помощью следующей команды:

```bash
rake assets:precompile
```

В рабочей среде направляющие также использует секреты для управления безопасностью. Создает закрытый ключ.

```bash
rails secret
```

Сохраните соответствующих переменных, используемых в рабочей среде направляющие секретный ключ. Для удобства используйте тот же ключ для обоих переменных.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Включите направляющие рабочей среде, чтобы обрабатывать файлы JavaScript и CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Запустите образец приложения в рабочей среде.

```bash
rails server -e production
```

Перейдите на страницу `http://localhost:3000`. Если страница загружается без ошибок, Ruby на направляющие приложения выполняется подключение к базы данных MySQL в Azure.

Добавьте несколько задач на странице.

![Ruby на направляющие успешно подключается к базе данных Azure для MySQL (Предварительная версия)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Чтобы остановить сервер направляющие, введите `Ctrl + C` в окне терминала.

### <a name="commit-your-changes"></a>Фиксация изменений

Чтобы зафиксировать изменения, выполните следующие команды Git:

```bash
git add .
git commit -m "database.yml updates"
```

Ваше приложение готово к развертыванию.

## <a name="deploy-to-azure"></a>Развернуть в Azure

На этом шаге подключен MySQL направляющие приложение развертывается в службе приложений Azure.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

В Cloud Shell создайте веб-приложение в рамках плана службы приложений `myAppServicePlan` с помощью команды [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

В следующем примере замените `<app_name>`глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`). Среда выполнения имеет значение `RUBY|2.3`, которая развертывает [Ruby изображение по умолчанию](https://hub.docker.com/r/appsvc/ruby/). Чтобы просмотреть все поддерживаемые среды выполнения, выполните команду [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение с включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>

### <a name="configure-database-settings"></a>Настройка параметров базы данных

В службе приложений устанавливаются переменные среды, как _параметры приложения_ с помощью [az webapp конфигурации appsettings набор](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) в оболочке облака.

Следующая команда оболочки облака настраивает параметры приложения `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, и `DB_PASSWORD`. Замените заполнители _&lt;appname>_ и _&lt;mysql_server_name>_ собственными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Настройка переменных среды направляющие

Локальный терминал создайте новый секретный ключ для рабочей среды направляющие в Azure.

```bash
rails secret
```

Настройка переменных, предусмотренного направляющие рабочей среде.

В следующей команде оболочки облака, замените две  _&lt;output_of_rails_secret >_ местозаполнителей новый секретный ключ, созданный локального терминала.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`Указывает значение по умолчанию контейнер Ruby для предварительной компиляции ресурсов во время каждого развертывания Git.

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

В локальном терминала добавьте Azure удаленного ваш локальный репозиторий Git.

```bash
git remote add azure <paste_copied_url_here>
```

Отправить в Azure удаленного развертывания Ruby на направляющие приложения. После этого введите пароль, указанный ранее в процессе создания пользователя развертывания.

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

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите по адресу `http://<app_name>.azurewebsites.net` и добавьте несколько задач в список.

![Ruby на направляющие приложения, запущенные в службе приложений Azure](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Поздравляем, вы используете Ruby, управляемые данными на направляющие приложения в службе приложений Azure.

## <a name="update-model-locally-and-redeploy"></a>Локальное обновление и повторное развертывание модели

На этом шаге вы внесете некоторые изменения в модель данных `task` и веб-приложение, а затем опубликуете обновления в Azure.

Для сценария с задачами необходимо изменить приложение, чтобы можно было пометить задачу как выполненную.

### <a name="add-a-column"></a>Добавление столбца

В окне терминала перейдите к корневой папке репозитория Git.

Создать новый миграции, который добавляет логическое столбец с именем `Done` для `Tasks` таблицы:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Эта команда создает файл переноса в _или переноса db_ каталога.


В окне терминала запустите направляющие миграции базы данных на внесение изменений в локальной базе данных.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Обновление логики приложения

Откройте *app/controllers/tasks_controller.rb* файла. В конце файла найдите следующую строку:

```rb
params.require(:task).permit(:Description)
```

Изменить эту строку, чтобы включить новый `Done` параметра.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Обновление представлений

Откройте *app/views/tasks/_form.html.erb* файл, который находится в форме редактирования.

Найдите строку `<%=f.error_span(:Description) %>` и вставьте следующий код непосредственно под ним:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Откройте *app/views/tasks/show.html.erb* файл, который является страница представления одной записи. 

Найдите строку `<dd><%= @task.Description %></dd>` и вставьте следующий код непосредственно под ним:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Откройте *app/views/tasks/index.html.erb* файл, который является страницей индекса для всех записей.

Найдите строку `<th><%= model_class.human_attribute_name(:Description) %></th>` и вставьте следующий код непосредственно под ним:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

В том же файле найдите строку `<td><%= task.Description %></td>` и вставьте следующий код непосредственно под ним:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Локальная проверка изменений

В локальном терминала запустите направляющие сервера.

```bash
rails server
```

Для просмотра состояния задачи изменения, перейдите в каталог `http://localhost:3000` и добавлять или изменять элементы.

![Добавлен флажок для задачи](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Чтобы остановить сервер направляющие, введите `Ctrl + C` в окне терминала.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала запустите направляющие миграции базы данных для рабочей среде, чтобы внести изменения в базе данных Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Зафиксируйте все изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

После выполнения команды `git push` перейдите в веб-приложение Azure и еще раз проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

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

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение к MySQL Ruby на направляющие приложения
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../app-service-web-tutorial-custom-domain.md)

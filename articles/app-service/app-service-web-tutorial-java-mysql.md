---
title: Создание веб-приложения Java в Azure с подключением к базе данных MySQL
description: Узнайте, как создать приложение Java, которое подключается к службе базы данных MySQL в Azure и работает в службе приложений Azure.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 46e222ffe40db186343250efc71e20d41adbc285
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203125"
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>Руководство. Создание веб-приложения Java в Azure с подключением к базе данных MySQL

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть приложение в службе приложений на платформе _Linux_, см. статью [Развертывание приложения Spring Boot в Azure с помощью подключаемого модуля Maven для веб-приложений Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

В этом руководстве показано, как создать веб-приложение Java в Azure и подключить его к базе данных MySQL. Когда вы выполните инструкции руководства, у вас будет приложение [Spring Boot](https://projects.spring.io/spring-boot/), данные которого хранятся в [базе данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/overview), которая работает в [веб-приложениях службы приложений Azure](app-service-web-overview.md).

![Приложение Java, работающее в службе приложений Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение примера приложения к базе данных
> * Развертывание приложения в Azure
> * Обновление и повторное развертывание приложения
> * Потоковая передача журналов диагностики из Azure.
> * Мониторинг приложения на портале Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

1. [Скачайте и установите Git](https://git-scm.com/)
1. [Скачайте и установите Java 7 JDK или более поздней версии](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) (этот компонент потребуется запустить) 

## <a name="prepare-local-mysql"></a>Подготовка локальной базы данных MySQL 

На этом шаге вы создадите базу данных на локальном сервере MySQL. Вы будете использовать ее для тестирования приложения на локальном компьютере.

### <a name="connect-to-mysql-server"></a>Подключение к серверу MySQL

В окне терминала подключитесь к локальному серверу MySQL. Используйте это окно терминала для выполнения всех команд в рамках этого руководства.

```bash
mysql -u root -p
```

Если появится предложение ввести пароль, введите пароль для учетной записи `root`. Если вы не помните пароль учетной записи привилегированного пользователя, ознакомьтесь с разделом [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: как сбросить пароль привилегированного пользователя).

Если команда выполнена успешно, значит, сервер MySQL уже работает. В противном случае обеспечьте запуск локального сервера MySQL, выполнив соответствующие [действия после установки MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Создание базы данных 

В командной строке `mysql` создайте базу данных и таблицу для элементов списка дел.

```sql
CREATE DATABASE tododb;
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Создание и запуск примера приложения 

На этом шаге вы клонируете пример приложения Spring Boot, настроите его для использования локальной базы данных MySQL и запустите на компьютере. 

### <a name="clone-the-sample"></a>Клонирования репозитория

В окне терминала перейдите в рабочую папку и клонируйте репозиторий. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Настройка приложения для использования базы данных MySQL

Замените `spring.datasource.password` и значение в каталоге *spring-boot-mysql-todo/src/main/resources/application.properties* корневым паролем, который вы использовали для открытия строки MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Соберите и запустите пример с помощью оболочки Maven, включенной в репозиторий:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Откройте в браузере адрес `http://localhost:8080`, чтобы увидеть пример в действии. По мере добавления задач в список используйте следующие команды SQL в строке MySQL, чтобы просмотреть данные, хранящиеся в базе данных MySQL.

```SQL
use testdb;
select * from todo_item;
```

Остановите приложение, нажав `Ctrl`+`C` в окне терминала. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Создание базы данных MySQL в Azure

На этом шаге вы создадите экземпляр [базы данных Azure для MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) с помощью [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Пример приложения для использования базы данных вы настроите позже в этом руководстве.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [`az group create`](/cli/azure/group#az_group_create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание и администрирование связанных ресурсов (веб-приложений, баз данных и учетных записей хранения). 

В следующем примере создается группа ресурсов в регионе "Северная Европа".

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Чтобы увидеть доступные значения для `--location`, выполните команду [`az appservice list-locations`](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

В Cloud Shell создайте сервер в службе "База данных Azure для MySQL", выполнив команду [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

В следующей команде замените заполнитель *\<mysql_server_name>* уникальным именем сервера, заполнитель *\<admin_user>* — именем пользователя, а заполнитель *\<admin_password>* — паролем. Это имя используется как часть конечной точки PostgreSQL (`https://<mysql_server_name>.mysql.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>--location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Так как в этом руководстве рассматриваются несколько учетных данных, чтобы избежать путаницы для `--admin-user` и `--admin-password` задаются фиктивные значения. В рабочей среде выберите имя пользователя и пароль для сервера MySQL в Azure, следуя рекомендациям по безопасности.
>
>

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
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
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

В Cloud Shell создайте правило брандмауэра для сервера MySQL, чтобы разрешить подключения клиентов, выполнив команду [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create). Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Вы можете применить еще более строгие ограничения в правиле брандмауэра, [разрешив только исходящие IP-адреса, используемые приложением](app-service-ip-addresses.md#find-outbound-ips).
>

## <a name="configure-the-azure-mysql-database"></a>Настройка базы данных MySQL в Azure

В окне терминала на локальном компьютере подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для заполнителя _&lt;mysql_server_name>_. При появлении запроса на ввод пароля используйте пароль, указанный во время создания базы данных в Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Создание базы данных 

В командной строке `mysql` создайте базу данных и таблицу для элементов списка дел.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных и предоставьте ему все привилегии в базе данных `tododb`. Замените заполнители `<Javaapp_user>` и `<Javaapp_password>` уникальным именем своего приложения.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Развертывание примера в службе приложений Azure

Создайте план службы приложений Azure с ценовой категорией **Бесплатный** с помощью команды CLI [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create). От плана службы приложений зависят физические ресурсы, используемые для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, которые позволяют сэкономить при размещении нескольких приложений. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

После создания плана в Azure CLI отобразится приблизительно такой результат:

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

### <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure

В Cloud Shell с помощью команды CLI [`az webapp create`](/cli/azure/appservice/web#az_appservice_web_create) создайте определение веб-приложения в плане службы приложений `myAppServicePlan`. Определение веб-приложения предоставляет URL-адрес для доступа к приложению и настраивает несколько параметров для развертывания кода в Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Замените заполнитель `<app_name>` уникальным именем своего приложения. Это уникальное имя используется в доменном имени по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить запись личного доменного имени с веб-приложением, прежде чем предоставлять его пользователям.

Когда вы создадите определение веб-приложения, в Azure CLI отобразятся следующие сведения: 

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

### <a name="configure-java"></a>Настройка Java 

В Cloud Shell настройте конфигурацию среды выполнения Java, необходимую для работы приложения, с помощью команды [`az webapp config set`](/cli/azure/webapp/config#az_webapp_config_set).

Следующая команда настраивает веб-приложение для запуска в Java 8 JDK и [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Настройка приложения для использования базы данных Azure SQL

Перед запуском примера приложения измените параметры веб-приложения, чтобы оно использовало базу данных Azure MySQL, созданную в Azure. Эти свойства доступны для веб-приложения как переменные среды. Они переопределяют значения, заданные в application.properties внутри упакованного веб-приложения. 

В Cloud Shell задайте параметры приложения с помощью команды CLI [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Получение учетных данных FTP для развертывания 
Для развертывания веб-приложения в службе приложений Azure можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и BitBucket. В этом примере используется FTP для развертывания WAR-файла, ранее созданного на локальном компьютере в службе приложений Azure.

Чтобы определить, какие учетные данные передавать в команде ftp для веб-приложения, выполните команду [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) в Cloud Shell. 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Отправка приложения с помощью FTP

Используйте любой FTP-клиент для развертывания WAR-файла в папку */site/wwwroot/webapps* по адресу сервера, который получен из поля `URL` в предыдущей команде. Удалите существующий каталог приложения по умолчанию (ROOT) и замените существующий файл ROOT.war WAR-файлом, созданном ранее в этом руководстве.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Тестирование веб-приложения

Перейдите по адресу `http://<app_name>.azurewebsites.net/` и добавьте несколько задач в список. 

![Приложение Java, работающее в службе приложений Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Поздравляем!** Вы запустили управляемое данными приложение Java в службе приложений Azure.

## <a name="update-the-app-and-redeploy"></a>Обновление и повторное развертывание приложения

Обновите приложение, чтобы включить в список дел дополнительный столбец для дня, когда был создан элемент. Приложение Spring Boot выполняет обновление схемы базы данных автоматически при изменении модели данных. При этом оно не меняет существующие записи базы данных.

1. На локальном компьютере, откройте файл *src/main/java/com/example/fabrikam/TodoItem.java* и добавьте в класс следующие операции импорта:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Добавьте свойство `String` `timeCreated` в файл *src/main/java/com/example/fabrikam/TodoItem.java*, инициализируя его с отметкой времени создания объекта. Во время редактирования этого файла добавьте методы получения и задания для нового свойства `timeCreated`.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Добавьте в файле *src/main/java/com/example/fabrikam/TodoDemoController.java* строку в метод `updateTodo`, чтобы задать метку времени:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Добавьте поддержку нового поля в шаблоне `Thymeleaf`. Добавьте в файл *src/main/resources/templates/index.html* новый заголовок таблицы для отметки времени и новое поле для отображения значения отметки времени в каждой строке таблицы данных.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Перестройте приложение:

    ```bash
    mvnw clean package 
    ```

6. С помощью FTP удалите существующий каталог *site/wwwroot/webapps/ROOT* и файл *ROOT.war*, а затем отправьте обновленный. WAR-файл как файл ROOT.war. 

После обновления приложения будет отображаться столбец **Время создания**. При добавлении новой задачи приложение заполнит отметку времени автоматически. Существующие задачи останутся без изменений и будут выполняться приложением, даже если исходная модель данных изменена. 

![Приложение Java, в которое добавлен новый столбец](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики 

При запуске приложения Java в службе приложений Azure можно передавать журналы консоли прямо в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) в Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное веб-приложение.

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-java-mysql/access-portal.png)

По умолчанию на странице веб-приложения отображается страница **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления, например завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

На вкладках страницы содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:
* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если эти ресурсы не требуются для изучения другого руководства (см. раздел [Дальнейшие действия](#next)), их можно удалить, выполнив следующие команды в Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Дополнительная информация

> [!div class="checklist"]
> * Создание базы данных MySQL в Azure.
> * Подключение примера приложения Java к базе данных MySQL
> * Развертывание приложения в Azure
> * Обновление и повторное развертывание приложения
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"] 
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)

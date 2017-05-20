---
title: "Создание веб-приложения Java в Azure с подключением к базе данных MySQL | Документация Майкрософт"
description: "Узнайте, как создать приложение Java, которое подключается к службе базы данных MySQL в Azure и работает в службе приложений Azure."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Создание веб-приложения Java в Azure с подключением к базе данных MySQL
В этом руководстве показано, как создать веб-приложение Java в Azure и подключить его к базе данных MySQL. Первым шагом является клонирование приложения на локальный компьютер и настройка его работы с локальным экземпляром MySQL.
Следующим шагом является установка служб Azure для приложения Java и MySQL, а затем — развертывание приложения в службе приложений Azure.
По завершении у вас будет приложение списка дел, работающее в Azure и подключенное к службе базы данных MySQL в Azure.

![Приложение Java, работающее в службе приложений Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>Перед началом работы

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Java 7 или более поздней версии](http://Java.net/downloads.Java);
1. [Maven](https://maven.apache.org/download.cgi);
1. [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) (этот компонент потребуется запустить); 
1. [Скачивание и установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>Подготовка локальной базы данных MySQL

На этом шаге вы создадите базу данных на локальном сервере MySQL для использования в этом руководстве.

### <a name="connect-to-mysql-server"></a>Подключение к серверу MySQL
Подключитесь к локальному серверу MySQL с помощью командной строки.

```bash
mysql -u root -p
```

Если команда выполнена успешно, значит, сервер MySQL уже работает. В противном случае обеспечьте запуск локального сервера MySQL, выполнив соответствующие [действия после установки MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Если появится предложение ввести пароль, введите пароль для учетной записи `root`. Если вы не помните пароль учетной записи привилегированного пользователя, ознакомьтесь с разделом [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: как сбросить пароль привилегированного пользователя).


### <a name="create-a-database-and-table"></a>Создание базы данных и таблицы

В командной строке `mysql` создайте базу данных и таблицу для элементов списка дел.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

## <a name="create-local-java-application"></a>Создание локального приложения Java
На этом шаге вы клонируете репозиторий GitHub, настроите подключение к базе данных MySQL и запустите приложение в локальной среде. 

### <a name="clone-the-sample"></a>Клонирования репозитория

В командной строке перейдите в рабочий каталог.  

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

Настройте lombok.jar согласно инструкциям в файле сведений репозитория.


### <a name="configure-mysql-connection"></a>Настройка подключения к MySQL

Это приложение использует подключаемый модуль Maven Jetty для запуска приложения в локальной среде и подключения к базе данных MySQL.
Чтобы разрешить доступ к локальному экземпляру MySQL, задайте идентификатор и пароль локального пользователя MySQL в файле WebContent/WEB-INF/jetty-env.xml.

Обновите значения имени пользователя и пароля, указав идентификатор пользователя и пароль для локального экземпляра MySQL.

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; Дополнительные сведения об использовании файла `jetty-env.xml` в Jetty см. в [справочнике по XML для Jetty](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html).
&gt; &gt;

### <a name="run-the-sample"></a>Запуск примера

Выполните команду Maven для запуска примера. 

```bash
mvn package jetty:run
```

Откройте браузер и перейдите по адресу `http://localhost:8080`. Добавьте несколько задач на странице.

Чтобы остановить приложение в любое время, введите `Ctrl`+`C` в командной строке. 

## <a name="create-a-mysql-database-in-azure"></a>Создание базы данных MySQL в Azure

На этом шаге вы создадите базу данных MySQL в [базе данных Azure для MySQL (предварительная версия)](/azure/mysql). Позже вы настроите приложение Java для подключения к этой базе данных.

### <a name="log-in-to-azure"></a>Вход в Azure

Используйте Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Java в службе приложений Azure. Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере создается группа ресурсов в регионе "Северная Европа".

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

Чтобы увидеть доступное значение для `--location`, выполните команду [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Создание сервера MySQL

Создайте сервер в базе данных Azure для MySQL (предварительная версия), выполнив команду [az mysql server create](/cli/azure/mysql/server#create).

В приведенной команде замените заполнитель `&lt;mysql_server_name&gt;` уникальным именем сервера MySQL. Это имя является частью имени узла сервера MySQL (`&lt;mysql_server_name&gt;.database.windows.net`), поэтому оно должно быть глобально уникальным. Кроме того, замените `&lt;admin_user&gt;` и `&lt;admin_password&gt;` собственными значениями.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

После создания сервера MySQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>Настройка брандмауэра сервера

Создайте правило брандмауэра для сервера MySQL, чтобы разрешить подключения клиентов, выполнив команду [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; База данных Azure для MySQL (предварительная версия) пока еще не позволяет использовать только подключения из служб Azure. Так как IP-адреса в Azure назначаются динамически, на данный момент рекомендуется включить все IP-адреса. Так как служба находится на этапе предварительной версии, в ближайшем будущем будут добавлены более надежные методы защиты базы данных.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>Подключение к серверу MySQL

В окне терминала подключитесь к серверу MySQL в Azure. Используйте значение, указанное ранее для `&lt;admin_user&gt;` и `&lt;mysql_server_name&gt;`.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Создание базы данных и таблицы в службе MySQL в Azure

В командной строке `mysql` создайте базу данных и таблицу для элементов списка дел.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных и предоставьте ему все привилегии в базе данных `todoItemDb`. Замените заполнители `&lt;Javaapp_user&gt;` и `&lt;Javaapp_password&gt;` уникальным именем своего приложения.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

Завершите подключение к серверу, введя команду `quit`.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>Настройка локального подключения MySQL с помощью новой службы MySQL в Azure

На этом шаге вы подключите приложение Java к базе данных MySQL, созданной в базе данных Azure для MySQL (предварительная версия). 

Чтобы разрешить доступ из локального приложения к службе MySQL в Azure, задайте новую конечную точку MySQL, идентификатор пользователя и пароль в файле WebContent/WEB-INF/jetty-env.xml.

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

Сохраните изменения.

## <a name="test-the-application"></a>Тестирование приложения

Используйте ту же команду Maven, что и раньше, чтобы еще раз запустить пример в локальной среде, но на этот раз подключитесь к службе MySQL в Azure. 

```bash
mvn package jetty:run
```

Откройте браузер и перейдите по адресу `http://localhost:8080`. Если страница загрузилась без ошибок, значит, приложение Java подключается к базе данных MySQL в Azure. 

Не следует добавлять задачи на странице.

Чтобы остановить приложение в любое время, введите `Ctrl`+`C` в окне терминала. 

### <a name="secure-sensitive-data"></a>Защита конфиденциальных данных

Убедитесь, что конфиденциальные данные в `WebContent/WEB-INF/jetty-env.xml` не фиксируются в Git.

Для этого откройте `.gitignore` из корня репозитория и добавьте `WebContent/WEB-INF/jetty-env.xml` в новой строке. Сохраните изменения.

Зафиксируйте изменения в `.gitignore`.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Развертывание приложения Java в Azure
Далее мы развернем приложение Java в службе приложений Azure.

### <a name="create-an-appservice-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

&gt; [!NOTE] 
&gt; План службы приложений представляет собой коллекцию физических ресурсов, используемых для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, определенные в нем. Поэтому, разместив несколько приложений, вы сможете сэкономить. 
&gt; &gt; План служб приложений определяет: &gt; &gt; * регион (Северная Европа, восточная часть США, Юго-Восточная Азия) &gt; * размер экземпляра (маленький, средний, большой) &gt; * число масштабируемых элементов (один, два, три экземпляра и т. д.) &gt; * номер SKU ("Бесплатный", "Общий", "Базовый", "Стандартный" и "Премиум")&gt;. 

В следующем примере создается план службы приложений `myAppServicePlan` в ценовой категории **Бесплатный**.

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображаются следующие сведения.

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
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure

После создания плана службы приложений `myAppServicePlan` создайте в нем веб-приложение Azure. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В следующей команде замените заполнитель `&lt;app_name&gt;` уникальным именем своего приложения. Это уникальное имя будет использоваться в доменном имени веб-приложения по умолчанию, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
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
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Задайте версию Java, тип сервера приложений Java и версию сервера приложений.

Задайте версию Java, сервер приложений Java (контейнер) и версию контейнера с помощью команды [az appservice web config update](/cli/azure/appservice/web/config#update).

Следующая команда задает версию Java 8, сервер приложений Java — Jetty и последнюю версию Jetty 9.3.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>Получение учетных данных для развертывания веб-приложения с помощью FTP 

Для развертывания веб-приложения в службе приложений Azure можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и BitBucket. В этом примере мы используем Maven для компиляции WAR-файла и FTP для развертывания WAR-файла в веб-приложении.

Чтобы определить, какие учетные данные передавать в команде ftp для веб-приложения, выполните команду [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles), как показано ниже. 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Компиляция локального приложения для развертывания в веб-приложении 

Чтобы подготовить локальное приложение Java для запуска в веб-приложении Azure, перекомпилируйте все ресурсы этого приложения Java в один WAR-файл, готовый для развертывания. Перейдите в каталог, в котором расположен файл pom.xml приложения, и введите следующую команду.

```bash 
mvn clean package
``` 
К концу процесса упаковки Maven вы увидите расположение WAR-файла.  Результат должен выглядеть следующим образом.

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

Запишите расположение WAR-файла и используйте свой избранный метод FTP для развертывания WAR-файла в папку Jetty WebApps.  Обратите внимание, что папка Jetty WebApps находится в каталоге /site/wwwroot/webapps в веб-приложении Azure. 

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите по адресу `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` и добавьте несколько задач в список. 

![Приложение Java, работающее в службе приложений Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**Поздравляем!** Вы запустили управляемое данными приложение Java в службе приложений Azure.
Чтобы обновить приложение, повторите команду Maven clean package и повторно разверните приложение с помощью FTP.

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на портал Azure, чтобы увидеть созданное веб-приложение, выполнив вход по адресу [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

Отобразится _колонка_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части колонки отображаются различные страницы конфигурации, которые можно открыть.

На странице **Параметры приложения** вы увидите следующее. 

![Параметры веб-приложения службы приложений Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

## <a name="more-resources"></a>Дополнительные ресурсы

- [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)
- [Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](app-service-web-tutorial-custom-ssl.md)
- [Сценарии интерфейса командной строки для веб-приложений](app-service-cli-samples.md)</PRE>


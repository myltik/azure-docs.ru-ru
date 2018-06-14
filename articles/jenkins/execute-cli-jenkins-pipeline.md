---
title: Выполнение Azure CLI с помощью Jenkins | Документация Майкрософт
description: Сведения об использовании Azure CLI для развертывания веб-приложения Java в Azure в конвейере Jenkins
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926936"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Развертывание в службу приложений Azure с помощью Jenkins и Azure CLI
Для развертывания веб-приложения Java в Azure можно использовать Azure CLI в [конвейере Jenkins](https://jenkins.io/doc/book/pipeline/). В этом учебнике мы создадим конвейер CI/CD на виртуальной машине Azure, включая следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Настройка Jenkins
> * Создание веб-приложения в Azure
> * Подготовка репозитория GitHub
> * Создание конвейера Jenkins
> * Запуск конвейера и проверка веб-приложения

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Создание и настройка экземпляра Jenkins
Если у вас нет главного экземпляра, воспользуйтесь [шаблоном решений](install-jenkins-solution-template.md), который по умолчанию содержит необходимый подключаемый модуль [Учетные данные Azure](https://plugins.jenkins.io/azure-credentials). 

Подключаемый модуль учетных данных Azure позволяет хранить учетные данные субъекта-службы Microsoft Azure в Jenkins. В версии 1.2 добавлена соответствующая поддержка, поэтому конвейер Jenkins может получать учетные данные Azure. 

Убедитесь, что у вас установлена версия 1.2 или более поздняя:
* На панели мониторинга Jenkins последовательно выберите **Manage Jenkins -> Plugin Manager ->** (Управление Jenkins -> Диспетчер подключаемых модулей) и выполните поиск **учетных данных Azure**. 
* Если используется версия более ранняя, чем 1.2, обновите подключаемый модуль.

В главном экземпляре Jenkins также требуются Java JDK и Maven. Чтобы выполнить установку, войдите в главный экземпляр с помощью SSH-подключения и выполните следующие команды:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Добавление субъекта-службы Azure в учетные данные Jenkins

Для выполнения Azure CLI необходимы учетные данные Azure.

* На панели мониторинга Jenkins выберите **Credentials -> System ->**(Учетные данные -> Система). Щелкните **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).
* Щелкните **Add Credentials** (Добавить учетные данные), чтобы добавить [субъект-службу Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) путем ввода следующих значений: идентификатор подписки, идентификатор клиента, секрет клиента и конечная точка маркера OAuth 2.0. Укажите идентификатор, который будет использоваться в следующем шаге.

![Добавить учетные данные](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Создание службы приложений Azure для развертывания веб-приложения Java

Создайте план службы приложений Azure с ценовой категорией **Бесплатный** с помощью команды CLI [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). От плана службы приложений зависят физические ресурсы, используемые для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, которые позволяют сэкономить при размещении нескольких приложений. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
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

 С помощью команды CLI [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) создайте определение веб-приложения в плане службы приложений `myAppServicePlan`. Определение веб-приложения предоставляет URL-адрес для доступа к приложению и настраивает несколько параметров для развертывания кода в Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

Настройте конфигурацию среды выполнения Java, необходимую для работы приложения, с помощью команды [az appservice web config update](/cli/azure/appservice/web/config#az_appservice_web_config_update).

Следующая команда настраивает веб-приложение для запуска в Java 8 JDK и [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Подготовка репозитория GitHub
Откройте репозиторий [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Простое веб-приложение Java для Azure). Чтобы создать разветвление репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Разветвление) в правом верхнем углу.

* В пользовательском веб-интерфейсе GitHub откройте файл **Jenkinsfile**. Щелкните значок с изображением карандаша, чтобы изменить этот файл для обновления группы ресурсов и имени веб-приложения в строках 20 и 21, соответственно.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Измените строку 23 для обновления идентификатора учетных данных в вашем экземпляре Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Создание конвейера Jenkins
Откройте Jenkins в веб-браузере, щелкните **New Item** (Создать элемент). 

* Укажите имя задания и выберите **Pipeline** (Конвейер). Последовательно выберите **ОК**.
* Откройте вкладку **Pipeline** (Конвейер), находящуюся рядом. 
* Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).
* Для параметра **SCM** выберите значение **Git**.
* Введите URL-адрес GitHub для разветвленного репозитория: https:\<разветвленный репозиторий\>.git
* Нажмите кнопку **Сохранить**

## <a name="test-your-pipeline"></a>Тестирование конвейера
* Перейдите созданный в конвейер и щелкните **Build Now** (Собрать).
* Процесс должен завершиться в течение нескольких секунд, после чего можно перейти к сборке и щелкнуть **Console Output** (Вывод консоли), чтобы просмотреть подробные сведения.

## <a name="verify-your-web-app"></a>Проверка веб-приложения
Чтобы проверить успешное развертывание WAR-файла в веб-приложении: Откройте веб-браузер:

* Перейдите по адресу: http://&lt;имя_приложения>.azurewebsites.net/api/calculator/ping.  
Вот что вы увидите:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Перейдите по адресу: http://&lt;имя_приложени>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (замените &lt;x> и &lt;y> любыми числами) для получения суммы x и y.

![Калькулятор: сложение](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Развертывание в веб-приложении Azure на платформе Linux
Теперь, когда вы знаете, как использовать Azure CLI в конвейере Jenkins, можно изменить сценарий для развертывания в веб-приложении Azure в Linux.

Для развертывания в веб-приложении в Linux поддерживается другой способ, который заключается в использовании Docker. Чтобы выполнить развертывание, необходимо предоставить файл Dockerfile, который упаковывает веб-приложение со службой среды выполнения в образ Docker. Затем подключаемый модуль создаст образ, отправит его в реестр Docker и развернет в веб-приложении.

* Выполните описанные [здесь](../app-service/containers/quickstart-nodejs.md) действия по созданию веб-приложения Azure на платформе Linux.
* Установите Docker в экземпляре Jenkins, следуя инструкциям в этой [статье](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Создайте реестр контейнеров на портале Azure, выполнив описанные [здесь](/azure/container-registry/container-registry-get-started-azure-cli) действия.
* В том же разветвленном репозитории [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Простое веб-приложение Java для Azure) измените файл **Jenkinsfile2**:
    * В строках 18–21 обновите имена группы ресурсов, веб-приложения и ACR, соответственно. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * В строке 24 обновите \<azsrvprincipal\> значением идентификатора учетных данных.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Создайте конвейер Jenkins, как вы делали это при развертывании в веб-приложение Azure в Windows, только на этот раз используйте **Jenkinsfile2**.
* Запустите новое задание.
* Чтобы осуществить проверку, в Azure CLI выполните следующую команду:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Вы получите следующий результат:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Перейдите по адресу: http://&lt;имя_приложения>.azurewebsites.net/api/calculator/ping. Отобразится сообщение: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Перейдите по адресу: http://&lt;имя_приложени>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (замените &lt;x> и &lt;y> любыми числами) для получения суммы x и y.
    
## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы настроили конвейер Jenkins, который извлекает исходный код в репозитории GitHub. Он запускает Maven для построения WAR-файла, а затем использует Azure CLI для развертывания в службу приложений Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Jenkins
> * Настройка Jenkins
> * Создание веб-приложения в Azure
> * Подготовка репозитория GitHub
> * Создание конвейера Jenkins
> * Запуск конвейера и проверка веб-приложения

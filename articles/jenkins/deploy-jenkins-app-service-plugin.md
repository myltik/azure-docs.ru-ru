---
title: "Развертывание в службе приложений Azure с помощью подключаемого модуля Jenkins | Документация Майкрософт"
description: "Узнайте, как развернуть веб-приложение Java в Azure в Jenkins с использованием подключаемого модуля Jenkins службы приложений Azure."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: dfb1abd4eefdb2baea8cdbd497bc8fcc95d200e6
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Развертывание в службе приложений Azure с использованием подключаемого модуля Jenkins 
Для развертывания веб-приложения Java в Azure можно использовать Azure CLI в [конвейере Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) или [подключаемый модуль Jenkins службы приложений Azure](https://plugins.jenkins.io/azure-app-service). Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка Jenkins для развертывания в службе приложений Azure по протоколу FTP 
> * Настройка Jenkins для развертывания в службе приложений Azure в Linux с помощью Docker 

## <a name="create-and-configure-jenkins-instance"></a>Создание и настройка экземпляра Jenkins
Если у вас нет главного экземпляра, воспользуйтесь [шаблоном решений](install-jenkins-solution-template.md), который содержит JDK8 и следующие необходимые подключаемые модули:

* [подключаемый модуль Jenkins клиента Git](https://plugins.jenkins.io/git-client) версии 2.4.6; 
* [подключаемый модуль Docker Commons](https://plugins.jenkins.io/docker-commons) версии 1.4.0;
* [учетные данные Azure](https://plugins.jenkins.io/azure-credentials) версии 1.2;
* [служба приложений Azure](https://plugins.jenkins.io/azure-app-server) версии 0.1.

Подключаемый модуль службы приложений можно использовать для развертывания веб-приложения на всех языках (например, C#, PHP, Java и Node.js и т. д.), поддерживаемых службой приложений Azure. В этом руководстве мы используем пример приложения Java, [простое веб-приложение Java для Azure](https://github.com/azure-devops/javawebappsample). Чтобы создать разветвление репозитория для своей учетной записи GitHub, нажмите кнопку **Fork** (Разветвление) в правом верхнем углу.  

Для создания проекта Java требуются Java JDK и Maven. Убедитесь, что установлены компоненты в главном узле Jenkins или агенте виртуальной машины при их использовании для обеспечения непрерывной интеграции. 

Чтобы выполнить установку, войдите в экземпляр Jenkins с помощью SSH-подключения и выполните следующие команды:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Для развертывания в службе приложений на платформе Linux также необходимо установить Docker в главном экземпляре Jenkins или агенте виртуальной машины, используемом для сборки. Сведения об установке Docker см. в этой статье: https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Добавление субъекта-службы Azure в учетные данные Jenkins

Для развертывания в Azure требуется субъект-служба Azure. 

<ol>
<li>Используйте [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) или [портал Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal) для создания субъекта-службы Azure.</li>
<li>На панели мониторинга Jenkins выберите **Credentials -> System ->**(Учетные данные -> Система). Щелкните **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).</li>
<li>Щелкните **Add Credentials** (Добавить учетные данные), чтобы добавить субъект-службу Microsoft Azure путем ввода следующих значений: идентификатор подписки, идентификатор клиента, секрет клиента и конечная точка маркера OAuth 2.0. Укажите идентификатор, **mySp**, который будет использоваться в следующем шаге.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Подключаемый модуль службы приложений Azure

Подключаемый модуль службы приложений Azure версии 1.0 поддерживает непрерывное развертывание в веб-приложение Azure через:

* Git и FTP;
* Docker для веб-приложения в Linux.

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Настройка Jenkins для развертывания веб-приложения по протоколу FTP с помощью панели мониторинга Jenkins

Чтобы развернуть проект в веб-приложении Azure, можно отправить артефакты сборки (например, WAR-файл на Java) с использованием Git или FTP.

Перед настройкой задания в Jenkins требуется план службы приложений Azure и веб-приложение для запуска приложения Java.


1. Создайте план службы приложений Azure с ценовой категорией **Бесплатный** с помощью команды CLI [az appservice plan create](/cli/azure/appservice/plan#create). От плана службы приложений зависят физические ресурсы, используемые для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, которые позволяют сэкономить при размещении нескольких приложений.
2. Создайте веб-приложение. Воспользуйтесь [порталом Azure](/azure/app-service-web/web-sites-configure) или следующей командой az интерфейса командной строки:
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. Настройте конфигурацию среды выполнения Java, необходимую вашему приложению. Следующая команда Azure CLI настраивает веб-приложение для запуска в Java 8 JDK и [Apache Tomcat](http://tomcat.apache.org/) 8.0.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Настройка задания Jenkins


1. Создайте **универсальный** проект на панели мониторинга Jenkins.
2. Настройте **управление исходным кодом** для использования локальной вилки [простого веб-приложения Java для Azure](https://github.com/azure-devops/javawebappsample), указав **URL-адрес репозитория**. Например: http://github.com/&lt;yourID>/javawebappsample.
3. Добавьте шаг сборки для создания проекта с помощью Maven. Для этого добавьте **оболочку выполнения**. В этом примере требуется дополнительный шаг для переименования WAR-файла в целевой папке на ROOT.war.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. Добавьте действие после сборки, выбрав **Publish an Azure Web App** (Публикация веб-приложения Azure).
5. Укажите mySp, субъект-службу Azure, которую мы сохранили на предыдущем шаге.
6. В разделе **Конфигурация приложения** выберите группу ресурсов и веб-приложение в своей подписке. Подключаемый модуль автоматически определяет платформу веб-приложения (Windows или Linux). Для веб-приложения на базе Windows появляется параметр Publish Files (Публикация файлов).
7. Заполните файлы, которые нужно развернуть (например, пакет в формате WAR, если вы используете Java). Исходный и целевой каталоги являются необязательными. Эти параметры позволяют указать исходную и целевую папки при отправке файлов. Веб-приложение Java в Azure выполняется на сервере Tomcat. Поэтому пакет в формате WAR передается в папку веб-приложений. В этом примере задайте для **исходного каталога** значение target, а для **целевого каталога** — webapps.
8. Если вы хотите выполнить развертывание в слот, отличный от рабочего, можно также задать имя **слота**.
9. Сохраните проект и создайте его. Веб-приложение развертывается в Azure после сборки.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Развертывание веб-приложения по протоколу FTP с помощью конвейера Jenkins

Подключаемый модуль готов к использованию в конвейере. Пример доступен в репозитории GitHub.

1. В пользовательском веб-интерфейсе GitHub откройте файл **Jenkinsfile_ftp_plugin**. Щелкните значок с изображением карандаша, чтобы изменить этот файл для обновления группы ресурсов и имени веб-приложения в строках 11 и 12, соответственно.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Измените строку 14 для обновления идентификатора учетных данных в вашем экземпляре Jenkins    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Создание конвейера Jenkins

1. Откройте Jenkins в веб-браузере, щелкните **New Item** (Создать элемент).
2. Укажите имя задания и выберите **Pipeline** (Конвейер). Нажмите кнопку **ОК**.
3. Откройте вкладку **Pipeline** (Конвейер), находящуюся рядом.
4. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).
5. Для параметра **SCM** выберите значение **Git**. Введите URL-адрес GitHub для разветвленного репозитория: https:&lt;разветвленный репозиторий>.git
6. Измените **путь к сценарию** на Jenkinsfile_ftp_plugin.
7. Нажмите кнопку **Сохранить** и запустите задание.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Настройка Jenkins для развертывания веб-приложения на платформе Linux с помощью Docker

Помимо Git и FTP веб-приложение в Linux поддерживает развертывания с помощью Docker. Чтобы выполнить развертывание с помощью Docker, необходимо предоставить файл Dockerfile, который упаковывает веб-приложение со службой среды выполнения в образ Docker. Затем подключаемый модуль создаст образ, отправит его в реестр Docker и развернет в веб-приложении.

Веб-приложения на платформе Linux также поддерживают традиционные способы, такие как Git и FTP, но только для встроенных языков (.NET Core, Node.js, PHP и Ruby). Для других языков необходимо объединить код приложения и среду выполнения службы в образ Docker и использовать Docker для развертывания.

Перед настройкой задания в Jenkins вам понадобится служба приложений Azure в Linux, а также реестр контейнеров для хранения частных образов контейнера Docker и управления ими. Вы можете использовать DockerHub. В этом примере используется реестр контейнеров Azure.

* Выполните действия, указанные [здесь](/azure/app-service-web/app-service-linux-how-to-create-web-app), для создания веб-приложения в Linux. 
* Реестр контейнеров Azure — это управляемая служба [реестра Docker] (https://docs.docker.com/registry/) на базе реестра Docker версии 2.0 с открытым кодом. Дополнительные сведения об этом см. [здесь] (/azure/container-registry/container-registry-get-started-azure-cli). Кроме того, можно использовать DockerHub.

### <a name="to-deploy-using-docker"></a>Развертывание с помощью Docker

1. Создайте универсальный проект на панели мониторинга Jenkins.
2. Настройте **управление исходным кодом** для использования локальной вилки [простого веб-приложения Java для Azure](https://github.com/azure-devops/javawebappsample), указав **URL-адрес репозитория**. Например: http://github.com/&lt;yourid>/javawebappsample.
Добавьте шаг сборки для создания проекта с помощью Maven. Для этого добавьте **оболочку выполнения** и следующую строку в разделе **Команда**:    
```bash
mvn clean package
```

3. Добавьте действие после сборки, выбрав **Publish an Azure Web App** (Публикация веб-приложения Azure).
4. Укажите **mySp**, субъект-службу Azure, которую мы сохранили на предыдущем шаге в качестве учетных данных Azure.
5. В разделе **Конфигурация приложения** выберите группу ресурсов и веб-приложение Linux в своей подписке.
6. Выберите публикацию через Docker.
7. Заполните путь **Dockerfile**. Вы можете сохранить значение по умолчанию /Dockerfile для **URL-адреса реестра Docker. При использовании реестра контейнеров Azure** укажите его в формате https://&lt;myRegistry >.azurecr.io. Не указывайте его при использовании DockerHub.
8. Добавьте **учетные данные реестра** для реестра контейнеров Azure. Идентификатор пользователя и пароль можно получить, выполнив следующие команды в Azure CLI. Первая команда включает учетную запись администратора.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. Имя образа Docker и тег на вкладке **Дополнительно** являются необязательными. По умолчанию имя образа можно получить из имени образа, настроенного на портале Azure (в параметрах контейнера Docker). Тег создается на основе $BUILD_NUMBER. Убедитесь, что имя образа задано на портале Azure, или задайте значение для **образа Docker** на вкладке **Дополнительно**. В этом примере укажите &lt;yourRegistry >.azurecr.io/calculator в качестве значения для **образа Docker** и не указывайте **тег образа Docker**.
10. Примечание. Развертывание завершается сбоем, если использовать параметр встроенного образа Docker. Обязательно настройте конфигурацию Docker для использования пользовательского образа в контейнере Docker на портале Azure. Для развертывания встроенного изображения используйте подход с передачей файла.
11. Кроме того, можно выбрать слот, отличный от рабочего. Этот способ аналогичный способу передачи файла.
12. Сохраните и создайте проект. После этого образ контейнера помещается в реестр, а веб-приложение развертывается.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Развертывание в веб-приложении на платформе Linux с помощью Docker с использованием конвейера Jenkins

1. В пользовательском веб-интерфейсе GitHub откройте файл **Jenkinsfile_container_plugin**. Щелкните значок с изображением карандаша, чтобы изменить этот файл для обновления группы ресурсов и имени веб-приложения в строках 11 и 12, соответственно.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Измените строку 13 на сервер контейнера реестра.    
```java
def registryServer = '<registryURL>'
```    

3. Измените строку 16 для обновления идентификатора учетных данных в своем экземпляре Jenkins.    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Создание конвейера Jenkins    

1. Откройте Jenkins в веб-браузере, щелкните **New Item** (Создать элемент).
2. Укажите имя задания и выберите **Pipeline** (Конвейер). Нажмите кнопку **ОК**.
3. Откройте вкладку **Pipeline** (Конвейер), находящуюся рядом.
4. Для параметра **Definition** (Определение) выберите значение **Pipeline script from SCM** (Сценарий конвейера из SCM).
5. Для параметра **SCM** выберите значение **Git**.
6. Введите URL-адрес GitHub для разветвленного репозитория: https:&lt;разветвленный репозиторий>.git</li>
Измените **путь к сценарию** на Jenkinsfile_container_plugin.
8. Нажмите кнопку **Сохранить** и запустите задание.

## <a name="verify-your-web-app"></a>Проверка веб-приложения

1. Чтобы проверить успешное развертывание WAR-файла в веб-приложении: Откройте веб-браузер.
2. Перейдите по адресу http://&lt;имя_приложения>.azurewebsites.net/api/calculator/ping. Отобразится следующее сообщение:    
     Добро пожаловать в веб-приложение Java!!! Это обновленная версия!
   17 июня, воскресенье, 16:39:10 UTC, 2017 г.
3. Перейдите по адресу: http://&lt;имя_приложени>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (замените &lt;x> и &lt;y> любыми числами) для получения суммы x и y.        
    ![Калькулятор: сложение](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Для службы приложений под управлением Linux

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
    
## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили развертывание в Azure с помощью подключаемого модуля службы приложений Azure.

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка Jenkins для развертывания в службе приложений Azure по протоколу FTP. 
> * Настройка Jenkins для развертывания в службе приложений Azure в Linux с помощью Docker. 

---
title: Развертывание веб-приложений в Azure с помощью Jenkins | Документация Майкрософт
description: Сведения о настройке непрерывной интеграции из GitHub в службу приложений Azure для веб-приложений Java с помощью Jenkins и Docker.
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: ''
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29853005"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Настройка непрерывной интеграции и развертывания в службе приложений Azure с помощью Jenkins и GitHub

Это руководство содержит сведения о настройке непрерывной интеграции и развертывания примеров веб-приложения Java, разработанного на платформе [Spring Boot](http://projects.spring.io/spring-boot/), в [веб-приложение службы приложений Azure на платформе Linux](/azure/app-service/containers/app-service-linux-intro) с помощью Jenkins.

В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * Установите подключаемые модули Jenkins, необходимые для развертывания в службу приложений Azure.
> * Определите задание Jenkins для разработки образов Docker из репозитория GitHub при выполнении новой фиксации.
> * Определите новое веб-приложение Azure для Linux и настроите его для развертывания образов Docker, отправленных в реестр контейнеров Azure.
> * Настроите подключаемый модуль Jenkins службы приложений Azure.
> * Развернете пример приложения в службе приложений Azure с помощью ручной сборки.
> * Активируете сборку Jenkins и обновите веб-приложение, добавив изменения в GitHub.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этим учебником необходимы указанные ниже компоненты.

* [Jenkins](https://jenkins.io/) с настроенными инструментами JDK и Maven. Если у вас нет системы Jenkins, создайте ее сейчас в Azure, используя [шаблон решения Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Учетная запись [GitHub](https://github.com).
* [Azure CLI 2.0](/cli/azure) в локальной командной строке или в [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Установка подключаемых модулей Jenkins

1. Откройте веб-браузер и перейдите к веб-консоли Jenkins. В меню слева выберите **Manage Jenkins** (Управление Jenkins), а затем — **Manage Plugins** (Управление подключаемыми модулями).
2. Перейдите на вкладку **Available** (Доступно).
3. Найдите и установите флажок рядом со следующими подключаемыми модулями:   

    - [подключаемый модуль службы приложений Azure](https://plugins.jenkins.io/azure-app-service);
    - [подключаемый модуль источника ветви GitHub](https://plugins.jenkins.io/github-branch-source).

    Если подключаемые модули не отображаются, перейдите на вкладку **Installed** (Установлено) и убедитесь, что они установлены.

1. Выберите **Download now and install after restart** (Скачать сейчас и установить после перезагрузки), чтобы активировать подключаемые модули в конфигурации Jenkins.

## <a name="configure-github-and-jenkins"></a>Настройка GitHub и Jenkins

Настройте Jenkins, чтобы получить [веб-перехватчики GitHub](https://developer.github.com/webhooks/) при переносе новых фиксаций в репозиторий учетной записи.

1. Выберите **Manage Jenkins** (Управление Jenkins), а затем — **Configure System** (Настройка системы). В разделе **GitHub** убедитесь, что установлен флажок **Manage hooks** (Управление перехватчиками), а затем выберите **Manage additional GitHub actions** (Управление дополнительными действиями GitHub) и **Convert login and password to token** (Преобразовать имя пользователя и пароль в токен).
2. Установите переключатель **From login and password** (Из имени пользователя и пароля) и введите имя пользователя и пароль в GitHub. Выберите **Create token credentials** (Создать учетные данные токена), чтобы создать [личный маркер доступа к GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Создание личного маркера доступа к GitHub с помощью имени пользователя и пароля](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Выберите созданный маркер в раскрывающемся списке **Credentials** (Учетные данные) в конфигурации серверов GitHub. Выберите **Test connection** (Проверка подключения), чтобы убедиться в исправности аутентификации.   
   ![Проверка подключения в GitHub после настройки личного маркера доступа](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Если ваша учетная запись GitHub использует двухфакторную проверку подлинности, создайте маркер в GitHub и настройте Jenkins для его использования. Полные сведения можно просмотреть в документации о [подключаемых модулях Jenkins в GitHub](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Копирование примера репозитория и создание задания Jenkins 

1. Откройте [репозиторий с примером приложения Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) и скопируйте его в свою учетную запись GitHub, нажав в правом верхнем углу кнопку **Fork** (Разветвление).   
    ![Копирование из GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. В веб-консоли Jenkins выберите **New Item** (Создать элемент), присвойте ему имя **MyJavaApp**, выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.   
    ![Новый универсальный проект Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. В разделе **General** (Общие) выберите проект **GitHub** и введите URL-адрес разветвления репозитория, например https://github.com/raisa/gs-spring-boot-docker.
3. В разделе **Source code management** (Управление исходным кодом) выберите **Git** и введите URL-адрес `.git` разветвления репозитория, например https://github.com/raisa/gs-spring-boot-docker.git.
4. В разделе **Build Triggers** (Создание триггеров) выберите **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm).
5. В разделе **Build** (Сборка) выберите **Add build step** (Добавить шаг сборки) и щелкните **Invoke top-level Maven targets** (Вызов целевых объектов Maven верхнего уровня). В поле **Goals** (Цели) введите `package`.
6. Щелкните **Сохранить**. Чтобы проверить задание, выберите **Build Now** (Собрать сейчас) на странице проекта.

## <a name="configure-azure-app-service"></a>Настройка службы приложений Azure 

1. Создайте [веб-приложение в Linux](/azure/app-service/containers/app-service-linux-intro) с помощью Azure CLI или [Cloud Shell](/azure/cloud-shell/overview). В этом руководстве веб-приложение имеет имя `myJavaApp`, но для своего приложения укажите уникальное имя.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Создайте [реестр контейнеров Azure](/azure/container-registry/container-registry-intro), чтобы хранить образы Docker, созданные Jenkins. В этом руководстве реестр контейнеров имеет имя `jenkinsregistry`, однако для своего реестра укажите уникальное имя. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Настройте веб-приложение для запуска образов Docker, отправленных в реестр контейнеров. Укажите, что приложение, запущенное в контейнере, прослушивает запросы порта 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Настройка подключаемого модуля Jenkins службы приложений Azure

1. В веб-консоли Jenkins выберите созданное задание **MyJavaApp**, а затем щелкните **Configure** (Настройка) в левой части страницы.
2. Прокрутите вниз до раздела **Post-build Actions** (Действия после сборки), выберите **Add post-build action** (Добавление действия после сборки), а затем — **Publish an Azure Web App** (Публикация веб-приложения Azure).
3. В разделе **Azure Profile Configuration** (Конфигурация профиля Azure) щелкните **Add** (Добавить) рядом с **учетными данными Azure**, а затем выберите **Jenkins**.
4. В диалоговом окне **добавления учетных данных** выберите **субъект-службу Microsoft Azure** в раскрывающемся списке **Kind** (Вид).
5. Создайте субъект-службу Active Directory с помощью Azure CLI или [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Введите учетные данные из субъекта-службы в диалоговом окне **добавления учетных данных**. Если вы не знаете идентификатор подписки Azure, выполните следующий запрос в интерфейсе командной строки:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Настройка субъекта-службы Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Проверьте аутентификацию субъекта-службы в Azure, выбрав **Verify Service Principal** (Проверить субъект-службу). 
7. Щелкните **Add** (Добавить), чтобы сохранить учетные данные.
8. Выберите учетные данные субъекта-службы, которые вы добавили в раскрывающемся списке **учетных данных Azure**, когда вернулись к конфигурации **Publish an Azure Web App** (Публикация веб-приложения Azure).
9. В **параметрах приложения** выберите в раскрывающемся списке имя группы ресурсов и веб-приложения.
10. Установите переключатель **Publish via Docker** (Публикация через Docker).
11. Введите `complete/Dockerfile` в **путь Dockerfile**.
12. Введите `https://jenkinsregistry.azurecr.io` в поле **URL-адреса реестра Docker**.
13. Выберите **Add** (Добавить) рядом с **учетными данными реестра**. 
14. Введите имя администратора реестра контейнера Azure, созданного в качестве **имени пользователя**.
15. Введите пароль реестра контейнеров Azure в поле **пароля**. Имя пользователя и пароль можно получить на портале Azure или выполнив следующую команду интерфейса командной строки:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Добавление учетных данных реестра контейнеров](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Щелкните **Add** (Добавить), чтобы сохранить учетные данные.
16. Выберите созданные учетные данные в раскрывающемся списке **учетных данных реестра** на панели **конфигурации приложения** в окне **Publish an Azure Web App** (Публикация веб-приложения Azure). Готовое действие после сборки должно выглядеть следующим образом:   
    ![Настройка действия после сборки для развертывания службы приложений Azure](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Чтобы сохранить конфигурацию, выберите **Save** (Сохранить).

## <a name="deploy-the-app-from-github"></a>Развертывание приложения из GitHub

1. В проекте Jenkins выберите **Build Now** (Собрать сейчас), чтобы развернуть пример приложения в Azure.
2. После завершения сборки приложение станет активным в Azure по URL-адресу публикации, например http://myjavaapp.azurewebsites.net.   
   ![Просмотр развернутого приложения в Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Отправка изменений и повторное развертывание

1. Найдите файл `complete/src/main/java/Hello/Application.java` в Интернете, используя разветвление Github. Щелкните ссылку **Edit this file** (Изменить файл) в правой части интерфейса GitHub.
2. Внесите следующие изменения в метод `home()` и зафиксируйте их в главной ветви репозитория.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. В Jenkins запустится новая сборка, активированная новой фиксацией ветви `master` репозитория. После завершения перезагрузите приложение в Azure.     
      ![Просмотр развернутого приложения в Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Дополнительная информация

- [Использование агентов виртуальных машин для непрерывной интеграции с Jenkins](/azure/jenkins/jenkins-azure-vm-agents)
- [Развертывание в службу приложений Azure с помощью Jenkins и Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline)
 

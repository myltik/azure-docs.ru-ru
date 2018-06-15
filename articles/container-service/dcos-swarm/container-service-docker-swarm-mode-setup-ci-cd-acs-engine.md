---
title: Непрерывная интеграция и непрерывное развертывание с использованием обработчика службы контейнеров Azure и Swarm Mode
description: Используйте обработчик Службы контейнеров Azure с Docker Swarm Mode, реестром контейнеров Azure и Visual Studio Team Services для непрерывной доставки многоконтейнерного приложения .NET Core.
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 01126f3eef988eb1787bafea92e7384aad1a703c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179577"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Полный конвейер непрерывной интеграции и развертывания многоконтейнерного приложения в Службе контейнеров Azure с обработчиком ACS и Docker Swarm Mode при помощи Visual Studio Team Services

*Эта статья основана на [документации по ](container-service-docker-swarm-setup-ci-cd.md)полному конвейеру непрерывной интеграции и развертывания многоконтейнерного приложения в Службе контейнеров Azure с Docker Swarm с использованием Visual Studio Team Services*.

Сегодня одной из основных сложностей при разработке современных приложений для облака является возможность предоставлять эти приложения непрерывно. Из этой статьи вы узнаете, как реализовать полный конвейер непрерывной интеграции и развертывания (далее — конвейер CI/CD) с использованием следующих компонентов: 
* обработчик Службы контейнеров Azure с Docker Swarm Mode;
* реестр контейнеров Azure;
* Visual Studio Team Services.

В этой статье используется простое приложение, доступное в [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) и разработанное с помощью ASP.NET Core. Это приложение состоит из четырех различных служб: трех веб-API и одного веб-интерфейса.

![Пример приложения MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Цель — непрерывная доставка приложения в кластере с Docker Swarm Mode при помощи Visual Studio Team Services. На следующем рисунке детально представлен этот конвейер непрерывной доставки.

![Пример приложения MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Вот краткое описание действий:

1. Изменения кода фиксируются в репозитории исходного кода (здесь GitHub). 
2. GitHub активирует сборку в Visual Studio Team Services. 
3. Visual Studio Team Services получает последнюю версию источников и создает все образы, из которых состоит приложение. 
4. Visual Studio Team Services помещает каждый образ в реестр Docker, созданный с помощью службы реестра контейнеров Azure. 
5. Visual Studio Team Services активирует новый выпуск. 
6. Выпуск выполняет некоторые команды с помощью SSH в главном узле кластера Службы контейнеров Azure. 
7. Docker Swarm Mode в кластере запрашивает последнюю версию образов. 
8. Новая версия приложения развертывается с помощью Docker Stack. 

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством вам потребуется выполнить следующие задачи:

- [Создать кластер Swarm Mode в Службе контейнеров Azure с обработчиком ACS.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Подключиться к кластеру Swarm в службе контейнеров Azure.](../container-service-connect.md)
- [Создать реестр контейнеров Azure](../../container-registry/container-registry-get-started-portal.md)
- [Создать учетную запись и командный проект Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Скопировать репозиторий GitHub в свою учетную запись GitHub.](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Оркестратор Docker Swarm в службе контейнеров Azure использует автономную устаревшую версию Swarm. Сейчас интегрированный [режим Swarm](https://docs.docker.com/engine/swarm/) (в Docker версии 1.12 или выше) не является поддерживаемым оркестратором в службе контейнеров Azure. По этой причине мы используем [обработчик ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), предоставленный сообществом [шаблон для быстрого начала работы](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), или решение Docker из [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Шаг 1. Настройка учетной записи Visual Studio Team Services 

В этом разделе настраивается учетная запись Visual Studio Team Services. Чтобы настроить конечные точки служб VSTS, в проекте Visual Studio Team Services на панели инструментов щелкните значок **Параметры** и выберите **Службы**.

![Откройте конечную точку служб](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Подключение Visual Studio Team Services к учетной записи Azure

Настройте подключение между проектом VSTS и учетной записью Azure.

1. Слева выберите **Создать конечную точку службы** > **Azure Resource Manager**.
2. Чтобы авторизовать VSTS для работы с учетной записью Azure, выберите свою **подписку** и нажмите кнопку **ОК**.

    ![Visual Studio Team Services — авторизация в Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Подключение между Visual Studio Team Services и GitHub

Настройте подключение между проектом VSTS и учетной записью GitHub.

1. Слева выберите **Создать конечную точку службы** > **GitHub**.
2. Чтобы авторизовать VSTS для работы с вашей учетной записью GitHub, щелкните **Авторизовать** и следуйте указаниям в открывшемся окне.

    ![Visual Studio Team Services — авторизация GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Подключение VSTS к кластеру Службы контейнеров Azure

Последнее, что нужно сделать перед началом работы с конвейером CI/CD, — это настроить внешние подключения к кластеру Docker Swarm в Azure. 

1. Для кластера Docker Swarm добавьте конечную точку типа **SSH**. Затем введите сведения о SSH-подключении кластера Swarm (главный узел).

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Настройка завершена. На следующих шагах создается конвейер CI/CD, который создает и развертывает приложение в кластере Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Шаг 2: Создание определения сборки

На этом шаге настраивается определение сборки для проекта VSTS и определяется рабочий процесс сборки для образов контейнера.

### <a name="initial-definition-setup"></a>Начальная настройка определения

1. Чтобы создать определение сборки, подключитесь к проекту Visual Studio Team Services и щелкните **Build & Release** (Сборка и выпуск). В разделе **Определения сборки** щелкните **+ Создать**. 

    ![Visual Studio Team Services — новое определение сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Выберите **Пустой процесс**.

    ![Visual Studio Team Services — новое пустое определение сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Затем щелкните вкладку **Переменные** и создайте две новые переменные: **RegistryURL** и **AgentURL**. Вставьте значения для DNS реестра и агентов кластера.

    ![Visual Studio Team Services — конфигурация переменных сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. На странице **Определения сборки** сначала откройте вкладку **Триггеры** и настройте сборку для использования непрерывной интеграции с разветвлением проекта MyShop, созданного на этапе предварительных требований. Затем выберите **Пакетные изменения**. Убедитесь, что для параметра **Спецификация ветви** выбрано значение *docker-linux*.

    ![Visual Studio Team Services — конфигурация репозитория сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. В завершение щелкните вкладку **Параметры** и укажите для очереди агента по умолчанию значение **Hosted Linux Preview** (Размещенная предварительная версия Linux).

    ![Visual Studio Team Services — конфигурация агента узла](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Определение рабочего процесса сборки
Дальнейшие действия определяют рабочий процесс сборки. Сначала необходимо настроить источник кода. Для этого выберите **GitHub**, свой **репозиторий** и **ветвь** (docker-linux).

![Visual Studio Team Services — настройка источника кода](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Для сборки приложения *MyShop* доступно пять образов контейнера. Каждый образ создается с помощью Dockerfile, расположенного в папках проекта:

* ProductsApi;
* Прокси-сервер
* RatingsApi;
* RecommandationsApi;
* ShopFront.

Для каждого образа нужны два шага Docker: один — для создания образа, а другой — для передачи образа в реестр контейнеров Azure. 

1. Чтобы добавить шаг в рабочий процесс сборки, щелкните **Добавить шаг сборки...** и выберите **Docker**.

    ![Visual Studio Team Services — добавление шагов сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Для каждого образа настройте один шаг, в котором используется команда `docker build`.

    ![Visual Studio Team Services — сборка Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Для операции сборки выберите свой реестр контейнеров Azure, действие **Build an image** (Создать образ) и файл Dockerfile, определяющий каждый образ. В качестве **рабочей папки** укажите корневой каталог Dockerfile, определите **имя образа** и выберите **Включить последний тег**.
    
    Имя образа должно быть представлено в формате ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Замените **[NAME]** именем образа:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Для каждого образа настройте второй шаг, на котором используется команда `docker push`.

    ![Visual Studio Team Services — принудительная отправка Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Чтобы выполнить операцию отправки, выберите свой реестр контейнеров Azure, действие **Push an image** (Отправить образ), в соответствующем поле укажите **имя образа**, введенное на предыдущем шаге, и выберите **Включить последний тег**.

4. Настроив действия сборки и отправки для каждого из пяти образов, добавьте еще три действия в рабочий процесс сборки.

   ![Visual Studio Team Services — добавление задачи командной строки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Задача командной строки, которая использует bash-скрипт для замены вхождения *RegistryURL* в файле docker-compose.yml переменной RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services — обновление файла Compose с URL-адресом реестра](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Задача командной строки, которая использует bash-скрипт для замены вхождения *AgentURL* в файле docker-compose.yml переменной AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Задача, которая удаляет обновленный файл Compose в качестве артефакта сборки, чтобы его можно было использовать в выпуске. Подробнее показано на следующем снимке экрана.

         ![Visual Studio Team Services — публикация артефакта](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services — публикация файла Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Щелкните **Сохранить и поместить в очередь**, чтобы проверить определение сборки.

   ![Visual Studio Team Services — сохранение и помещение в очередь](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services — новая очередь](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Если **сборка** выполнена правильно, должен отобразиться такой экран:

  ![Visual Studio Team Services — сборка выполнена успешно](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Шаг 3. Создание определения выпуска

Visual Studio Team Services позволяет [управлять выпусками в разных средах](https://www.visualstudio.com/team-services/release-management/). Вы можете включить непрерывное развертывание, чтобы ваше приложение беспрепятственно развертывалось в разных средах (например, в среде разработки, тестирования, подготовки и рабочей среде). Вы также можете создать среду, которая представляет кластер Docker Swarm Mode Службы контейнеров Azure.

![Visual Studio Team Services — выпуск в ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Начальная настройка выпуска

1. Чтобы создать определение выпуска, щелкните **Выпуски** > **+ Выпуск**.

2. Чтобы настроить источник артефакта, выберите **Артефакты** > **Link an artifact source** (Связать источник артефакта). Свяжите это новое определение выпуска со сборкой, указанной на предыдущем шаге. После этого файл docker-compose.yml появится в процессе выпуска.

    ![Visual Studio Team Services — артефакты выпуска](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Чтобы настроить триггер выпуска, выберите **Триггеры** и **Непрерывное развертывание**. Установите триггер в том же источнике артефакта. Этот параметр обеспечивает запуск нового выпуска после успешного завершения сборки.

    ![Visual Studio Team Services — триггеры выпуска](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Чтобы настроить переменные выпуска, щелкните **Переменные** и выберите **+Переменная**, чтобы создать три новые переменные с данными из реестра: **docker.username**, **docker.password** и **docker.registry**. Вставьте значения для DNS реестра и агентов кластера.

    ![Visual Studio Team Services — конфигурация репозитория сборки](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Как показано на предыдущем снимке экрана, щелкните флажок **блокировки** в docker.password. Этот параметр важен для ограничения пароля.
    >

### <a name="define-the-release-workflow"></a>Определение рабочего процесса выпуска

Рабочий процесс выпуска состоит из двух задач, которые необходимо добавить.

1. Настройте задачу для безопасного копирования файла Compose в папку *deploy* на главном узле Docker Swarm через подключение SSH, настроенное ранее. Подробнее показано на следующем снимке экрана.
    
    Исходная папка: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services — SCP выпуска](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Настройте вторую задачу для выполнения команды bash, чтобы запустить команды `docker` и `docker stack deploy` на главном узле. Подробнее показано на следующем снимке экрана.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services — bash-команда выпуска](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    На главном узле выполняется CLI-команда Docker и Docker-Compose, выполняющая следующие задачи:

    - вход в реестр контейнеров Azure (в нем используются три переменные сборки, указанные на вкладке **Переменные**);
    - определение переменной **DOCKER_HOST** для работы с конечной точкой Swarm (:2375);
    - переход к папке *deploy*, созданной при выполнении предыдущей задачи безопасного копирования, содержащей файл docker-compose.yml; 
    - выполнение команд `docker stack deploy`, которые извлекают новые образы и создают контейнеры.

    >[!IMPORTANT]
    > Как показано на предыдущем снимке экрана, не устанавливайте флажок **Ошибка в STDERR**. Этот параметр позволяет завершить процесс выпуска, так как `docker-compose` выводит несколько диагностических сообщений, например об остановке контейнеров или их удалении, в стандартном выводе данных об ошибках. Если флажок установлен, Visual Studio Team Services сообщает об ошибках, произошедших во время выпуска, даже если все работает правильно.
    >
3. Сохраните это новое определение выпуска.

## <a name="step-4-test-the-cicd-pipeline"></a>Шаг 4. Тестирование конвейера CI/CD

Теперь после выполнения настройки пришло время протестировать этот новый конвейер CI/CD. Самый простой способ сделать это — обновить исходный код и сохранить изменения в репозитории GitHub. Через несколько секунд после отправки кода в Visual Studio Team Services появится новая сборка. После успешного выполнения новый выпуск активируется и разворачивает новую версию приложения в кластере Службы контейнеров Azure.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о CI/CD с Visual Studio Team Services см. в [обзоре сборки VSTS](https://www.visualstudio.com/docs/build/overview).
* Дополнительные сведения об обработчике ACS см. в [репозитории GitHub для обработчика ACS](https://github.com/Azure/acs-engine).
* Дополнительные сведения о Docker Swarm Mode см. в [этой статье](https://docs.docker.com/engine/swarm/).

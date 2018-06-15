---
title: Непрерывная интеграция и непрерывное развертывание со использованием службы контейнеров Azure и Swarm
description: Использование Службы контейнеров Azure с Docker Swarm, реестром контейнеров Azure и Visual Studio Team Services для непрерывной доставки многоконтейнерного приложения .NET Core
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 81a07fdfe1c862bc30fb9d567db9a393c0610990
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179560"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Полный конвейер средств непрерывной интеграции и доставки для развертывания многоконтейнерного приложения в Службе контейнеров Azure с Docker Swarm с использованием Visual Studio Team Services

Одна из основных сложностей при разработке современных приложений для облака — возможность предоставлять эти приложения непрерывно. Из этой статьи вы узнаете, как реализовать полный конвейер непрерывной интеграции и развертывания (CI/CD) с помощью Службы контейнеров Azure с Docker Swarm, реестра контейнеров Azure, а также управления сборками и выпусками Visual Studio Team Services.

В этой статье используется простое приложение, доступное в [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) и разработанное с помощью ASP.NET Core. Это приложение состоит из четырех различных служб: трех веб-API и одного веб-интерфейса.

![Пример приложения MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Цель — непрерывная доставка приложения в кластере Docker Swarm с помощью Visual Studio Team Services. На следующем рисунке детально представлен этот конвейер непрерывной доставки.

![Пример приложения MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Вот краткое описание действий:

1. Изменения кода фиксируются в репозитории исходного кода (здесь GitHub). 
2. GitHub активирует сборку в Visual Studio Team Services. 
3. Visual Studio Team Services получает последнюю версию источников и создает все образы, из которых состоит приложение. 
4. Visual Studio Team Services помещает каждый образ в реестр Docker, созданный с помощью службы реестра контейнеров Azure. 
5. Visual Studio Team Services активирует новый выпуск. 
6. Выпуск выполняет некоторые команды с помощью SSH в главном узле кластера Службы контейнеров Azure. 
7. Docker Swarm в кластере извлекает последнюю версию образов. 
8. Развертывается новая версия приложения с помощью Docker Compose. 

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством вам потребуется выполнить следующие задачи:

- [Создайте кластер Swarm в службе контейнеров Azure.](container-service-deployment.md)
- [Подключитесь к кластеру Swarm в службе контейнеров Azure.](../container-service-connect.md)
- [Создать реестр контейнеров Azure](../../container-registry/container-registry-get-started-portal.md)
- [Создать учетную запись и командный проект Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Скопировать репозиторий GitHub в свою учетную запись GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Вам также понадобится компьютер Ubuntu (14.04 или 16.04) с установленным программным обеспечением Docker. Visual Studio Team Services использует этот компьютер в процессе сборки и выпуска. Этот компьютер можно создать, используя образ, доступный в [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Шаг 1. Настройка учетной записи Visual Studio Team Services 

В этом разделе настраивается учетная запись Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Настройка агента сборки Linux Visual Studio Team Services

Для создания образов Docker и их отправки в реестр контейнеров Azure из сборки Visual Studio Team Services необходимо зарегистрировать агент Linux. Доступны следующие варианты установки:

* [Развертывание агента на платформе Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Запуск агента VSTS с использованием Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Установка расширения Docker Integration VSTS

Корпорация Майкрософт предоставляет расширение VSTS для работы с Docker в процессе сборки и выпуска. Это расширение доступно в [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Нажмите кнопку **Установить**, чтобы добавить это расширение в свою учетную запись VSTS.

![Установка Docker Integration](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Вам будет предложено подключиться к своей учетной записи VSTS, используя свои учетные данные. 

### <a name="connect-visual-studio-team-services-and-github"></a>Подключение между Visual Studio Team Services и GitHub

Настройте подключение между проектом VSTS и учетной записью GitHub.

1. В проекте Visual Studio Team Services на панели инструментов щелкните значок **Параметры** и выберите **Службы**.

    ![Visual Studio Team Services — внешнее подключение](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. Слева выберите **Создать конечную точку службы** > **GitHub**.

    ![Visual Studio Team Services — GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Чтобы авторизовать VSTS для работы с вашей учетной записью GitHub, щелкните **Авторизовать** и следуйте указаниям в открывшемся окне.

    ![Visual Studio Team Services — авторизация GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Подключение VSTS к реестру контейнеров Azure и кластеру Службы контейнеров Azure

Последнее, что нужно сделать перед началом работы с конвейером CI/CD — это настроить внешние подключения к реестру контейнеров и кластеру Docker Swarm в Azure. 

1. В параметрах **Службы** проекта Visual Studio Team Services добавьте конечную точку службы типа **Docker Registry**. 

2. В открывшемся всплывающем окне введите URL-адрес и учетные данные реестра контейнеров Azure.

    ![Visual Studio Team Services — реестр Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Для кластера Docker Swarm добавьте конечную точку типа **SSH**. Затем введите сведения о подключении SSH кластера Swarm.

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Настройка завершена. На следующих шагах создается конвейер CI/CD, который создает и развертывает приложение в кластере Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Шаг 2: Создание определения сборки

На этом шаге настраивается определение сборки проекта VSTS и определяется рабочий процесс сборки для образов контейнера.

### <a name="initial-definition-setup"></a>Начальная настройка определения

1. Чтобы создать определение сборки, подключитесь к проекту Visual Studio Team Services и щелкните **Build & Release** (Сборка и выпуск). 

2. В разделе **Определения сборки** щелкните **+ Создать**. Выберите шаблон **Пустой**.

    ![Visual Studio Team Services — новое определение сборки](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Настройте новую сборку с помощью источника репозитория GitHub, установите флажок **Непрерывная интеграция** и выберите очередь агента, в которой зарегистрирован агент Linux. Нажмите кнопку **Создать**, чтобы создать определение сборки.

    ![Visual Studio Team Services — создание определения сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. На странице **Определения сборки** сначала откройте вкладку **Репозиторий** и настройте сборку для использования разветвления проекта MyShop, созданного на этапе предварительных требований. Выберите *acs-docs* в качестве значения для поля **Ветвь по умолчанию**.

    ![Visual Studio Team Services — конфигурация репозитория сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. На вкладке **Триггеры** настройте активацию сборки после выполнения каждой фиксации. Выберите **Непрерывная интеграция** и **Пакетные изменения**.

    ![Visual Studio Team Services — конфигурация триггера сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Определение рабочего процесса сборки
Дальнейшие действия определяют рабочий процесс сборки. Для сборки приложения *MyShop* доступно пять образов контейнера. Каждый образ создается с помощью Dockerfile, расположенного в папках проекта:

* ProductsApi;
* Прокси-сервер
* RatingsApi;
* RecommandationsApi;
* ShopFront.

Для каждого образа необходимо добавить два шага Docker: один для создания образа, а другой для передачи образа в реестр контейнеров Azure. 

1. Чтобы добавить шаг в рабочий процесс сборки, щелкните **Добавить шаг сборки...** и выберите **Docker**.

    ![Visual Studio Team Services — добавление шагов сборки](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Для каждого образа настройте один шаг, в котором используется команда `docker build`.

    ![Visual Studio Team Services — сборка Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Для операции сборки выберите реестр контейнеров Azure, действие **Build an image** (Создать образ) и Dockerfile, определяющий каждый образ. В поле **Build context** (Контекст сборки) введите имя корневого каталога Dockerfile и укажите **имя образа** в соответствующем поле. 
    
    Как показано на предыдущем снимке экрана, имя образа должно начинаться с универсального кода ресурса (URI) в реестре контейнеров Azure. (Чтобы параметризовать тег образа, также можно использовать переменную сборки, например идентификатор сборки в этом примере.)

3. Для каждого образа настройте второй шаг, на котором используется команда `docker push`.

    ![Visual Studio Team Services — принудительная отправка Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Чтобы выполнить операцию принудительной отправки, выберите реестр контейнеров Azure, действие **Push an image** (Отправить образ) и укажите **имя образа**, введенное на предыдущем шаге, в соответствующем поле.

4. Настроив действия сборки и принудительной отправки для каждого из пяти образов, добавьте еще два действия в рабочий процесс сборки.

    a. Задача командной строки, которая использует bash-скрипт для замены вхождения *BuildNumber* в файле docker-compose.yml текущим идентификатором сборки. Подробнее показано на следующем снимке экрана.

    ![Visual Studio Team Services — обновление файла Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    Б. Задача, которая удаляет обновленный файл Compose в качестве артефакта сборки, чтобы его можно было использовать в выпуске. Подробнее показано на следующем снимке экрана.

    ![Visual Studio Team Services — публикация файла Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Щелкните **Сохранить** и присвойте имя определению сборки.

## <a name="step-3-create-the-release-definition"></a>Шаг 3. Создание определения выпуска

Visual Studio Team Services позволяет [управлять выпусками в разных средах](https://www.visualstudio.com/team-services/release-management/). Вы можете включить непрерывное развертывание, чтобы ваше приложение беспрепятственно развертывалось в разных средах (например, в среде разработки, тестирования, подготовки и рабочей среде). Вы также можете создать среду, которая представляет кластер Docker Swarm Службы контейнеров Azure.

![Visual Studio Team Services — выпуск в ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Начальная настройка выпуска

1. Чтобы создать определение выпуска, щелкните **Выпуски** > **+ Выпуск**.

2. Чтобы настроить источник артефакта, выберите **Артефакты** > **Link an artifact source** (Связать источник артефакта). Свяжите это новое определение выпуска со сборкой, указанной на предыдущем шаге. После этого файл docker-compose.yml появится в процессе выпуска.

    ![Visual Studio Team Services — артефакты выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Чтобы настроить триггер выпуска, выберите **Триггеры** и **Непрерывное развертывание**. Установите триггер в том же источнике артефакта. Этот параметр обеспечивает запуск нового выпуска сразу после успешного завершения сборки.

    ![Visual Studio Team Services — триггеры выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Определение рабочего процесса выпуска

Рабочий процесс выпуска состоит из двух задач, которые необходимо добавить.

1. Настройте задачу для безопасного копирования файла Compose в папку *deploy* на главном узле Docker Swarm через подключение SSH, настроенное ранее. Подробнее показано на следующем снимке экрана.

    ![Visual Studio Team Services — SCP выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Настройте вторую задачу для выполнения команды bash, чтобы запустить команды `docker` и `docker-compose` на главном узле. Подробнее показано на следующем снимке экрана.

    ![Visual Studio Team Services — bash-команда выпуска](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    В команде, выполняемой на главном узле, используется интерфейс командной строки Docker и Docker-Compose для выполнения следующих задач:

    - вход в реестр контейнеров Azure (в нем используются три переменные сборки, указанные на вкладке **Переменные**);
    - определение переменной **DOCKER_HOST** для работы с конечной точкой Swarm (:2375);
    - переход к папке *deploy*, созданной при выполнении предыдущей задачи безопасного копирования, содержащей файл docker-compose.yml; 
    - выполнение команд `docker-compose`, которые извлекают новые образы, останавливают работу служб, удаляют службы и создают контейнеры.

    >[!IMPORTANT]
    > Как показано на предыдущем снимке экрана, не устанавливайте флажок **Ошибка в STDERR**. Это важный параметр, так как `docker-compose` выводит несколько диагностических сообщений, например об остановке контейнеров или их удалении, в стандартном выводе данных об ошибках. Если флажок установлен, Visual Studio Team Services сообщает об ошибках, произошедших во время выпуска, даже если все работает правильно.
    >
3. Сохраните это новое определение выпуска.


>[!NOTE]
>В этом развертывании допускается некоторое время простоя, так как мы останавливаем старые службы и запускаем новые. Этого можно избежать, выполнив "сине-зеленое" развертывание.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Шаг 4. Тестирование конвейера CI/CD

Теперь после выполнения настройки пришло время протестировать этот новый конвейер CI/CD. Самый простой способ сделать это — обновить исходный код и сохранить изменения в репозитории GitHub. Через несколько секунд после отправки кода в Visual Studio Team Services появится новая сборка. После успешного выполнения новый выпуск будет активирован и развернет новую версию приложения в кластере Службы контейнеров Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о CI/CD с Visual Studio Team Services см. в [обзоре сборки VSTS](https://www.visualstudio.com/docs/build/overview).

---
title: "Непрерывное создание и интеграция приложений Azure Service Fabric Linux на Java с помощью Jenkins | Документация Майкрософт"
description: "Непрерывное создание и интеграция приложения Linux на Java с помощью Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Создание и развертывание приложения Linux на Java с помощью Jenkins
Jenkins — это популярное средство для непрерывной интеграции и развертывания. В этом документе описывается создание и развертывание приложения Service Fabric с помощью Jenkins.

## <a name="general-prerequisites"></a>Общие предварительные требования
1. Необходимо иметь локальный репозиторий Git. Соответствующую вашей ОС версию Git можно установить [отсюда](https://git-scm.com/downloads). Если вы не знакомы с Git, ознакомьтесь со сведениями, приведенными в [этой документации](https://git-scm.com/docs).
2. Также необходимо иметь подключаемый модуль Jenkins для Service Fabric. Скачать подключаемый модуль можно [здесь](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Настройка Jenkins в кластере Service Fabric

### <a name="prerequisites"></a>Предварительные требования
1. Подготовьте кластер Service Fabric под управлением Linux. Кластер Service Fabric, созданный на портале Azure, уже содержит установленный Docker. При локальной работе с кластером проверьте, установлен ли Docker, с помощью команды ``docker info``, и если он не установлен, установите его, выполнив следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Разверните приложение-контейнер Service Fabric в кластере, выполнив следующие действия:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Контейнер Jenkins установится в кластер, и его можно будет отслеживать с помощью обозревателя Service Fabric.

### <a name="steps"></a>Действия
1. Перейдите по URL-адресу ``http://PublicIPorFQDN:8081`` в браузере. Это предоставит путь к начальному паролю администратора, необходимому для входа в систему. Вы можете продолжить использовать Jenkins в качестве пользователя с правами администратора или создать и изменить пользователя после входа под начальной учетной записью администратора.

  > [!NOTE]
  > При создании кластера необходимо убедиться, что в качестве порта конечной точки приложения задан порт 8081.
  >

2. Получите идентификатор экземпляра контейнера с помощью ``docker ps -a``.
3. Войдите в контейнер с помощью протокола SSH и вставьте путь, предоставленный на портале Jenkins. Например, если на портале был указан путь `PATH_TO_INITIAL_ADMIN_PASSWORD`, можно выполнить следующую команду:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Настройте GitHub для работы с Jenkins, выполнив шаги, описанные в этой [статье](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Используйте инструкции, предоставленные в GitHub, чтобы создать ключ SSH и добавить его в учетную запись GitHub, в которой находится репозиторий.
    * В оболочке Jenkins или Docker (а не на узле) выполните команды, перечисленные в приведенной выше статье.
    * Чтобы войти в оболочку Jenkins с вашего узла, выполните следующую команду:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Настройка Jenkins за пределами кластера Service Fabric

### <a name="prerequisites"></a>Предварительные требования
Необходимо иметь установленный Docker. Чтобы установить Docker из терминала, выполните следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Теперь, выполнив ``docker info`` в терминале, в выходных данных вы увидите, что служба Docker запущена.

### <a name="steps"></a>Действия
  1. Получите образ контейнера Jenkins для Service Fabric: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``.
  2. Запустите образ контейнера: ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Получите идентификатор экземпляра образа контейнера. Вы можете вывести список всех контейнеров Docker с помощью команды ``docker ps –a``.
  4. Войдите на портал Jenkins, сделав следующее:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Если идентификатор контейнера имеет значение 2d24a73b5964, используйте 2d24.
    * Этот пароль необходим для входа на панель мониторинга Jenkins с портала ``http://<HOST-IP>:8080``.
    * После первого входа можно создать собственную учетную запись и использовать ее в будущем или же продолжать использовать учетную запись администратора. Если вы создадите пользователя, вам потребуется использовать его для последующих действий.
  5. Настройте GitHub для работы с Jenkins, выполнив шаги, описанные в этой [статье](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Используйте инструкции, предоставленные в GitHub, чтобы создать ключ SSH и добавить его в учетную запись GitHub, в которой находится репозиторий.
      * В оболочке Jenkins или Docker (а не на узле) выполните команды, перечисленные в приведенной выше статье.
      * Чтобы войти в оболочку Jenkins с вашего узла, выполните следующие команды:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Убедитесь, что кластер или компьютер, где размещен образ контейнера Jenkins, имеет общедоступный IP-адрес, чтобы экземпляр Jenkins получал уведомления от GitHub.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Установка подключаемого модуля Jenkins для Service Fabric с помощью портала

1. Перейдите на сайт ``http://PublicIPorFQDN:8081``.
2. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins) -> **Manage Plugins** (Управление подключаемыми модулями) -> **Advanced** (Дополнительно).
Здесь можно загрузить подключаемый модуль. Выберите параметр **Choose file** (Выбрать файл), а затем выберите файл serviceFabric.hpi, который был скачан в разделе предварительных требований. После того как вы щелкните Upload (Отправить), Jenkins автоматически установит подключаемый модуль. При необходимости выполните перезагрузку.

## <a name="creating-and-configuring-a-jenkins-job"></a>Создание и настройка задания Jenkins

1. Создайте **новый элемент** из панели мониторинга.
2. Введите имя элемента, например **MyJob**, выберите проект в свободной форме и нажмите кнопку "ОК".
3. Затем перейдите на страницу задания и щелкните **Настройка** -
  * В разделе общих сведений в **проекте Github``https://github.com/sayantancs/SFJenkins`` укажите URL-адрес проекта GitHub, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, **).
  * В разделе **Source Code Management** (Управление исходным кодом) выберите **Git**. Укажите URL-адрес репозитория, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, ``https://github.com/sayantancs/SFJenkins.git``). Также здесь можно указать, какую ветвь необходимо создать, например **/master**.
4. Настройте свой *Github* (в котором размещается репозиторий), чтобы он мог обмениваться данными с Jenkins, сделав следующее:
  1. Перейдите на страницу репозитория GitHub. Последовательно выберите пункты **Settings** (Параметры) -> **Integrations and Services** (Интеграции и службы).
  2. Выберите **Add Service** (Добавить службу), введите Jenkins и выберите **Jenkins-Github plugin** (Модуль Jenkins-Github).
  3. Введите URL-адрес webhook для Jenkins (по умолчанию это должен быть ``http://<PublicIPorFQDN>:8081/github-webhook/``). Добавьте или обновите службу.
  4. В ваш экземпляр Jenkins будет отправлено событие проверки. Рядом с webhook в GitHub должен появиться зеленый флажок. Это означает, что проект успешно создан.
  5. В разделе **Build Triggers** (Создание триггеров) выберите требуемый параметр сборки. В этом примере мы планируем инициировать создание сборки каждый раз, когда будет происходить отправка в репозиторий, поэтому соответствующим параметром будет **GitHub hook trigger for GITScm polling** (Обработчик триггера GitHub для опроса GITScm) (ранее было Build when a change is pushed to GitHub (Выполнять сборку при отправке изменений в GitHub)).
  6. В раскрывающем списке **Add build step** (Добавить шаг сборки) раздела **Build** (Создание) выберите параметр **Invoke Gradle Script** (Вызов скрипта Gradle). В поставляемом мини-приложении укажите путь к **корневому скрипту сборки** вашего приложения. Этот скрипт выбирает build.gradle по указанному пути и действует соответствующим образом. При создании проекта с именем ``MyActor`` (с помощью подключаемого модуля Eclipse или генератора Yeoman) корневой скрипт сборки должен содержать ``${WORKSPACE}/MyActor``. Этот раздел почти всегда выглядит так:

    ![Действие при сборке Jenkins для Service Fabric][build-step]
  7. В раскрывающемся списке **Post-Build Actions** (Действия после сборки) выберите **Deploy Service Fabric Project** (Развернуть проект Service Fabric). Здесь необходимо предоставить сведения о кластере, где будет развернуто приложение Service Fabric, скомпилированное с помощью Jenkins, а также дополнительные сведения о приложении, необходимые для развертывания. В качестве примера можно использовать следующий снимок экрана:

    ![Действие при сборке Jenkins для Service Fabric][post-build-step]

 > [!NOTE]
 > Если образ контейнера Jenkins развертывается с помощью Service Fabric, можно использовать тот же кластер, на котором размещается приложение-контейнер Jenkins.
 >

### <a name="end-to-end-scenario"></a>Дополнительные задачи
Теперь GitHub и Jenkins настроены, и можно внести несколько изменений в ваш проект ``MyActor`` в репозитории, например https://github.com/sayantancs/SFJenkins, и передать изменения в удаленную ветвь ``master`` (или ветвь, настроенную для работы). Это запустит задание Jenkins ``MyJob``, которое вы настроили. По сути, оно получит изменения из GitHub, выполнит сборку и развертывание приложения в конечную точку кластера, выбранную в раскрывающемся списке Post-build Actions (Действия после сборки).  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png


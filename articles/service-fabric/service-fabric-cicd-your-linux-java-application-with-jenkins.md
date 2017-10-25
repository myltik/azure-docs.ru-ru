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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.openlocfilehash: 8ba108ed107e2e023867bcc3b3b1b8cc159377ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Создание и развертывание приложения Linux на Java с помощью Jenkins
Jenkins — это популярное средство для непрерывной интеграции и развертывания приложений. В этой статье описывается создание и развертывание приложения Azure Service Fabric с помощью Jenkins.

## <a name="general-prerequisites"></a>Общие предварительные требования
- Необходимо иметь локальный репозиторий Git. Соответствующую вашей операционной системе версию Git можно установить, скачав ее со [страницы загрузок Git](https://git-scm.com/downloads). Если вы не знакомы с Git, ознакомьтесь со сведениями, приведенными в [этой документации](https://git-scm.com/docs).
- Также необходимо иметь подключаемый модуль Jenkins для Service Fabric. Его можно скачать [здесь](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Настройка Jenkins в кластере Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как его настроить внутри кластера при использовании учетной записи хранения Azure для сохранения состояния экземпляра контейнера.

### <a name="prerequisites"></a>Предварительные требования
1. Подготовьте кластер Service Fabric под управлением Linux. Кластер Service Fabric, созданный на портале Azure, уже содержит установленный Docker. При локальной работе с кластером проверьте, установлен ли Docker, с помощью команды ``docker info``. Если он не установлен, установите его, выполнив следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Разверните приложение-контейнер Service Fabric в кластере, сделав следующее:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
cd service-fabric-java-getting-started/Services/JenkinsDocker/
```

3. Необходимо иметь сведения о параметрах подключения общей папки службы хранилища Azure, в которой необходимо сохранить состояние экземпляра контейнера Jenkins. Если вы используете для этого портал Microsoft Azure, выполните следующие действия. Создайте учетную запись хранения, например ``sfjenkinsstorage1``. Затем создайте **общую папку** для этой учетной записи хранения, например ``sfjenkins``. Щелкните **Подключить** для общей папки. Обратите внимание на значения, которые отображаются в разделе **Connecting from Linux** (Подключение из Linux). Они должны выглядеть следующим образом:
```sh
sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
```

> [!NOTE]
> Для подключения файловых ресурсов cifs требуется установить пакет cifs-utils на узлах кластера. 
>

4. Замените значения заполнителей в скрипте ```setupentrypoint.sh``` соответствующими сведениями службы хранилища Azure.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
Замените ``[REMOTE_FILE_SHARE_LOCATION]`` значением ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` из выходных данных подключения из пункта 3, описанного выше.
Замените ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` значением ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` из пункта 3, описанного выше.

5. Подключитесь к кластеру и установите приложение-контейнер.
```azurecli
sfctl cluster select --endpoint http://PublicIPorFQDN:19080   # cluster connect command
bash Scripts/install.sh
```
Контейнер Jenkins установится в кластер, и его можно будет отслеживать с помощью Service Fabric Explorer.

### <a name="steps"></a>Действия
1. Откройте в браузере ``http://PublicIPorFQDN:8081``. Это предоставит путь к начальному паролю администратора, необходимому для входа в систему. Вы можете продолжить использовать Jenkins в качестве пользователя с правами администратора или создать и изменить пользователя после входа под начальной учетной записью администратора.

   > [!NOTE]
   > При создании приложения необходимо убедиться, что в качестве порта конечной точки приложения задан порт 8081 (и он открыт в кластере).
   >

2. Получите идентификатор экземпляра контейнера с помощью ``docker ps -a``.
3. Войдите в контейнер по протоколу SSH и вставьте путь, предоставленный на портале Jenkins. Например, если на портале был указан путь `PATH_TO_INITIAL_ADMIN_PASSWORD`, выполните следующую команду:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Настройте GitHub для работы с Jenkins, выполнив шаги, описанные в статье [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Создание ключа SSH и добавление его в агент SSH).
    * Используйте инструкции, предоставленные в GitHub, чтобы создать ключ SSH и добавить его в учетную запись GitHub, в которой находится репозиторий.
    * В оболочке Jenkins или Docker (а не на узле) выполните команды, перечисленные в приведенной выше статье.
    * Чтобы войти в оболочку Jenkins с вашего узла, выполните следующую команду:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Настройка Jenkins за пределами кластера Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как настроить Jenkins за пределами кластера.

### <a name="prerequisites"></a>Предварительные требования
Необходимо иметь установленный Docker. Чтобы установить Docker из терминала, выполните следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Теперь, выполнив ``docker info`` в терминале, в выходных данных вы увидите, что служба Docker запущена.

### <a name="steps"></a>Действия
  1. Получите образ контейнера Jenkins для Service Fabric: ``docker pull raunakpandya/jenkins:v1``.
  2. Запустите образ контейнера: ``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Получите идентификатор экземпляра образа контейнера. Вы можете вывести список всех контейнеров Docker с помощью команды ``docker ps –a``.
  4. Войдите на портал Jenkins, сделав следующее:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Если идентификатор контейнера имеет значение 2d24a73b5964, используйте 2d24.
    * Этот пароль необходим для входа на панель мониторинга Jenkins с портала ``http://<HOST-IP>:8080``.
    * После первого входа можно создать собственную учетную запись и использовать ее в будущем или же продолжать использовать учетную запись администратора. Если вы создадите пользователя, вам потребуется использовать его для последующих действий.
  5. Настройте GitHub для работы с Jenkins, выполнив шаги, описанные в статье [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Создание ключа SSH и добавление его в агент SSH).
        * Используйте инструкции, предоставленные в GitHub, чтобы создать ключ SSH и добавить его в учетную запись GitHub, в которой находится репозиторий.
        * В оболочке Jenkins или Docker (а не на узле) выполните команды, перечисленные в приведенной выше статье.
      * Чтобы войти в оболочку Jenkins с вашего узла, выполните следующие команды:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Убедитесь, что кластер или компьютер, где размещен образ контейнера Jenkins, имеет общедоступный IP-адрес, чтобы экземпляр Jenkins получал уведомления от GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Установка подключаемого модуля Jenkins для Service Fabric с помощью портала

1. Перейдите на сайт ``http://PublicIPorFQDN:8081``.
2. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins) > **Manage Plugins** (Управление подключаемыми модулями) > **Advanced** (Дополнительно).
Здесь можно загрузить подключаемый модуль. Выберите **Choose file** (Выбрать файл), а затем выберите файл **serviceFabric.hpi**, который был скачан в разделе предварительных требований. После того как вы щелкните **Upload** (Отправить), Jenkins автоматически установит подключаемый модуль. При необходимости выполните перезагрузку.

## <a name="create-and-configure-a-jenkins-job"></a>Создание и настройка задания Jenkins

1. Создайте **новый элемент** из панели мониторинга.
2. Введите имя элемента, например **MyJob**. Выберите **проект в свободной форме** и нажмите кнопку **ОК**.
3. Перейдите на страницу задания и щелкните **Configure** (Настройка).

   а. В разделе общих сведений **проекта GitHub** укажите URL-адрес проекта GitHub. Это URL-адрес, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, ``https://github.com/sayantancs/SFJenkins``).

   b. В разделе **Source Code Management** (Управление исходным кодом) выберите **Git**. Укажите URL-адрес репозитория, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, ``https://github.com/sayantancs/SFJenkins.git``). Также здесь можно указать, какую ветвь необходимо создать (например, **/master**).
4. Настройте свой *Github* (где размещен репозиторий), чтобы он мог обмениваться данными с Jenkins. Выполните следующие действия.

   а. Перейдите на страницу репозитория GitHub. Последовательно выберите пункты **Settings** (Параметры) > **Integrations and Services** (Интеграции и службы).

   b. Выберите **Add Service** (Добавить службу), введите **Jenkins** и выберите **Jenkins-Github plugin** (Модуль Jenkins-Github).

   c. Введите URL-адрес webhook для Jenkins (по умолчанию это должен быть ``http://<PublicIPorFQDN>:8081/github-webhook/``). **Добавьте или обновите службу**.

   d. В ваш экземпляр Jenkins будет отправлено событие проверки. Рядом с webhook в GitHub должен появиться зеленый флажок. Это означает, что проект успешно создан.

   д. В разделе **Build Triggers** (Создание триггеров) выберите требуемый параметр сборки. В этом примере мы планируем инициировать создание сборки каждый раз, когда будет происходить отправка в репозиторий, поэтому выберите параметр **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm). (Ранее был указан параметр **Build when a change is pushed to GitHub** (Выполнять сборку при отправке изменений в GitHub).)

   f. В раскрывающемся списке **Add build step** (Добавить шаг сборки) раздела **Build** (Создание) выберите параметр **Invoke Gradle Script** (Вызов скрипта Gradle). В поставляемом мини-приложении укажите путь к **корневому скрипту сборки** вашего приложения. Этот скрипт выбирает build.gradle по указанному пути и действует соответствующим образом. При создании проекта с именем ``MyActor`` (с помощью подключаемого модуля Eclipse или генератора Yeoman) корневой скрипт сборки должен содержать ``${WORKSPACE}/MyActor``. Ниже приведен снимок экрана для примера.

    ![Действие при сборке Jenkins для Service Fabric][build-step]

   g. В раскрывающемся списке **Post-Build Actions** (Действия после сборки) выберите **Deploy Service Fabric Project** (Развернуть проект Service Fabric). Здесь необходимо предоставить сведения о кластере, где будет развернуто приложение Service Fabric, скомпилированное с помощью Jenkins, а также дополнительные сведения о приложении, необходимые для развертывания. Ниже приведен снимок экрана для примера.

    ![Действие при сборке Jenkins для Service Fabric][post-build-step]

   > [!NOTE]
   > Если образ контейнера Jenkins развертывается с помощью Service Fabric, можно использовать тот же кластер, на котором размещается приложение-контейнер Jenkins.
   >

## <a name="next-steps"></a>Дальнейшие действия
Теперь GitHub и Jenkins настроены, и вы можете внести некоторые изменения в ваш проект ``MyActor`` в репозитории, например https://github.com/sayantancs/SFJenkins. Передайте изменения в удаленную ветвь ``master`` (или ветвь, настроенную для работы). Это запустит задание Jenkins ``MyJob``, которое вы настроили. Оно получит изменения из GitHub, выполнит сборку и развертывание приложения в конечную точку кластера, выбранную в раскрывающемся списке Post-build Actions (Действия после сборки).  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

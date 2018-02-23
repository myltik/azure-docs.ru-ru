---
title: "Непрерывное выполнение сборки и интеграции приложений Azure Service Fabric на платформе Linux с помощью Jenkins | Документация Майкрософт"
description: "Непрерывное создание и интеграция приложения Service Fabric для Linux с помощью Jenkins"
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
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: 80c52cfeab007030203b6af4bb220f1a847e9426
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Создание и развертывание приложений Linux с помощью Jenkins
Jenkins — это популярное средство для непрерывной интеграции и развертывания приложений. В этой статье описывается создание и развертывание приложения Azure Service Fabric с помощью Jenkins.

## <a name="general-prerequisites"></a>Общие предварительные требования
- Необходимо иметь локальный репозиторий Git. Соответствующую вашей операционной системе версию Git можно установить, скачав ее со [страницы загрузок Git](https://git-scm.com/downloads). Если вы не знакомы с Git, ознакомьтесь со сведениями, приведенными в [этой документации](https://git-scm.com/docs).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Настройка Jenkins в кластере Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как его настроить внутри кластера при использовании учетной записи хранения Azure для сохранения состояния экземпляра контейнера.

### <a name="prerequisites"></a>предварительным требованиям
1. Подготовьте кластер Service Fabric под управлением Linux. Кластер Service Fabric, созданный на портале Azure, уже содержит установленный Docker. При локальной работе с кластером проверьте, установлен ли Docker, с помощью команды ``docker info``. Если он не установлен, установите его, выполнив следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Убедитесь, что в качестве пользовательской конечной точки в кластере задан порт 8081.
   >

2. Клонируйте приложение, выполнив следующие действия:
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. Сохраните состояние контейнера Jenkins в общей папке:
  * Создайте учетную запись хранения Azure, например с именем ``sfjenkinsstorage1``, в **том же регионе**, что и кластер.
  * Затем создайте **общую папку** для этой учетной записи хранения и присвойте ей имя, например ``sfjenkins``.
  * Щелкните **Подключить** для общей папки. Запишите значения, которые отображаются в разделе **Подключение из Linux**. Они должны выглядеть приблизительно так:
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Для подключения файловых ресурсов cifs требуется установить пакет cifs-utils на узлах кластера.       
  >

4. Замените значения заполнителей в скрипте ```setupentrypoint.sh``` сведениями из учетной записи хранения Azure, приведенными на шаге 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Замените ``[REMOTE_FILE_SHARE_LOCATION]`` значением ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` из выходных данных подключения, полученных выше на шаге 3.
  * Замените ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` значением ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777``, полученным ранее на шаге 3.

5. **Только безопасный кластер**. Для настройки развертывания приложений в безопасном кластере из службы Jenkins сертификат должен быть доступен в контейнере Jenkins. В кластерах Linux сертификаты (PEM-файлы) просто копируются из хранилища, заданного параметром X509StoreName, в контейнер. В ApplicationManifest в разделе ContainerHostPolicies добавьте эту ссылку на сертификат и измените значение отпечатка. Значение отпечатка должно соответствовать сертификату на узле.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > Значение отпечатка должно совпадать с сертификатом, используемым для подключения к безопасному кластеру. 
  >

6. Подключитесь к кластеру и установите приложение-контейнер.

  **Безопасный кластер**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **Небезопасный кластер**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  Контейнер Jenkins установится в кластер, и его можно будет отслеживать с помощью Service Fabric Explorer.

    > [!NOTE]
    > Скачивание образа Jenkins в кластер может занять несколько минут.
    >

### <a name="steps"></a>Действия
1. Откройте в браузере ``http://PublicIPorFQDN:8081``. Это предоставит путь к начальному паролю администратора, необходимому для входа в систему. 
2. Откройте Service Fabric Explorer, чтобы узнать, на каком узле выполняется контейнер Jenkins. Войдите на узел по протоколу SSH.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Получите идентификатор экземпляра контейнера с помощью ``docker ps -a``.
4. Войдите в контейнер по протоколу SSH и вставьте путь, предоставленный на портале Jenkins. Например, если на портале был указан путь `PATH_TO_INITIAL_ADMIN_PASSWORD`, выполните следующую команду:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. На главной странице Jenkins щелкните вариант Select plugins to install (Выбор подключаемых модулей для установки), установите флажок **None** (Ни один) и нажмите кнопку Install (Установить).
6. Создайте учетную запись пользователя или продолжите от имени администратора.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Настройка Jenkins за пределами кластера Service Fabric

Jenkins можно настроить внутри или за пределами кластера Service Fabric. В следующих разделах показано, как настроить Jenkins за пределами кластера.

### <a name="prerequisites"></a>предварительным требованиям
Необходимо иметь установленный Docker. Чтобы установить Docker из терминала, выполните следующие команды:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Теперь, выполнив ``docker info`` в терминале, в выходных данных вы увидите, что служба Docker запущена.

### <a name="steps"></a>Действия
  1. Получите образ контейнера Jenkins для Service Fabric: ``docker pull rapatchi/jenkins:v10``. Этот образ предварительно установлен в подключаемом модуле Jenkins Service Fabric.
  2. Запустите образ контейнера: ``docker run -itd -p 8080:8080 rapatchi/jenkins:v10``
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


## <a name="create-and-configure-a-jenkins-job"></a>Создание и настройка задания Jenkins

1. Создайте **новый элемент** из панели мониторинга.
2. Введите имя элемента, например **MyJob**. Выберите **проект в свободной форме** и нажмите кнопку **ОК**.
3. Перейдите на страницу задания и щелкните **Configure** (Настройка).

   a. В разделе общих сведений установите флажок напротив **проекта GitHub** и укажите URL-адрес этого проекта. Это URL-адрес, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, ``https://github.com/sayantancs/SFJenkins``).

   Б. В разделе **Source Code Management** (Управление исходным кодом) выберите **Git**. Укажите URL-адрес репозитория, где размещено приложение Service Fabric на Java, в которое необходимо интегрировать последовательность средств непрерывной интеграции и доставки Jenkins (например, ``https://github.com/sayantancs/SFJenkins.git``). Также здесь можно указать, какую ветвь необходимо создать (например, **/master**).
4. Настройте свой *Github* (где размещен репозиторий), чтобы он мог обмениваться данными с Jenkins. Выполните следующие действия.

   a. Перейдите на страницу репозитория GitHub. Последовательно выберите пункты **Settings** (Параметры) > **Integrations and Services** (Интеграции и службы).

   Б. Выберите **Add Service** (Добавить службу), введите **Jenkins** и выберите **Jenkins-Github plugin** (Модуль Jenkins-Github).

   c. Введите URL-адрес webhook для Jenkins (по умолчанию это должен быть ``http://<PublicIPorFQDN>:8081/github-webhook/``). **Добавьте или обновите службу**.

   d. В ваш экземпляр Jenkins будет отправлено событие проверки. Рядом с webhook в GitHub должен появиться зеленый флажок. Это означает, что проект успешно создан.

   д. В разделе **Build Triggers** (Создание триггеров) выберите требуемый параметр сборки. В этом примере мы планируем инициировать создание сборки каждый раз, когда будет происходить отправка в репозиторий, поэтому выберите параметр **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm). (Ранее был указан параметр **Build when a change is pushed to GitHub** (Выполнять сборку при отправке изменений в GitHub).)

   f. **Раздел "Build" (Сборка) для приложений Java**. В раскрывающемся списке **Add build step** (Добавить шаг сборки) раздела **Build** (Сборка) выберите параметр **Invoke Gradle Script** (Вызвать сценарий Gradle). В появившемся мини-приложении откройте расширенное меню и укажите путь к **корневому скрипту сборки** вашего приложения. Этот скрипт выбирает build.gradle по указанному пути и действует соответствующим образом. При создании проекта с именем ``MyActor`` (с помощью подключаемого модуля Eclipse или генератора Yeoman) корневой скрипт сборки должен содержать ``${WORKSPACE}/MyActor``. Ниже приведен снимок экрана для примера.

    ![Действие при сборке Jenkins для Service Fabric][build-step]

   ж. **Раздел "Build" (Сборка) для приложений .Net Core**. В раскрывающемся списке **Add build step** (Добавить шаг сборки) раздела **Build** (Сборка) выберите параметр **Execute Shell** (Выполнить оболочку). В отобразившемся окне команд сначала необходимо перейти в каталог, в котором находится файл build.sh. После этого можно будет запустить этот сценарий и выполнить сборку приложения.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    Ниже приведен пример команд, которые используются для сборки примера [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) с заданием Jenkins CounterServiceApplication.

    ![Действие при сборке Jenkins для Service Fabric][build-step-dotnet]
  
   h. В раскрывающемся списке **Post-Build Actions** (Действия после сборки) выберите **Deploy Service Fabric Project** (Развернуть проект Service Fabric). Здесь необходимо предоставить сведения о кластере, где будет развернуто приложение Service Fabric, скомпилированное с помощью Jenkins, Путь к сертификату можно найти, выводя значение переменной среды Certificates_JenkinsOnSF_Code_MyCert_PEM из контейнера. Этот путь можно использовать для полей "Ключ клиента" и "Сертификат клиента".

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    а также дополнительные сведения о приложении, необходимые для развертывания. Ниже приведен снимок экрана для примера.

    ![Действие при сборке Jenkins для Service Fabric][post-build-step]

      > [!NOTE]
      > Если образ контейнера Jenkins развертывается с помощью Service Fabric, можно использовать тот же кластер, на котором размещается приложение-контейнер Jenkins.
      >

## <a name="next-steps"></a>Дополнительная информация
Теперь GitHub и Jenkins настроены, и вы можете внести некоторые изменения в ваш проект ``MyActor`` в репозитории, например https://github.com/sayantancs/SFJenkins. Передайте изменения в удаленную ветвь ``master`` (или ветвь, настроенную для работы). Это запустит задание Jenkins ``MyJob``, которое вы настроили. Оно получит изменения из GitHub, выполнит сборку и развертывание приложения в конечную точку кластера, выбранную в раскрывающемся списке Post-build Actions (Действия после сборки).  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png


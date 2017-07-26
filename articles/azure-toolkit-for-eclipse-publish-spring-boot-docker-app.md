---
title: "Публикация приложения Spring Boot в виде контейнера Docker с помощью набора средств Azure для Eclipse | Документы Майкрософт"
description: "Вы можете узнать, как опубликовать веб-приложение в Microsoft Azure в виде контейнера Docker с помощью набора средств Azure для Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Публикация приложения Spring Boot в виде контейнера Docker с помощью набора средств Azure для Eclipse

**[Spring Framework]** — это решение с открытым исходным кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Spring Boot]. Он упрощает подход к созданию автономных приложений Java.

**[Docker]** — это решение с открытым исходным кодом, которое помогает разработчикам автоматизировать развертывание приложений, их масштабирование, а также управление приложениями, которые выполняются в контейнерах.

В данном руководстве описывается процесс развертывания приложения Spring Boot в качестве контейнера Docker в Microsoft Azure с помощью набора средств Azure для Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>Клонирование репозитория по умолчанию приложения Docker Spring Boot

### <a name="importing-the-public-repository"></a>Импорт общедоступного репозитория

Ниже рассмотрена процедура клонирования репозитория приложения Docker Spring Boot на локальный компьютер с помощью IntelliJ. Процесс использования командной строки см. в разделе [Развертывание приложения Spring Boot в Linux в службе контейнеров Azure][Deploy Spring Boot on Linux in ACS].

1. Откройте Eclipse.

1. В меню **File** (Файл) выберите **Import** (Импортировать).

   ![Меню "File" (Файл) > "Import" (Импортировать)][CL01]

1. Когда откроется диалоговое окно **Import** (Импорт), сделайте следующее.

   а. Разверните элемент **Git**.

   b. Выберите **Projects from Git** (Проекты из Git).
   
   c. Щелкните **Далее**.

   ![Диалоговое окно "Import" (Импорт)][CL02]

1. На странице **Select Repository Source** (Выбор источника репозитория) сделайте следующее.

   а. Выберите **Clone URI** (URI клона).
   
   b. Щелкните **Далее**.

   ![Выбор источника репозитория][CL03]

1. На странице **Source Git Repository** (Исходный репозиторий Git) сделайте следующее.

   а. Введите `https://github.com/spring-guides/gs-spring-boot-docker.git` в поле **URI** (Универсальный код ресурса (URI)) репозитория. Поля **Host** (Узел) и **Repository path** (Путь к репозиторию) должны автоматически заполниться правильными значениями.
   
   b. Репозиторий Spring Boot является общедоступным, поэтому нет необходимости вводить имя пользователя и пароль Git.
   
   c. Щелкните **Далее**.

   ![Исходный репозиторий Git][CL04]

1. На странице **Branch** (Ветвь) нажмите кнопку **Next** (Далее).

   ![Ветвь][CL05]

1. На странице **Local Destination** (Локальное назначение) сделайте следующее.

   а. Укажите локальную папку, в которой должен находиться локальный репозиторий.
   
   b. Щелкните **Далее**.

   ![Локальное назначение][CL06]

1. На странице **Select a wizard to use for importing projects** (Выбор мастера для импорта проектов) сделайте следующее.

   а. Выберите **Import as a general project** (Импортировать как универсальный проект).
   
   b. Щелкните **Далее**.

   ![Локальное назначение][CL07]

1. На странице **Import Projects** (Импорт проектов) сделайте следующее.

   а. В поле **Project name** (Имя проекта) укажите имя проекта.
   
   b. Нажмите кнопку **Готово**

   ![Импорт проектов][CL08]

1. Если репозиторий успешно клонирован, вы увидите полный список файлов в Eclipse.

   ![Локальный репозиторий][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>Создание проекта Maven из локального репозитория

Репозиторий Docker Spring Boot содержит завершенный проект Maven, который будет использоваться в этом руководстве. 

1. В меню **File** (Файл) выберите **Import** (Импортировать).

   ![Меню "File" (Файл) > "Import" (Импортировать)][CL01]

1. Когда откроется диалоговое окно **Import** (Импорт), сделайте следующее.

   а. Разверните элемент **Maven**.
   
   b. Выберите **Existing Maven Projects** (Существующие проекты Maven).
   
   c. Щелкните **Далее**.

   ![Диалоговое окно "Import" (Импорт)][MV01]

1. На странице **Maven Projects** (Проекты Maven) сделайте следующее.

   а. Укажите папку `complete`, расположенную в вашем локальном репозитории, в поле **Root Directory** (Корневой каталог).
   
   b. Разверните раздел **Advanced** (Дополнительно) и введите пользовательское имя в поле **Name template** (Шаблон имени).
   
   c. Установите флажок для файла `pom.xml` в проекте.
   
   г) Нажмите кнопку **Готово**

   ![Проекты Maven][MV02]

1. После успешного открытия проекта Maven в Eclipse появится второй проект.

   ![Локальный проект Maven][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>Создание приложения Spring Boot с помощью Maven

1. В обозревателе проектов Eclipse выберите проект Maven.

1. Щелкните **Run** (Выполнить), затем выберите **Run As** (Выполнить как) и щелкните **Maven build** (Сборка Maven).

   ![Выполнение в качестве сборки Maven][BU01]

1. После успешной сборки приложения в окне консоли отобразится состояние.

   ![Успешное выполнение сборки Maven][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Публикация веб-приложения в Azure с помощью контейнера Docker

1. В обозревателе проектов Eclipse выберите проект Maven.

1. Щелкните меню Azure **Publish** (Публикация) и выберите **Publish as Docker container** (Опубликовать как контейнер Docker).

   ![Публикация в виде контейнера Docker][PU01]

1. Когда отобразится диалоговое окно **Deploy Docker Container on Azure** (Развертывание контейнера Docker в Azure), сделайте следующее.

   а. Введите пользовательское имя образа Docker в поле **Docker image name** (Имя образа Docker).
   
   b. В поле **Artifact to deploy** (Развертываемый артефакт) укажите путь к файлу `gs-spring-boot-docker-0.1.0.jar`, сборку которого вы только что выполнили.

   ![Задание параметров Docker][PU02]

   Отобразятся все существующие узлы Docker. Если требуется выполнить развертывание на существующий узел, можно сразу перейти к шагу 4. В противном случае для создания нового узла необходимо использовать следующую процедуру.

   а. Щелкните **Добавить**.

      ![Добавление нового узла Docker][PU03]

   b. При появлении диалогового окна **Создание узла Docker** можно принять значения по умолчанию или задать пользовательские параметры для нового узла Docker. (Подробное описание различных параметров см. в статье [Публикации веб-приложения в виде контейнера Docker с помощью набора средств Azure для IntelliJ][Publish Container with Azure Toolkit].) Нажмите кнопку **Next** (Далее) после задания используемых параметров.

      ![Задание параметров узла Docker][PU04]

   c. Можно использовать существующие учетные данные для входа в систему, хранящиеся в Azure Key Vault, или ввести новые учетные данные для входа в Docker. После задания параметров нажмите кнопку **Finish** (Готово).

      ![Задание учетных данных узла Docker][PU05]

1. Выделите узел Docker и нажмите кнопку **Next** (Далее).

   ![Выбор используемого узла Docker][PU06]

1. На последней странице диалогового окна **Deploy Docker Container on Azure** (Развертывание контейнера Docker в Azure) необходимо задать следующие параметры.

   а. Можно указать пользовательское имя для контейнера, в котором будет размещаться контейнер Docker, или принять значение по умолчанию.

   b. Для узла Docker TCP-порты необходимо указать с помощью следующего синтаксиса: "*[внешний порт]*:*[внутренний порт]*. Например, "80:8080" означает внешний порт "80" и внутренний порт Spring Boot по умолчанию "8080".
   
      После настройки внутреннего порта (например, с помощью файла *application.yml*) необходимо указать номер порта для обеспечения корректной маршрутизации в Azure.

   c. После настройки этих параметров щелкните **Finish** (Готово).

   ![Развертывание контейнера Docker в Azure][PU07]

1. После завершения публикации набора средств Azure в журнале действий Azure будет отображаться состояние **Опубликовано**.

   ![Успешно развернутый узел Docker][PU08]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png


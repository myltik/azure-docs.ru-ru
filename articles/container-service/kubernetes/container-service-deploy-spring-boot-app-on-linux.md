---
title: "Развертывание веб-приложения Spring Boot в Linux в службе контейнеров Azure | Документы Майкрософт"
description: "В этом руководстве содержатся пошаговые инструкции по развертыванию приложения Spring Boot в качестве веб-приложения Linux в Microsoft Azure."
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
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 0fc873c83c7b518bf393964ec579ba205f8683c2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>Развертывание приложения Spring Boot в Linux в службе контейнеров Azure

**[Spring Framework]** — это решение с открытым исходным кодом, которое помогает разработчикам Java создавать приложения корпоративного класса. Одним из самых популярных проектов, созданных на этой платформе, является проект [Загрузки Spring]. Он упрощает подход к созданию автономных приложений Java.

**[Docker]** — это решение с открытым исходным кодом, которое помогает разработчикам автоматизировать развертывание приложений, их масштабирование, а также управление приложениями, которые выполняются в контейнерах.

В этом учебнике рассматривается применение Docker для разработки и развертывания приложения Spring Boot на узле Linux в [службе контейнеров Azure (ACS)].

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется следующее.

* Подписка Azure; если у вас еще нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN] или зарегистрироваться для получения [бесплатной учетной записи Azure].
* [Интерфейс командной строки Azure (CLI)].
* Актуальная версия [Java Developer Kit (JDK)].
* Средство сборки [Maven] (версия 3) от Apache.
* Клиент [Git].
* Клиент [Docker].

> [!NOTE]
>
> С учетом требований виртуализации для этого руководства изложенные здесь инструкции нельзя выполнять на виртуальной машине. Необходимо использовать физический компьютер с включенными функциями виртуализации.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Создание веб-приложения Spring Boot в Docker

Ниже приводятся пошаговые инструкции по созданию простого веб-приложения Spring Boot и его локальному тестированию.

1. Откройте командную строку и создайте локальный каталог для размещения приложения, после чего перейдите в этот каталог, например:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- или --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Клонируйте образец проекта [Spring Boot on Docker Getting Started] (Запуск Spring Boot в Docker) в созданный каталог, например:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Перейдите в каталог готового проекта, например:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Необязательно: если вы хотите, чтобы встроенный сервер Tomcat работал с использованием порта 80, а не порта по умолчанию 8080 (например, если вы собираетесь локально тестировать проект Spring Boot), то порт можно настроить следующим образом:

   а. Перейдите в каталог ресурсов; например:
   ```
   cd src/main/resources
   ```

   b. Откройте файл *application.yml* в текстовом редакторе.

   c. Измените значение параметра **server:**, чтобы сервер работал с использованием порта 80; например:
   ```
   server:
      port: 80
   ```

   г) Сохраните и закройте файл *application.yml*.

   д. Вернитесь в корневую папку готового проекта, например:
   ```
   cd ../../..
   ```

1. Выполните сборку файла JAR с помощью Maven, например:
   ```
   mvn package
   ```

1. После создания веб-приложения перейдите в каталог `target`, где находится JAR-файл, и запустите веб-приложение, например:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. Проверьте веб-приложение, перейдя к нему локально с помощью веб-браузера. Например, если у вас установлен CuRL и сервер Tomcat настроен для работы с использованием порта 80:
   ```
   curl http://localhost
   ```

1. Должно появиться следующее сообщение: **Hello Docker World!**

   ![Локальный просмотр образца приложения][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Создание реестра контейнеров Azure для использования в качестве частного реестра Docker

Ниже рассмотрена процедура использования портала Azure для создания реестра контейнеров Azure.

> [!NOTE]
>
> Если вы хотите использовать Azure CLI, а не портал Azure, выполните процедуру, описанную в разделе [Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0](../../container-registry/container-registry-get-started-azure-cli.md).
>

1. Перейдите на [портал Azure] и выполните вход.

   После входа в свою учетную запись на портале Azure можно выполнить процедуру, описанную в статье [Создание частного реестра контейнеров Docker с помощью портала Azure], которую здесь полезно представить еще раз.

1. Щелкните значок меню **+ Создать**, нажмите кнопку **Контейнеры**, а затем нажмите кнопку **Реестр контейнеров Azure**.
   
   ![Создание нового реестра контейнеров Azure][AR01]

1. При появлении страницы сведений о шаблоне реестра контейнеров Azure нажмите кнопку **Создать**. 

   ![Создание нового реестра контейнеров Azure][AR02]

1. При появлении колонки **Создать реестр контейнеров** введите данные в поля **Имя реестра** и **Группа ресурсов**, выберите **Включить** для **администратора** и нажмите кнопку **Создать**.

   ![Настройка параметров реестра контейнеров Azure][AR03]

1. После создания реестра контейнеров перейдите в реестр контейнеров на портале Azure и нажмите кнопку **Ключи доступа**. Запишите имя пользователя и пароль для последующих шагов.

   ![Ключи доступа к реестру контейнеров Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Настройка Maven для использования ключей доступа к реестру контейнеров Azure

1. Перейдите в каталог конфигурации для установки Maven и откройте файл *settings.xml* в текстовом редакторе.

1. Добавьте параметры доступа к реестру контейнеров Azure из предыдущего раздела этого учебника в коллекцию `<servers>` в файле *settings.xml*, например:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Перейдите в каталог завершенного проекта для приложения Spring Boot (например, "*C:\SpringBoot\gs-spring-boot-docker\complete*" или "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") и откройте файл *pom.xml* в текстовом редакторе.

1. Обновите коллекцию `<properties>` в файле *pom.xml*, добавив значение сервера входа для реестра контейнеров Azure из предыдущего раздела данного учебника, например:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Обновите коллекцию `<plugins>` в файле *pom.xml* таким образом, чтобы в `<plugin>` содержались адрес сервера входа и имя для реестра контейнеров Azure из предыдущего раздела данного учебника. Например:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Перейдите в каталог завершенного проекта для приложения Spring Boot и выполните команду ниже для перестроения приложения и отправки контейнера в реестр контейнеров Azure:

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> При отправке контейнера Docker в Azure может появиться сообщение об ошибке, похожее на одно из показанных ниже, даже если контейнер Docker был успешно создан:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> В этом случае возможно будет нужно войти в Azure из командной строки Docker, например:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Затем контейнер можно передать из командной строки, например:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Создание веб-приложения в Linux в службе приложений Azure с помощью образа контейнера

1. Перейдите на [портал Azure] и выполните вход.

1. Щелкните значок меню **+ Создать**, нажмите кнопку **Интернет + мобильные устройства**, а затем нажмите кнопку **веб-приложения на платформе Linux**.
   
   ![Создание веб-приложения на портале Azure][LX01]

1. При появлении колонки **Веб-приложения на платформе Linux** введите следующие сведения.

   а. Введите уникальное имя в поле **Имя приложения**, например "*wingtiptoyslinux*."

   b. В раскрывающемся списке выберите свою **подписку**.

   c. Выберите существующую **группу ресурсов** или укажите имя, чтобы создать новую группу ресурсов.

   г) Нажмите кнопку **Настройка контейнера** и введите следующие сведения.

      * Выберите **Частный реестр**.

      * **Образ и дополнительный тег**: задайте имя контейнера из более ранней версии, например "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*"

      * **URL-адрес сервера**: укажите URL-адрес реестра из более ранней версии, например "*https://wingtiptoysregistry.azurecr.io*"

      * **Имя входа пользователя** и **Пароль**: укажите учетные данные входа из своих **ключей доступа**, которые использовались на предыдущих шагах.
   
   д. После ввода всех этих данных нажмите кнопку **ОК**.

   ![Настройка параметров веб-приложения][LX02]

1. Щелкните **Создать**.

> [!NOTE]
>
> Azure будет автоматически сопоставлять интернет-запросы со встроенным сервером Tomcat, который работает с использованием стандартных портов 80 или 8080 . Однако, если вы настроили свой встроенный сервер Tomcat так, чтобы он работал с пользовательским портом, в веб-приложение необходимо добавить переменную среды, которая определяет порт для вашего встроенного сервера Tomcat. Для этого выполните следующие действия.
>
> 1. Перейдите на [портал Azure] и выполните вход.
> 
> 2. Щелкните значок для **служб приложений**. (См. элемент ном. 1 на рисунке ниже.)
>
> 3. Выберите веб-приложение в списке. (Элемент ном. 2 на рисунке ниже.)
>
> 4. Щелкните **Параметры приложения**. (Элемент ном. 3 на рисунке ниже.)
>
> 5. В разделе **Параметры приложения** добавьте новую переменную среды с именем **PORT** и введите номер пользовательского порта в качестве значения. (Элемент ном. 4 на рисунке ниже.)
>
> 6. Щелкните **Сохранить**. (Элемент ном. 5 на рисунке ниже.)
>
> ![Сохранение номера пользовательского порта на портале Azure][LX03]
>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании приложений Spring Boot в Azure см. в следующих статьях:

* [Развертывание приложения Spring Boot Application в службе приложений Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Запуск приложения Spring Boot в кластере Kubernetes в службе контейнеров Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [инструментов Java для Visual Studio Team Services].

Дополнительные сведения о Spring Boot в образце проекта Docker см. в разделе [Spring Boot on Docker Getting Started] (Начало работы с Spring Boot в Docker).

Справку по началу работы с собственными приложениями Spring Boot см. на странице **Spring Initializr** по адресу https://start.spring.io/.

Дополнительные сведения о начале создания простого приложения Spring Boot см. на странице Spring Initializr по адресу https://start.spring.io/.

Дополнительные примеры использования пользовательских образов Docker в Azure см. в разделе [Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux].

<!-- URL List -->

[Интерфейс командной строки Azure (CLI)]: /cli/azure/overview
[службе контейнеров Azure (ACS)]: https://azure.microsoft.com/services/container-service/
[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[портал Azure]: https://portal.azure.com/
[Создание частного реестра контейнеров Docker с помощью портала Azure]: /azure/container-registry/container-registry-get-started-portal
[Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[бесплатной учетной записи Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[инструментов Java для Visual Studio Team Services]: https://java.visualstudio.com/ (Инструменты Java для Visual Studio Team Services)
[Maven]: http://maven.apache.org/
[преимущества для подписчиков MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Загрузки Spring]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png


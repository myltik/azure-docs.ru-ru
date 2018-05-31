---
title: Краткое руководство по созданию веб-приложения Java в службе приложений Azure в Linux
description: В этом кратком руководстве описывается, как быстро развернуть ваше первое приложение Java Hello World в службе приложений Azure в Linux.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355191"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Краткое руководство по созданию веб-приложения Java в службе приложений Azure в Linux

Служба приложений в Linux в настоящее время предоставляет предварительную версию функции, позволяющую создавать веб-приложения Java. Сведения о предварительных версиях функций см. на странице [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

[Служба приложений на Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом кратком руководстве показано, как использовать [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Azure CLI) с [подключаемым модулем Maven для веб-приложений Azure (предварительная версия)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), чтобы развернуть веб-приложение Java с использованием встроенного образа Linux.

![Пример приложения, выполняющегося в Azure](media/quickstart-java/java-hello-world-in-browser.png)

Можно также [развернуть веб-приложения Java в собственный контейнер Linux в облаке с помощью набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее: 

* Установленный локально [Azure CLI 2.0 или более поздней версии](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Apache Maven](http://maven.apache.org/).



## <a name="create-a-java-app"></a>Создание приложения Java

Выполните следующую команду с помощью Maven, чтобы создать веб-приложение *helloworld*.  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Перейдите в каталог нового проекта *helloworld* и выполните сборку всех модулей, используя следующую команду:

    mvn verify

Эта команда проверит и создаст все модули, в том числе файл *helloworld.war*, в подкаталоге *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Развертывание приложения Java в службе приложений на платформе Linux

Существует несколько вариантов развертывания веб-приложений Java в службе приложений на платформе Linux. Вот какие параметры доступны:

* [развертывание с помощью подключаемого модуля Maven для веб-приложений Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin);
* [развертывание с использованием ZIP- или WAR-файла](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip);
* [развертывание через FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp).

В этом кратком руководстве описывается использование подключаемого модуля Maven для веб-приложений Azure. Его легко использовать из Maven, и он создает для вас необходимые ресурсы Azure (группу ресурсов, план службы приложений и веб-приложение).

### <a name="deploy-with-maven"></a>Развертывание с помощью Maven

Чтобы выполнить развертывание из Maven, добавьте следующее определение подключаемого модуля в элемент `<build>` файла *pom.xml*.

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

Укажите нужные значения вместо следующих заполнителей в конфигурации подключаемого модуля:

| Placeholder | ОПИСАНИЕ |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Имя новой группы ресурсов, в которой создается веб-приложение. Поместив все ресурсы для приложения в группу, вы можете управлять ими совместно. Например, при удалении группы ресурсов все ресурсы, связанные с приложением, также удаляются. Укажите вместо этого значения уникальное имя новой группы ресурсов, например *TestResources*. Это имя группы ресурсов будет использоваться для удаления всех ресурсов Azure в следующем разделе. |
| `YOUR_WEB_APP` | Имя приложения будет частью имени узла для веб-приложения, которое будет развернуто в Azure (ВАШЕ_ВЕБ-ПРИЛОЖЕНИЕ.azurewebsites.net). Измените значение этого параметра на уникальное имя нового веб-приложения Azure, в котором будет размещено ваше приложение Java, например *contoso*. |

Элемент конфигурации `linuxRuntime` определяет, какой встроенный образ Linux используется для вашего приложения.

Выполните следующую команду и следуйте всем инструкциям, чтобы выполнить проверку подлинности в Azure CLI.

    az login

Разверните приложение Java для веб-приложения, используя следующую команду:

    mvn clean package azure-webapp:deploy


По завершению развертывания перейдите к развернутому приложению, используя следующий URL-адрес в своем веб-браузере.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

Пример кода Java выполняется в веб-приложении со встроенным образом.

![Пример приложения, выполняющегося в Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Поздравляем!** Вы развернули свое первое приложение Java в службе приложений на платформе Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали модуль Maven для создания веб-приложения Java, которое вы затем развернули в службе приложений на Linux. Чтобы узнать больше об использовании Java с Azure, перейдите по ссылке ниже.

> [!div class="nextstepaction"]
> [Azure для разработчиков Java](https://docs.microsoft.com/java/azure/)


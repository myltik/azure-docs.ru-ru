---
title: "Скачивание пакета Azure SDK для Java | Документация Майкрософт"
description: "Сведения о скачивании пакета Azure SDK для Java с помощью примера кода, предоставленного для проектов Maven."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Загрузка пакета Azure SDK для Java
Эта статья содержит инструкции по скачиванию и установке библиотек управления Azure для Java.

> [!NOTE]
> Библиотеки управления Azure для Java распространяются по лицензии [Apache, версии 2.0][license].
>

## <a name="azure-libraries-for-java---manual-download"></a>Библиотеки Azure для Java — скачивание вручную
Чтобы вручную установить библиотеки управления Azure для Java, щелкните ссылку <http://go.microsoft.com/fwlink/?LinkId=690320> для скачивания ZIP-файла со всеми библиотеками и зависимыми объектами.

После скачивания ZIP-файла на компьютер распакуйте содержимое и используйте один из следующих вариантов для добавления JAR-файлов в проект:

* Импортируйте JAR-файлы в свой проект Java в Eclipse или IntelliJ.
* Настройте путь сборки для проекта Java в Eclipse или IntelliJ, чтобы указать путь к JAR-файлам.

> [!NOTE]
> Сведения о лицензии и другую информацию см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Библиотеки управления Azure для Java — сборка с помощью Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Шаг 1. Настройка проекта на использование Maven для сборки
Сведения о создании в Eclipse проектов Maven, использующих библиотеки управления Azure для Java, см. в статье [Приступая к работе с библиотеками управления Azure для Java][maven-getting-started].

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Шаг 2. Настройка параметров Maven с необходимыми зависимостями
После настройки проекта на использование Maven для сборки можно добавить необходимые зависимости в файл pom.xml, используя синтаксис, как в следующем примере. Обратите внимание, что не нужно добавлять каждую из зависимостей, указанных в следующем примере; необходимо добавить лишь те зависимости, которые требуются в вашем проекте.

> [!NOTE]
> В каждом элементе `<version>` в примере ниже замените заполнители n.n.n допустимыми номерами версий, которые можно получить в [репозитории библиотек Azure на сайте Maven].
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>См. также
Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Создание веб-приложения Hello World для Azure в Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * [Создание веб-приложения Hello World для Azure в IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]

Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

> [!NOTE]
> Дополнительные сведения о настройке путей сборки в Eclipse см. в статье [Java Build Path] (Путь сборки Java) на веб-сайте Eclipse.
>

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Центре разработчиков Java для Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[репозитории библиотек Azure на сайте Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998


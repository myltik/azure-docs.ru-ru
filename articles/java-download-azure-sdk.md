---
title: "Загрузка пакета Azure SDK для Java"
description: "Узнайте, как скачать пакет SDK Azure для Java, ознакомьтесь с примером кода для проектов Maven и общими шагами по установке набора средств Azure для Eclipse."
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
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 25b7e591172ea810ce370f946eb46258c801ba36


---
# <a name="download-the-azure-sdk-for-java"></a>Загрузка пакета Azure SDK для Java
Эта статья содержит инструкции по скачиванию и установке библиотек Azure для Java.

**Примечание.** Библиотеки Azure для Java распространяются по [лицензии Apache License, версия 2.0][].

## <a name="azure-libraries-for-java---manual-download"></a>Библиотеки Azure для Java — скачивание вручную
Чтобы вручную установить библиотеки Azure для Java, щелкните ссылку <http://go.microsoft.com/fwlink/?LinkId=690320> для скачивания ZIP-файла, где содержатся все библиотеки и зависимые объекты.

После скачивания ZIP-файла на компьютер распакуйте содержимое и используйте один из следующих вариантов для добавления JAR-файлов в проект:

* Импортируйте JAR-файлы в свой проект Java в Eclipse.
* Настройте **путь сборки** для проекта Java в Eclipse, чтобы указать путь к JAR-файлам.

Дополнительные сведения о настройке путей сборки в Eclipse см. в статье [Java Build Path] (Путь сборки Java) на веб-сайте Eclipse.

**Примечание.** Лицензию и другие сведения см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.

## <a name="azure-libraries-for-java---building-with-maven"></a>Библиотеки Azure для Java — сборка с помощью Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Шаг 1. Настройка проекта на использование Maven для сборки
Сведения о том, как создавать в Eclipse проекты Maven, использующие библиотеки Azure для Java, см. в записи блога [Приступая к работе с библиотеками управления Azure для Java][maven-getting-started]. 

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

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Установка набора средств Azure для Eclipse
Этот раздел содержит общие инструкции по установке набора средств Azure для Eclipse. Подробные инструкции см. в статье [Установка набора средств Azure для Eclipse].

### <a name="prerequisites"></a>Предварительные требования
1. Операционные системы Windows, перечисленные в статье [Новые возможности набора средств Azure для Eclipse] .
2. Операционные системы Macintosh или Linux, перечисленные в статье [Новые возможности набора средств Azure для Eclipse] .
3. Интегрированная среда разработки Eclipse для разработчиков Java EE, версия Indigo или более поздняя. Скачать ее можно на веб-странице <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Общие шаги установки
1. В Eclipse в меню **Help** выберите пункт **Install New Software** (Установка нового программного обеспечения).
2. Введите адрес веб-сайта <http://dl.microsoft.com/eclipse> и нажмите клавишу **ВВОД**.
3. Выберите элементы для установки и нажмите кнопку **Готово**.

Набор средств Azure для Eclipse использует последнюю версию пакета SDK для Azure. Ее можно скачать с помощью установщика веб-платформы (WebPI), перейдя по ссылке <http://go.microsoft.com/fwlink/?LinkID=252838>. Но даже если она не установлена, при создании первого проекта развертывания Azure, набор средств Azure для Eclipse автоматически установит соответствующую версию пакета SDK для Azure.

## <a name="see-also"></a>См. также
[Набор средств Azure для Eclipse]

[Установка набора средств Azure для Eclipse] 

[Создание приложения Hello World для Azure в Eclipse]

Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

<!-- URL List -->

[Центре разработчиков Java для Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[репозитории библиотек Azure на сайте Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Набор средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Создание приложения Hello World для Azure в Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Установка набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[лицензия]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Новые возможности набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333



<!--HONumber=Nov16_HO3-->



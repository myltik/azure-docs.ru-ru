<properties 
	pageTitle="Загрузка пакета Azure SDK для Java" 
	description="Узнайте, как скачать пакет SDK Azure для Java, ознакомьтесь с примером кода для проектов Maven и общими шагами по установке набора средств Azure для Eclipse." 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/09/2016" 
	ms.author="robmcm"/>

# Загрузка пакета Azure SDK для Java

Эта статья содержит инструкции по скачиванию и установке библиотек Azure для Java.

**Примечание.** Библиотеки Azure для Java распространяются по [Лицензии Apache, версия 2.0][license].

## Библиотеки Azure для Java — скачивание вручную

Чтобы вручную установить библиотеки Azure для Java, щелкните <http://go.microsoft.com/fwlink/?LinkId=690320> для скачивания ZIP-файла, который содержит все библиотеки и зависимости.

После скачивания ZIP-файла на компьютер распакуйте содержимое и используйте один из следующих вариантов для добавления JAR-файлов в проект:

* Импортируйте JAR-файлы в свой проект Java в Eclipse.

* Настройте **путь сборки** для проекта Java в Eclipse, чтобы указать путь к JAR-файлам.

Дополнительные сведения о настройке путей сборки в Eclipse см. в статье [Путь сборки Java] на веб-сайте Eclipse.

**Примечание.** Лицензию и другие сведения см. в файлах license.txt и ThirdPartyNotices.txt внутри ZIP-архива.

## Библиотеки Azure для Java — сборка с помощью Maven

### Шаг 1. Настройка проекта на использование Maven для сборки

Чтобы создать в Eclipse проекты Maven, использующие библиотеки Azure для Java, см. статью [Приступая к работе с библиотеками управления Azure для Java][maven-getting-started].

### Шаг 2. Настройка параметров Maven с необходимыми зависимостями

После настройки проекта на использование Maven для сборки можно добавить необходимые зависимости в файл pom.xml, используя синтаксис, как в следующем примере. Обратите внимание, что не нужно добавлять каждую из зависимостей, указанных в следующем примере; необходимо добавить лишь те зависимости, которые требуются в вашем проекте.

> [AZURE.NOTE] В каждом элементе `<version>` в приведенном ниже примере замените заполнители "n.n.n" допустимыми номерами версий, которые можно получить в [репозитории библиотек Azure в Maven].

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

## Установка набора средств Azure для Eclipse

Этот раздел содержит общие инструкции по установке набора средств Azure для Eclipse, подробные инструкции см. в статье [Установка набора средств Azure для Eclipse].

### Предварительные требования

1. Операционные системы Windows, перечисленные в статье [Новые возможности набора средств Azure для Eclipse].
1. Операционные системы Macintosh или Linux, перечисленные в статье [Новые возможности набора средств Azure для Eclipse].
1. Интегрированная среда разработки Eclipse для разработчиков Java EE, версия Indigo или более поздняя. Среду также можно загрузить с веб-страницы <http://www.eclipse.org/downloads/>.

### Общие шаги установки

1. В Eclipse в меню **Справка** выберите пункт **Установка нового программного обеспечения**.
1. Введите местоположение веб-сайта <http://dl.microsoft.com/eclipse> и нажмите клавишу **ВВОД**.
1. Выберите элементы для установки и нажмите кнопку **Готово**.

Набор средств Azure для Eclipse использует последнюю версию пакета SDK для Azure. Ее можно скачать с помощью установщика веб-платформы (WebPI) в <http://go.microsoft.com/fwlink/?LinkID=252838>. Но даже если она не установлена, при создании первого проекта развертывания Azure, набор средств Azure для Eclipse автоматически установит соответствующую версию пакета SDK для Azure.

## См. также

[Набор средств Azure для Eclipse]

[Установка набора средств Azure для Eclipse]

[Создание приложения Hello World для Azure в Eclipse]

Дополнительные сведения об использовании Azure с Java см. в [центре разработчиков Java для Azure].

<!-- URL List -->

[центре разработчиков Java для Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[репозитории библиотек Azure в Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Набор средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Создание приложения Hello World для Azure в Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Установка набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Путь сборки Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Новые возможности набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_0309_2016-->
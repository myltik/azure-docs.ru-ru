---
title: "Создание веб-приложения в службе приложений Azure с использованием пакета SDK для Azure для Java"
description: "Узнайте, как программным путем создать веб-приложение в службе приложений Azure, используя пакет SDK для Azure для Java."
tags: azure-classic-portal
services: app-service\web
documentationcenter: Java
author: donntrenton
manager: wpickett
editor: jimbe
ms.assetid: 8954c456-1275-4d57-aff4-ca7d6374b71e
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/25/2016
ms.author: v-donntr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e7e2c6ef375b860ad79f0cc0c385dec2e5de2660


---
# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Создание веб-приложения в службе приложений Azure с использованием пакета SDK для Azure для Java
<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Обзор
В этом пошаговом руководстве показано, как создать пакет SDK для Azure для приложения Java (создает веб-приложение в [службе приложений Azure][службы приложений Azure]), а затем развернуть это приложение. Руководство состоит из двух частей:

* в первой части показано, как создать приложение Java, которое позволяет создать веб-приложение;
* во второй части показано, как создать простое приложение JSP Hello World, а затем использовать FTP-клиент для развертывания кода в службе приложений.

## <a name="prerequisites"></a>Предварительные требования
### <a name="software-installations"></a>Установка программного обеспечения
Код приложения AzureWebDemo в этой статье написан с использованием пакета SDK 0.7.0 для Azure для Java, который можно установить с помощью [установщика веб-платформы ][Установщик веб-платформы] (WebPI). Следует использовать последнюю версию [набора средств Azure для Eclipse][Набор средств Azure для Eclipse]. После установки пакета SDK обновите зависимости в проекте Eclipse, выполнив команду **Update Index** (Обновить индекс) в представлении **Maven Repositories** (Репозитории Maven), а затем снова добавьте последнюю версию каждого пакета в окне **Dependencies** (Зависимости). Вы можете проверить версию установленного программного обеспечения в Eclipse, щелкнув **Help > Installation Details** (Справка > Подробная информация об установке). Вы увидите по крайней мере следующие версии:

* пакет для библиотек Microsoft Azure для Java 0.7.0.20150309;
* интегрированная среда разработки Eclipse для разработчиков Java EE 4.4.2.20150219.

### <a name="create-and-configure-cloud-resources-in-azure"></a>Создание и настройка облачных ресурсов в Azure
Прежде чем начать эту процедуру, необходимо убедиться, что у вас есть активная подписка Azure, и настроить Active Directory (AD) по умолчанию в Azure.

### <a name="create-an-active-directory-ad-in-azure"></a>Создание Active Directory в Azure
Если в вашей подписке Azure еще нет каталога Active Directory (AD), войдите на [классический портал Azure][Классический портал Azure] с помощью учетной записи Майкрософт. Если у вас несколько подписок, щелкните **Подписки** и выберите каталог по умолчанию для подписки, которая будет использоваться для этого проекта. Нажмите кнопку **Применить**, чтобы перейти в представление этой подписки.

1. В меню слева выберите **Active Directory** . Щелкните **Создать > Каталог > Настраиваемое создание**.
2. В разделе **Добавить каталог** выберите **Создать каталог**.
3. В поле **Имя**введите имя каталога.
4. В поле **Домен** введите имя домена. Это имя основного домена, включенное по умолчанию вместе с каталогом. У него такой формат: `<domain_name>.onmicrosoft.com`. Домен можно назвать на основе имени каталога или имени другого домена, которым вы владеете. Позже вы сможете добавить другое доменное имя, которое уже использует ваша организация.
5. В поле **Страна или регион**выберите свой языковой стандарт.

Дополнительную информацию об Active Directory см. в статье [Что такое каталог Azure AD?][What is an Azure AD directory]

### <a name="create-a-management-certificate-for-azure"></a>Создание сертификата управления для Azure
Пакет SDK для Azure для Java использует сертификаты управления для аутентификации с использованием подписок Azure. Вы используете такие сертификаты, а именно X.509 v3, для аутентификации клиентских приложений, использующих API управления службами для работы от имени владельца подписки с целью управления ресурсами подписки.

Для прохождения аутентификации с помощью Azure код в этой процедуре использует самозаверяющий сертификат. Чтобы выполнить эту процедуру, необходимо создать сертификат и заранее отправить его на [классический портал Azure][Классический портал Azure]. Для этого необходимо выполнить следующие шаги.

* Создайте PFX-файл, представляющий собой сертификат клиента, и сохраните его локально.
* Создайте сертификат управления (CER-файл) из PFX-файла.
* Загрузите CER-файл в подписку Azure.
* Преобразуйте PFX-файл в JKS-файл, так как Java использует этот формат для аутентификации с использованием сертификатов.
* Напишите код для аутентификации приложения, связанный с локальным JKS-файлом.

При выполнении этой процедуры CER-сертификат будет находиться в подписке Azure, а JKS-сертификат — на локальном диске. Дополнительную информацию о сертификатах управления см. в статье [Общие сведения о сертификатах для облачных служб Azure][Create and Upload a Management Certificate for Azure].

#### <a name="create-a-certificate"></a>Создание сертификата
Чтобы создать самозаверяющий сертификат, откройте консоль командной строки в своей операционной системе и выполните следующие команды.

> **Примечание.** На компьютере, где вы будете выполнять эту команду, должен быть установлен пакет JDK. Кроме того, путь к keytool зависит от расположения, в котором установлен этот пакет. Дополнительную информацию см. в разделе онлайн-документации Java, посвященном [средству управления сертификатами и ключами (keytool)][Key and Certificate Management Tool (keytool)]
> 
> 

Чтобы создать PFX-файл, выполните следующее:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Чтобы создать CER-файл, выполните следующее:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

где:

* `<java-install-dir>` — это путь к каталогу, в котором установлено приложение Java;
* `<keystore-id>` — это идентификатор записи в хранилище ключей (например, `AzureRemoteAccess`);
* `<cert-store-dir>` — это путь к каталогу, в котором вам необходимо хранить сертификаты (например, `C:/Certificates`);
* `<cert-file-name>` — это имя файла сертификата (например, `AzureWebDemoCert`);
* `<password>` — это пароль, выбранный для защиты сертификата. Он должен состоять по крайней мере из 6 символов. Вы можете вовсе не указывать пароль, хотя это не рекомендуется;
* `<dname>` — различающееся имя X.500, которое необходимо связать с псевдонимом. Оно используется в полях издателя и субъекта самозаверяющего сертификата.

Дополнительную информацию см. в статье [Общие сведения о сертификатах для облачных служб Azure][Create and Upload a Management Certificate for Azure].

#### <a name="upload-the-certificate"></a>Загрузка сертификата
Чтобы отправить самозаверяющий сертификат в Azure, перейдите на страницу **Параметры** на классическом портале, а затем щелкните вкладку **Сертификаты управления**. Щелкните **Загрузить** в нижней части страницы и перейдите к расположению CER-файла, который вы создали.

#### <a name="convert-the-pfx-file-into-jks"></a>Преобразование PFX-файла в JKS-файл
В командной строке Windows (ее следует запустить под учетной записью администратора) перейдите в каталог, в котором содержатся сертификаты, и выполните следующую команду, где `<java-install-dir>` — это каталог с установленным приложением Java:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. При появлении запроса введите пароль назначения для хранилища ключей, который будет паролем JKS-файла.
2. При появлении запроса введите исходный пароль хранилища ключей. Это пароль, который вы указали для PFX-файла.

Эти два пароля могут не совпадать. Вы можете вовсе не указывать пароль, хотя это не рекомендуется.

## <a name="build-a-web-app-creation-application"></a>Сборка приложения для создания веб-приложения
### <a name="create-the-eclipse-workspace-and-maven-project"></a>Создание рабочей области Eclipse и проекта Maven
В этом разделе вы создадите рабочую область и проект Maven для приложения, создающего веб-приложение AzureWebDemo.

1. Создайте проект Maven. Щелкните **File > New > Maven Project** (Файл > Создать > Проект Maven). На странице **New Maven Project** (Новый проект Maven) выберите **Create a simple project** (Создать простой проект) и **Use default workspace location** (Использовать расположение рабочей области по умолчанию).
2. На второй странице **Новый проект Maven**укажите следующее:
   
   * идентификатор группы — `com.<username>.azure.webdemo`
   * идентификатор артефакта — AzureWebDemo;
   * версия— 0.0.1-SNAPSHOT;
   * упаковка — JAR;
   * имя — AzureWebDemo.
     
     Нажмите кнопку **Готово**
3. Откройте файл нового проекта pom.xml в обозревателе проектов. Выберите вкладку **Зависимости** . Так как это новый проект, здесь еще нет списка пакетов.
4. Откройте представление «Репозитории Maven». Щелкните **Window > Show View > Other > Maven > Maven Repositories** (Окно > Показать представление > Другие > Maven > Репозитории Maven) и нажмите кнопку **OK**. Представление **Репозитории Maven** будет отображаться в нижней части интегрированной среды разработки.
5. Откройте представление **Global Repositories** (Глобальные репозитории), щелкните правой кнопкой мыши **central** (центральный репозиторий), а затем щелкните **Rebuild Index** (Перестроить индекс).
   
    ![][1]
   
    Выполнение этого шага может длиться несколько минут в зависимости от скорости подключения. Когда индекс перестроится, вы увидите пакеты Microsoft Azure в **центральном** репозитори Maven.
6. На вкладке **Dependencies** (Зависимости) щелкните **Add** (Добавить). В поле **Enter Group ID…** (Введите идентификатор группы…) введите `azure-management`. Выберите пакеты для базового управления и управления веб-приложениями службы приложений:
   
        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites
   
   > **Примечание.** При обновлении зависимостей после выпуска новой версии в этот список необходимо повторно добавить все зависимости.
   > После того как вы щелкнете **Add** (Добавить) и выберете зависимости, они будут отображаться с номером новой версии в списке **Dependencies** (Зависимости).
   > 
   > 

Нажмите кнопку **ОК**. После этого в списке **Зависимости** будут отображаться пакеты Azure.

### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Написание кода Java для создания веб-приложения путем вызова пакета SDK для Azure
Теперь напишите код, который вызывает API в пакете SDK для Azure для Java, чтобы создать веб-приложение службы приложений.

1. Создайте класс Java, который будет содержать код главной точки входа. В обозревателе проектов щелкните узел проекта правой кнопкой мыши и выберите **Создать > Класс**.
2. В окне **Новый класс Java`WebCreator` укажите имя класса ** и установите флажок **public static void main**. Значения выбранных параметров должны выглядеть так:
   
    ![][2]
3. Нажмите кнопку **Готово** В обозревателе проектов отобразится файл WebCreator.java.

### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Вызов API Azure для создания веб-приложения службы приложений
#### <a name="add-necessary-imports"></a>Добавление необходимых операций импорта
Добавьте следующие операции импорта в файл WebCreator.java. Эти операции позволяют получить доступ к классам в библиотеках управления для используемых API Azure:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;

    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;

    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;

    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;

    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Определение класса главной точки входа
Так как приложение AzureWebDemo предназначено для создания веб-приложения службы приложений, назовите основной класс для этого приложения — `WebAppCreator`. Этот класс предоставляет код главной точки входа, который вызывает API управления службами Azure для создания веб-приложения.

Добавьте следующие определения параметров для веб-приложения и веб-пространства. Вам понадобиться указать идентификатор подписки Azure и информацию о сертификате.

    public class WebAppCreator {

        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";

        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

где:

* `<subscription-id>` — это идентификатор подписки Azure, в рамках которой нужно создать группу ресурсов;
* `<certificate-store-path>` — это путь и имя JKS-файла в каталоге локального хранилища сертификатов (например, `C:/Certificates/CertificateName.jks` — для Linux, а `C:\Certificates\CertificateName.jks` — для Windows);
* `<certificate-password>` — это пароль, указанный при создании JKS-сертификата;
* в качестве `webAppName` можно указать любое имя. В этой процедуре используется имя `WebDemoWebApp`. Полное доменное имя — `webAppName`, к которому добавлено `domainName`, поэтому в этом случае полное имя домена — `webdemowebapp.azurewebsites.net`;
* `domainName` следует указать, как показано выше;
* параметру `webSpaceName` следует присвоить одно из значений, определенных в классе [WebSpaceNames][WebSpaceNames];
* `appServicePlanName` следует указать, как показано выше;

> **Примечание.** При каждом запуске этого приложения необходимо изменять значения `webAppName` и `appServicePlanName` (или удалять веб-приложение на портале Azure), прежде чем запускать его повторно. В противном случае произойдет сбой выполнения, так как в Azure уже есть такой же ресурс.
> 
> 

#### <a name="define-the-web-creation-method"></a>Определение метода создания веб-приложения
Далее следует определить метод создания веб-приложения. Метод `createWebApp`указывает параметры веб-приложения и веб-пространства. Он также создает и настраивает клиент управления веб-приложениями службы приложений, определенный объектом [WebSiteManagementClient][WebSiteManagementClient]. Клиент управления — это ключ к созданию веб-приложений. Он позволяет использовать веб-службы RESTful, которые, в свою очередь, позволяют приложениям управлять веб-приложениями (операции создания, обновления и удаления), вызывая API управления службами.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Код будет выводить состояние HTTP из ответа. Это состояние будет свидетельствовать об успешном выполнении или сбое. В случае успешного выполнения выводится имя созданного веб-приложения.

#### <a name="define-the-main-method"></a>Определение метода main()
Укажите код метода main(), который вызывает createWebApp() для создания веб-приложения.

Наконец, вызовите `createWebApp` из `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Запуск приложения и проверка созданного веб-приложения
Чтобы убедиться, что приложение работает, щелкните **Запуск > Запустить**. После завершения работы приложения вы увидите следующие выходные данные в консоли Eclipse:

    ----------
    Web app created - HTTP response 200

    ----------

    Name of web app created: WebDemoWebApp

    ----------

Войдите на классический портал Azure и щелкните **Веб-приложения**. Через несколько минут в списке «Веб-приложения» должно отобразиться новое веб-приложение.

## <a name="deploying-an-application-to-the-web-app"></a>Развертывание приложения в веб-приложение
После того, как вы запустили AzureWebDemo и создали веб-приложение, войдите на классический портал, щелкните **Веб-приложения**и выберите **WebDemoWebApp** in the **Веб-приложения** . На странице панели мониторинга веб-приложения щелкните **Обзор** (или щелкните URL-адрес `webdemowebapp.azurewebsites.net`) для перехода. Вы увидите пустую страницу заполнителя, так как в веб-приложении еще не опубликовали никакого содержимого.

Далее мы создадим приложение Hello World и развернем его в веб-приложение.

### <a name="create-a-jsp-hello-world-application"></a>Создание приложения JSP Hello World
#### <a name="create-the-application"></a>Создание приложения
Чтобы продемонстрировать то, как развернуть приложение в веб-приложение, далее описывается процедура создания простого приложения Java Hello World и его загрузки в веб-приложение службы приложений, которое создано с помощью вашего приложения.

1. Щелкните **File > New > Dynamic Web Project** (Файл > Создать > Динамический веб-проект). Назовите его `JSPHello`. В этом диалоговом окне не нужно менять никакие другие настройки. Нажмите кнопку **Готово**
   
    ![][3]
2. В обозревателе проектов разверните проект **JSPHello**, щелкните правой кнопкой мыши **WebContent**, а затем щелкните **New > JSP File** (Создать > JSP-файл). В диалоговом окне «Создание JSP-файла» укажите имя для нового файла `index.jsp`. Нажмите кнопку **Далее**.
3. В диалоговом окне **Select JSP Template** (Выбор шаблона JSP) выберите **New JSP File (html)** (Новый JSP-файл (HTML)) и нажмите кнопку **Готово**.
4. В файле index.jsp добавьте следующий код в разделы с тегами `<head>` и `<body>`:
   
        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
   
        <body>
          Hello, the time is <%= date %> 
        </body>

#### <a name="run-the-hello-world-application-in-localhost"></a>Запуск приложения Hello World на localhost
Перед запуском этого приложения необходимо настроить некоторые свойства.

1. Щелкните правой кнопкой мыши проект **JSPHello** и выберите пункт **Properties** (Свойства).
2. В диалоговом окне **Properties** (Свойства) выберите **Java Build Path** (Путь сборки Java), щелкните вкладку **Order and Export** (Порядок и экспорт) и установите флажок **JRE System Library** (Системная библиотека JRE), а затем щелкните **Up** (Вверх), чтобы переместить библиотеку в начало списка.
   
    ![][4]
3. В диалоговом окне **Properties** (Свойства) выберите **Targeted Runtimes** (Целевые среды выполнения) и щелкните **New** (Создать).
4. В диалоговом окне **New Server Runtime Environment** (Создание среды выполнения сервера) выберите сервер, например **Apache Tomcat v7.0**, и щелкните **Next** (Далее). В диалоговом окне **Tomcat Server** (Сервер Tomcat) в поле **Name** (Имя) задайте `Apache Tomcat v7.0`, а в поле **Tomcat Installation Directory** (Каталог установки Tomcat) — каталог с установленной версией сервера Tomcat, которую необходимо использовать.
   
    ![][5]
   
    Нажмите кнопку **Готово**
5. Затем вернитесь на страницу **Targeted Runtimes** (Целевые среды выполнения) диалогового окна **Properties** (Свойства). Выберите **Apache Tomcat v7.0** и нажмите кнопку **OK**.
   
    ![][6]
6. В Eclipse в меню **Run** (Запуск) щелкните **Run** (Запустить). В диалоговом окне **Run As** (Запустить как) выберите **Run on Server** (Запустить на сервере). В диалоговом окне **Run on Server** (Запустить на сервере) выберите **Tomcat v7.0 Server** (Сервер Tomcat версии 7.0):
   
    ![][7]
   
    Нажмите кнопку **Готово**
7. Когда приложение запустится, вы увидите страницу **JSPHello`http://localhost:8080/JSPHello/` в окне localhost в Eclipse (**), где будет отображаться следующее сообщение:
   
    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`

#### <a name="export-the-application-as-a-war"></a>Экспорт приложения в виде WAR-файла
Экспортируйте файлы веб-проектов в виде файлов веб-архивов (WAR-файлов), чтобы вы могли развернуть их в веб-приложение. В папке WebContent находятся следующие файлы веб-проектов:

    META-INF
    WEB-INF
    index.jsp

1. Щелкните правой кнопкой мыши папку WebContent и выберите **Экспортировать**.
2. В диалоговом окне **Export Select** (Экспорт выбранного содержимого) щелкните **Web > WAR** (Веб > WAR-файл), а затем щелкните **Next** (Далее).
3. В диалогом окне **Экспорт WAR-файлов** выберите каталог src в текущем проекте и добавьте имя WAR-файла в конце. Например:
   
    `<project-path>/JSPHello/src/JSPHello.war`

Дополнительную информацию о развертывании WAR-файлов см. в статье [Добавление приложения Java в веб-приложения службы приложений Azure](web-sites-java-add-app.md).

### <a name="deploying-the-hello-world-application-using-ftp"></a>Развертывание приложения Hello World с использованием FTP
Выберите сторонний FTP-клиент для публикации приложения. Эта процедура описана в двух вариантах — использование консоли Kudu, встроенной в Azure, и использование FileZilla, популярного инструмента с удобным графическим пользовательским интерфейсом.

> **Примечание.** Набор средств Azure для Eclipse поддерживает развертывание в учетных записях хранения и облачных службах, но сейчас не поддерживает развертывание в веб-приложениях. Вы можете выполнять развертывание в учетных записях хранения или облачных службах, используя проект развертывания Azure, как описано в разделе [Создание приложения Hello World для Azure в Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), но не можете выполнять развертывания в веб-приложения. Используйте другие способы передачи файлов в свое веб-приложение, например с использованием FTP или GitHub.
> 
> **Примечание.** Мы не советуем использовать FTP через командную строку Windows (служебную программу командной строки FTP.EXE, которая включена в комплект Windows). FTP-клиенты, использующие FTP в активном режиме, например FTP.EXE, зачастую дают сбой при работе с брандмауэрами. FTP в активном режиме задает внутренний адрес локальной сети, к которой FTP-серверу, скорее всего, не удастся подключится.
> 
> 

Дополнительные сведения о развертывании веб-приложения службы приложений с помощью FTP см. в следующих статьях:

* [Развертывание с помощью служебной программы FTP](web-sites-deploy.md)

#### <a name="set-up-deployment-credentials"></a>"Настройка учетных данных для развертывания"
Прежде чем начать создание веб-приложения, убедитесь, что запущено приложение **AzureWebDemo** . Вы будете передавать файлы в это расположение.

1. Войдите на классический портал Azure и щелкните **Веб-приложения**. Убедитесь, что **WebDemoWebApp** отображается в списке веб-приложений и что приложение работает. Щелкните **WebDemoWebApp**, чтобы открыть соответствующую страницу **панели мониторинга**.
2. На странице **панели мониторинга** в разделе **Сводка** щелкните **Настроить учетные данные развертывания** (если у вас уже есть учетные данные развертывания, этот пункт будет называться **Сброс учетных данных развертывания**).
   
    Учетные данные развертывания связаны с учетной записью Майкрософт. Необходимо указать имя пользователя и пароль, которые можно использовать для развертывания с использованием Git и FTP. Вы можете использовать эти учетные данные для развертывания в любое веб-приложение в пределах любой подписки Azure, связанной с учетной записью Майкрософт. Укажите учетные данные развертывания с использованием Git и FTP в диалоговом окне и запишите имя пользователя и пароль, чтобы использовать их в будущем.

#### <a name="get-ftp-connection-information"></a>Получение информации о подключении по FTP
Чтобы использовать FTP для развертывания файлов приложения в созданное веб-приложение, необходимо получить информацию о подключении. Ее можно получить двумя способами. Первый способ — посетить страницу **Панель мониторинга** в веб-приложении, а второй способ — скачать профиль публикации веб-приложения. Профиль публикации — это XML-файл, который содержит такую информацию, как имя узла FTP и учетные данные для входа ваших веб-приложений в службе приложений Azure. Вы можете использовать эти имя пользователя и пароль не только для развертывания этого приложения, но и для развертывания в любые веб-приложения в рамках любых подписок, связанных с учетной записью Azure.

Чтобы получить информацию о подключении по FTP из колонки веб-приложения на [портале Azure][портале Azure], сделайте следующее.

1. В разделе **Основные компоненты** найдите и скопируйте имя в поле **Имя узла FTP**. Это URI, аналогичный `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.
2. В разделе **Основные компоненты** найдите и скопируйте **имя пользователя FTP или развертывания**. У него будет такой формат: *webappname\deployment-username* (например, `WebDemoWebApp\deployer77`).

Чтобы получить информацию о подключении по FTP из профиля публикации:

1. В колонке веб-приложения щелкните **Получить профиль публикации**. Это позволит скачать PUBLISHSETTINGS-файл на локальный диск.
2. Откройте PUBLISHSETTINGS-файл в редакторе XML-файлов или текстовом редакторе и найдите элемент `<publishProfile>`, в котором содержится `publishMethod="FTP"`. Он должен выглядеть примерно так:
   
        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>
3. Обратите внимание, что параметры `publishProfile` веб-приложения соответствуют параметрам диспетчера сайта FileZilla, как приведено ниже.

* `publishUrl` — значение этого параметра аналогично значению **Имя узла FTP**, заданному в поле **Узел**.
* `publishMethod="FTP"` означает, что для параметра **Протокол** вы задали значение **FTP — протокол передачи файлов**, а для параметра **Шифрование** — **Use plain FTP** (Использовать простой FTP).
* `userName` и `userPWD` — это ключи для фактических значений имени пользователя и пароля, которые вы указали при сбросе учетных данных развертывания. `userName` — значение этого параметра аналогично значению **Пользователь FTP или развертывания**. Они соответствуют параметрам **User** (Пользователь) и **Password** (Пароль) в FileZilla.
* `ftpPassiveMode="True"` означает, что FTP-сайт использует пассивный режим передачи FTP. Выберите **Passive** (Пассивный) на вкладке **Transfer Settings** (Параметры передачи).

#### <a name="configure-the-web-app-to-host-a-java-application"></a>Настройка размещения приложения Java в веб-приложении
Прежде чем опубликовать приложение, необходимо изменить некоторые параметры конфигурации, чтобы в веб-приложении можно было разместить приложение Java.

1. На классическом портале перейдите на страницу **Панель мониторинга** в веб-приложении и щелкните **Настроить**. На странице **Настройка** укажите следующие параметры.
2. Значение по умолчанию для параметра **Версия Java** — **Отключено**. Выберите целевую версию Java для своего приложения, например 1.7.0_51. После этого убедитесь, что для **веб-контейнера** указана версия сервера Tomcat.
3. В раздел **Документы по умолчанию** добавьте файл index.jsp и переместите его в начало списка. (файл по умолчанию для веб-приложений— hostingstart.html).
4. Щелкните **Сохранить**.

#### <a name="publish-your-application-using-kudu"></a>Публикация приложения с помощью Kudu
Один из способов публикации приложения предусматривает использование консоли отладки Kudu, встроенной в Azure. Kudu известна своей стабильностью и согласованностью с веб-приложениями службы приложений и сервером Tomcat. Для работы с веб-приложением консоль можно открыть, перейдя по URL-адресу такого формата:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Консоль Kudu для этой процедуры размещена по следующему URL-адресу. Перейдите к этому расположению:
   
    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`
2. В меню вверху щелкните **Консоль отладки > CMD**.
3. В командной строке консоли перейдите к `/site/wwwroot` (или щелкните `site`, а затем `wwwroot` в представлении каталога в верхней части страницы):
   
    `cd /site/wwwroot`
4. После того как вы укажете значение для **Версия Java**, сервер Tomcat должен создать каталог веб-приложений. В командной строке консоли перейдите к каталогу веб-приложений:
   
    `mkdir webapps`
   
    `cd webapps`
5. Перетащите файл JSPHello.war из `<project-path>/JSPHello/src/` и поместите его в представление каталога Kudu в `/site/wwwroot/webapps`. Не перетаскивайте его в область «Перетащить сюда, чтобы загрузить и запаковать», так как Tomcat распакует его.
   
   ![][8]

Сначала файл JSPHello.war сам собой отобразится в области каталога:

  ![][9]

Через короткий промежуток времени (возможно, меньше 5 минут) сервер Tomcat распакует WAR-файл в распакованный каталог JSPHello. Выберите каталог ROOT, чтобы увидеть, распакован и скопирован ли сюда файл index.jsp. Если это выполнено, перейдите назад к каталогу веб-приложений, чтобы убедиться, что создан распакованный каталог JSPHello. Если эти элементы не отображаются, подождите, а затем повторите процедуру.

  ![][10]

#### <a name="publish-your-application-using-filezilla-optional"></a>Публикация приложения с помощью FileZilla (необязательно)
Для публикации своего приложения вы также можете использовать другой инструмент — FileZilla, популярный сторонний FTP-клиент с удобным графическим пользовательским интерфейсом. Скачать и установить FileZilla можно на веб-сайте [http://filezilla-project.org/](http://filezilla-project.org/). Дополнительную информацию об использовании этого клиента см. в [документации по FileZilla](https://wiki.filezilla-project.org/Documentation), а также в записи блога, посвященной [FTP-клиентам (часть 4 — FileZilla)](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. В FileZilla щелкните **File > Site Manager** (Файл > Диспетчер сайтов).
2. В диалоговом окне **Site Manager** (Диспетчер сайтов) щелкните **New Site** (Создать сайт). В **Выберите запись** отобразиться новый пустой FTP-сайт с запросом на указание имени. Укажите имя `AzureWebDemo-FTP`для использования в этой процедуре.
   
    На вкладке **Общие** укажите следующие параметры.
   
   * **Host** (Узел): введите **имя узла FTP**, скопированное на панели мониторинга.
   * **Порт:** (оставьте поле пустым, так как это пассивная передача, и сервер будет определять, какой порт использовать).
   * **Протокол:** протокол передачи файлов FTP.
   * **Шифрование:** используйте простой FTP.
   * **Тип входа:** обычный.
   * **Пользователь:** укажите пользователя развертывания или узла FTP, который был скопирован из панели мониторинга. Это полное имя пользователя FTP в формате *имя_веб-приложения\имя_пользователя*.
   * **Пароль:** введите пароль, указанный при задании учетных данных развертывания.
     
     На вкладке **Transfer Settings** (Параметры передачи) выберите **Passive** (Пассивный).
3. Щелкните **Подключить**. При успешном подключении в консоли FileZilla отобразиться сообщение `Status: Connected` и будет выполнена команда `LIST`, позволяющая вывести список содержимого каталога.
4. На панели сайта **Local** (Локальные) выберите исходный каталог, в котором находится файл JSPHello.war. Путь будет выглядеть приблизительно так:
   
    `<project-path>/JSPHello/src/`
5. На панели сайта **Удаленные** выберите папку назначения. Разверните WAR-файл в каталоге `webapps` в корне веб-приложения. Перейдите к `/site/wwwroot`, щелкните правой кнопкой мыши `wwwroot` и выберите **Create directory** (Создать каталог). Укажите для каталога имя `webapps` и войдите в этот каталог.
6. Передайте JSPHello.war в `/site/wwwroot/webapps`. Выберите JSPHello.war в списке файлов **Local** (Локальные), щелкните его правой кнопкой мыши и выберите **Upload** (Отправить). Вы должны увидеть его в `/site/wwwroot/webapps`.
7. После копирования JSPHello.war в каталог веб-приложений сервер Tomcat автоматически распакует файлы в WAR-файле. Несмотря на то, что сервер Tomcat начинает распаковку почти мгновенно, файлы могут отобразиться в FTP-клиенте спустя большой период времени (возможно, через несколько часов).

#### <a name="run-the-hello-world-application-on-the-web-app"></a>Запуск приложения Hello World в веб-приложении
1. Загрузив WAR-файл и убедившись, что сервер Tomcat создал распакованный каталог `JSPHello`, перейдите к `http://webdemowebapp.azurewebsites.net/JSPHello`, чтобы запустить приложение.
   
   > **Примечание.** Если вы щелкнете **Обзор** на классическом портале, может отобразиться стандартная веб-страница с сообщением "This Java based web application has been successfully created" (Это веб-приложение на основе Java успешно создано). Возможно, понадобится обновить веб-страницу, чтобы просмотреть выходные данные приложения вместо веб-страницы по умолчанию.
   > 
   > 
2. Когда приложение запустится, вы должны увидеть веб-страницу со следующими выходными данными:
   
    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`

#### <a name="clean-up-azure-resources"></a>Очистка ресурсов Azure
Эта процедура создает веб-приложение службы приложений. На протяжении существования ресурсов за них будут выставляться счета. Если вы не планируете продолжить использование веб-приложения для тестирования или разработки, вам следует остановить или удалить его. За остановленное веб-приложение все равно взимается небольшая плата, зато его можно перезапустить в любое время. При удалении приложения будут стерты все загруженные в него данные.

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
[2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
[3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
[4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
[5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
[6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
[7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
[8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
[9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
[10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png


[службы приложений Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Установщик веб-платформы]: http://go.microsoft.com/fwlink/?LinkID=252838
[Набор средств Azure для Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Классический портал Azure]: https://manage.windowsazure.com
[What is an Azure AD directory]: http://technet.microsoft.com/library/jj573650.aspx
[Create and Upload a Management Certificate for Azure]: ../cloud-services/cloud-services-certs-create.md
[Key and Certificate Management Tool (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[портале Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->



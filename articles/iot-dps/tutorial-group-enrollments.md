---
title: Подготовка имитированного устройства X.509 в Центре Интернета вещей Azure с помощью Java и групп регистрации | Документация Майкрософт
description: Руководство Azure. Создание и подготовка имитированного устройства X.509 с помощью Java устройства, SDK службы и групп регистрации для службы подготовки устройств Центра Интернета вещей
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 0ebf71a68f00b9766e14ea775fa2b1e9f15a201b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Создание и подготовка имитированного устройства X.509 с помощью Java устройства, SDK службы и групп регистрации для службы подготовки устройств Центра Интернета вещей

В этом руководстве показано, как имитировать устройство X.509 на компьютере разработки под управлением ОС Windows, а также как с помощью примера кода подключить имитированное устройство к службе подготовки устройств и Центру Интернета вещей, используя группы регистрации. 

Прежде чем продолжить, выполните инструкции по [настройке службы подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Подготовка среды 

1. Убедитесь, что на вашем компьютере установлен [пакет SDK 8 для Java SE](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

1. Скачайте и установите [Maven](https://maven.apache.org/install.html).

1. Установите на компьютер систему `git` и добавьте ее в переменные среды, доступные в командном окне. Последнюю версию средств `git` для установки, которая включает **Git Bash**, приложение командной строки для взаимодействия с локальным репозиторием Git, можно найти на [этой странице](https://git-scm.com/download/). 

1. Для создания тестовых сертификатов воспользуйтесь данными из [обзора сертификата](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). Более подробно создание сертификатов рассмотрено в статье [PowerShell scripts to manage CA-signed X.509 certificates](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates) (Использование сценариев PowerShell для управления сертификатами X.509, подписанными ЦС).

    > [!NOTE]
    > На этом шаге необходим [OpenSSL](https://www.openssl.org/), который можно либо создать и установить из источника, либо скачать и установить из [сторонних ресурсов](https://wiki.openssl.org/index.php/Binaries), например [из этого](https://sourceforge.net/projects/openssl/). Если _корневой_, _промежуточный_ сертификаты и сертификат _устройства_ уже созданы, то этот шаг можно пропустить.
    >

    1. Выполните первые два шага, чтобы создать _корневой_ и _промежуточный_ сертификаты.

    1. Войдите на портал Azure, нажмите кнопку **Все ресурсы** в меню слева и откройте службу подготовки.

        1. В колонке сводной информации о службе подготовки устройств выберите **Сертификаты** и нажмите кнопку **Добавить** в верхней части экрана.

        1. В разделе **Добавление сертификата** введите следующие данные.
            - Введите уникальное имя сертификата.
            - Выберите созданный файл **_RootCA.pem_**.
            - Затем нажмите кнопку **Сохранить**.

        ![Добавление сертификата](./media/tutorial-group-enrollments/add-certificate.png)

        1. Выберите только что созданный сертификат.
            - Щелкните **Создать код проверки**. Скопируйте созданный код.
            - Выполните этап проверки. Введите _код проверки_ или щелкните правой кнопкой мыши в открытом окне PowerShell, чтобы вставить код.  Нажмите клавишу **ВВОД**.
            - На портале Azure выберите созданный файл **_verifyCert4.pem_**. Нажмите кнопку **Проверка**.

            ![Проверка сертификата](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Выполните шаги по созданию сертификатов для устройства и удалению ресурсов.

    > [!NOTE]
    > Создавая сертификаты устройств убедитесь, что в имени устройства используются только строчные буквы, цифры и дефисы.
    >


## <a name="create-a-device-enrollment-entry"></a>Создание записи о регистрации устройства

1. Откройте окно командной строки. Клонируйте репозиторий GitHub для образцов кода пакета SDK для Java:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. В загруженном исходном коде найдите папку **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Откройте файл **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** в любом удобном текстовом редакторе и добавьте следующие данные:

    1. В службу подготовки добавьте `[Provisioning Connection String]` с портала следующим образом:

        1. Откройте службу подготовки на [портале Azure](https://portal.azure.com). 

        1. Откройте раздел **Политики общего доступа** и выберите политику с разрешением *EnrollmentWrite*.
    
        1. Скопируйте **строку подключения первичного ключа**. 

            ![Получение с портала строки подключения для подготовки](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. В файле с примером кода **_ServiceEnrollmentGroupSample.java_** замените `[Provisioning Connection String]` значением **строки подключения первичного ключа**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Откройте файл **_RootCA.pem_** в текстовом редакторе. Присвойте значение **корневого сертификата** параметру **PUBLIC_KEY_CERTIFICATE_STRING**, как показано ниже:

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```
 
    1. На [портале Azure](https://portal.azure.com) откройте Центр Интернета вещей, связанный с используемой службой подготовки. Откройте для этого центра вкладку **Обзор** и скопируйте значение **Hostname**. Присвойте значение **Hostname** параметру *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Изучите пример кода. Этот код создает, обновляет, удаляет группу регистрации для устройств X.509 и выполняет запросы к ней. Чтобы проверить успешность регистрации на портале, временно закомментируйте в конце файла _ServiceEnrollmentGroupSample.java_ следующие строки кода:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Сохраните файл _ServiceEnrollmentGroupSample.java_. 

1. Откройте окно командной строки и перейдите к папке **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Соберите пример кода с помощью следующей команды:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Запустите пример кода, выполнив в окне командной строки следующие команды:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Удостоверьтесь, что регистрация прошла успешно, просмотрев данные в окне вывода.

    ![Регистрация прошла успешно](./media/tutorial-group-enrollments/enrollment.png) 

1. Откройте службу подготовки на портале Azure. Щелкните **Управление регистрациями**. Убедитесь, что на вкладке **Группы регистрации** появилась группа устройств X.509 с автоматически созданным *именем группы*. 


## <a name="simulate-the-device"></a>Имитация устройства

1. В колонке сводной информации о службе подготовки устройств выберите **Обзор** и запишите указанные там значения _Область идентификатора_ и _Provisioning Service Global Endpoint_ (Глобальная конечная точка службы подготовки).

    ![Сведения о службе](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Откройте окно командной строки. Перейдите к папке с примером проекта.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Введите сведения о группе регистрации следующим образом:

    - Измените `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, добавив значения параметров _Область идентификатора_ и _Provisioning Service Global Endpoint_ (Глобальная конечная точка службы подготовки), как было указано ранее. Откройте файл **_{deviceName}-public.pem_** и внесите это значение как _сертификат клиента_. Откройте файл **_{deviceName}-all.pem_** и скопируйте текст от строки _-----BEGIN PRIVATE KEY-----_ по строку _-----END PRIVATE KEY-----_.  Используйте скопированный фрагмент в качестве _клиентского сертификата закрытого ключа_.

        ```java
        private static final String idScope = "[Your ID scope here]";
        private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
        private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
        private static final String leafPublicPem = "<Your Public PEM Certificate here>";
        private static final String leafPrivateKey = "<Your Private PEM Key here>";
        ```

        - Используйте для сертификата и ключа следующий формат:
            
            ```java
            private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
            private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXX\n" +
                "-----END PRIVATE KEY-----\n";
            ```

1. Выполните сборку примера. Перейдите к целевой папке и запустите созданный JAR-файл.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Успешная регистрация](./media/tutorial-group-enrollments/registration.png)

1. На портале перейдите в Центр Интернета вещей, связанный со службой подготовки, и откройте колонку **Обозреватель устройств**. Когда имитированное устройство X.509 будет успешно подготовлено для центра, в колонке **Обозреватель устройств** появится идентификатор этого устройства со значением **Включено** в столбце *Состояние*. Обратите внимание, что может потребоваться нажать кнопку **Обновить** в верхней части окна, если вы уже открывали колонку, прежде чем запустить пример приложения для устройства. 

    ![Устройство зарегистрировано в Центре Интернета вещей](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с примером клиентского устройства, не удаляйте ресурсы, созданные в ходе работы с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера клиентского устройства на компьютере.
1. В меню слева на портале Azure щелкните **Все ресурсы** и откройте службу подготовки устройств. Откройте колонку **Управление регистрациями** для службы, затем откройте вкладку **Индивидуальные регистрации**. Выберите *идентификатор регистрации* устройства, которое вы зарегистрировали в процессе работы с этим руководством, и нажмите кнопку **Удалить** вверху. 
1. В меню слева на портале Azure нажмите кнопку **Все ресурсы** и выберите свой Центр Интернета вещей. Откройте колонку **Устройства Интернета вещей** для нужного концентратора, выберите *идентификатор устройства*, зарегистрированного в процессе работы с руководством, и нажмите кнопку **Удалить** вверху.


## <a name="next-steps"></a>Дополнительная информация

В ходе работы с этим руководством вы создали имитированное устройство X.509 на компьютере с ОС Windows и подготовили его для Центра Интернета вещей с помощью службы подготовки устройств Центра Интернета вещей и групп регистрации. Дополнительные сведения об устройстве X.509 см. в статье с основными понятиями. 

> [!div class="nextstepaction"]
> [Понятия устройства в контексте подготовки устройств в Центре Интернета вещей](concepts-device.md)

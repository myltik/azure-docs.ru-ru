---
title: "Регистрация устройств X.509 в службе подготовки устройств Azure с помощью Java | Документация Майкрософт"
description: "Краткое руководство по Azure. Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей Azure с помощью пакета SDK для службы Java"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f05524f9ec70fea243ab6cb3e87a70e762b0fa48
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей с помощью пакета SDK для службы Java
> [!div class="op_single_selector"]
> * [Java](quick-enroll-device-x509-java.md)
> * [Node.js](quick-enroll-device-x509-node.md)

В этой статье на примере приложения Java показано, как программным способом зарегистрировать группу имитированных устройств X.509 в службе подготовки устройств Центра Интернета вещей Azure с помощью [пакета SDK для службы Java](https://azure.github.io/azure-iot-sdk-java/service/). В этой статье процесс регистрации описан на примере компьютера под управлением Windows. Но пакет SDK для службы Java также успешно работает на Linux.

Прежде чем продолжить, [настройте службу подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки 

1. Убедитесь, что на вашем компьютере установлен [пакет SDK 8 для Java SE](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). 

2. Настройте переменные среды для установки Java. Переменная `PATH` должна содержать полный путь к каталогу *jdk1.8.x\bin*. Если это первая установка Java на этом компьютере, создайте новую переменную среды с именем `JAVA_HOME` и сохраните в ней полный путь к каталогу *jdk1.8.x*. На компьютере с ОС Windows этот каталог обычно находится в папке *C:\\Program Files\\Java\\*. Для создания и редактирования переменных среды вам нужно выполнить поиск по строке **Изменение переменных среды** на **панели управления**. 

  Чтобы проверить, правильно ли настроена среда Java на компьютере, выполните в окне командной строки следующую команду:

    ```cmd\sh
    java -version
    ```

3. Скачайте и разверните на компьютере [Maven 3](https://maven.apache.org/download.cgi). 

4. Измените переменную среды `PATH`, чтобы она указывала на папку *apache-maven-3.x.x\\bin* в папке, куда вы извлекли Maven. Чтобы убедиться, что средство Maven успешно установлено, выполните в окне командной строки следующую команду:

    ```cmd\sh
    mvn --version
    ```

5. Обязательно установите на компьютер систему [git](https://git-scm.com/download/) и добавьте ее в переменную среды `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Скачивание и изменение примера кода на Java

В этом разделе показано, как добавить в пример кода сведения о подготовке устройства X.509. 

1. Откройте окно командной строки. Клонируйте из репозитория GitHub пример кода для регистрации устройства, используя пакет SDK для службы Java:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. В загруженном исходном коде найдите папку **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Откройте файл **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** в любом удобном текстовом редакторе и добавьте следующие данные:

    1. В службу подготовки добавьте `[Provisioning Connection String]` с портала следующим образом:
        1. Откройте службу подготовки на [портале Azure](https://portal.azure.com). 
        2. Откройте раздел **Политики общего доступа** и выберите политику с разрешением *EnrollmentWrite*.
        3. Скопируйте **строку подключения первичного ключа**. 

            ![Получение с портала строки подключения для подготовки](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. В файле с примером кода **_ServiceEnrollmentGroupSample.java_** замените `[Provisioning Connection String]` значением **строки подключения первичного ключа**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Добавьте корневой сертификат для группы устройств. Если вам нужен пример корневого сертификата, примените _генератор сертификатов X.509_ следующим образом:
        1. В окне командной строки перейдите в папку  **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_**.
        2. Выполните сборку средства с помощью этой команды:

                ```cmd\sh
                mvn clean install
                ```

        4. Запустите средство с помощью таких команд:

                ```cmd\sh
                cd target
                java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
                ```

        5. (Необязательно.) Введите _общее имя_ для сертификатов, когда появится соответствующий запрос.
        6. Это средство локально создает **сертификат клиента**, **закрытый ключ сертификата клиента** и **корневой сертификат**.
        7. Скопируйте полученный **корневой сертификат**, включая строки **_-----BEGIN CERTIFICATE-----_** и **_-----END CERTIFICATE-----_**. 
        8. Присвойте значение **корневого сертификата** параметру **PUBLIC_KEY_CERTIFICATE_STRING**, как показано ниже:

                ```Java
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

        9. Закройте окно командной строки или введите **n**, когда появится запрос *кода проверки*. 
 
    3. Также службу подготовки можно настроить с помощью этого примера кода:
        - Чтобы добавить эту конфигурацию в пример, выполните следующие действия:
            1. На [портале Azure](https://portal.azure.com) откройте Центр Интернета вещей, связанный с используемой службой подготовки. Откройте вкладку **Обзор** для этого центра и скопируйте значение **Hostname**. Присвойте значение **Hostname** параметру *IOTHUB_HOST_NAME*.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Укажите понятное имя в качестве значения параметра *DEVICE_ID*, а для параметра *PROVISIONING_STATUS* сохраните значение по умолчанию *ENABLED* (Включено). 

        - Вы также можете не настраивать службу подготовки. В этом случае закомментируйте или удалите в файле _ServiceEnrollmentGroupSample.java_ следующие инструкции:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Изучите пример кода. Этот код создает, обновляет, удаляет группу регистрации для устройств X.509 и выполняет запросы к ней. Чтобы проверить успешность регистрации на портале, временно закомментируйте в конце файла _ServiceEnrollmentGroupSample.java_ следующие строки кода:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Сохраните файл _ServiceEnrollmentGroupSample.java_. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Сборка и запуск примера для регистрации группы

1. Откройте окно командной строки и перейдите к папке **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

2. Соберите пример кода с помощью следующей команды:

    ```cmd\sh
    mvn install -DskipTests
    ```

   С помощью этой команды можно скачать на компьютер пакет Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client). Этот пакет содержит двоичные файлы пакета SDK для службы Java, который нужно собрать с помощью примера кода. Если в предыдущем разделе вы применяли _генератор сертификатов X.509_, этот пакет уже загружен на ваш компьютер. 

3. Запустите пример кода, выполнив в окне командной строки следующие команды:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. В окне вывода проследите, чтобы регистрация прошла успешно.

5. Откройте службу подготовки на портале Azure. Щелкните **Управление регистрациями**. Убедитесь, что на вкладке **Группы регистрации** появилась группа устройств X.509 с автоматически созданным *именем группы*. 

    ![Проверка успешной регистрации устройств X.509 на портале](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Изменения, позволяющие зарегистрировать одно устройство X.509

Чтобы зарегистрировать одно устройство X.509, отредактируйте пример кода для *отдельной регистрации*, который приводится в статье [Регистрация устройств TPM в службе подготовки устройств Центра Интернета вещей с помощью пакета SDK для службы Java](quick-enroll-device-tpm-java.md#javasample), следующим образом:

1. Скопируйте в буфер обмена *общее имя* сертификата клиента X.509. Если вы хотите использовать _генератор сертификатов X.509_, как описано [в предыдущем разделе](#javasample), введите _общее имя_ своего сертификата или имя по умолчанию **microsoftriotcore**. Это **общее имя** следует задать как значение переменной *REGISTRATION_ID*. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Переименуйте переменную *TPM_ENDORSEMENT_KEY* в *PUBLIC_KEY_CERTIFICATE_STRING*. Скопируйте сертификат клиента или значение **Client Cert** из выходных данных _генератора сертификатов X.509_ и задайте это значение для переменной *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
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
3. В функции **main** замените строку `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` следующим фрагментом, чтобы использовать сертификат клиента X.509:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Сохраните файла с примером кода *для отдельной регистрации*, выполните его сборку и запустите его, следуя инструкциям из раздела [о сборке и запуске примера кода для отдельной регистрации](quick-enroll-device-tpm-java.md#runjavasample).


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете изучить пример службы Java, не удаляйте ресурсы, созданные при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера Java, если оно открыто на компьютере.
1. Закройте окно _генератора сертификатов X.509_, если оно открыто на компьютере.
1. Перейдите к службе подготовки устройств на портале Azure, щелкните **Управление регистрациями** и выберите вкладку **Группы регистрации**. Выберите *имя группы* устройств X.509, которую вы зарегистрировали в процессе работы с этим руководством, и нажмите кнопку **Удалить** в верхней части колонки.  

## <a name="next-steps"></a>Дополнительная информация
В рамках работы с этим кратким руководством вы зарегистрировали группу имитированных устройств X.509 в службе подготовки устройств. Дополнительные сведения о подготовке устройств см. в руководстве по настройке службы подготовки устройств на портале Azure. 

> [!div class="nextstepaction"]
> [Руководства по службе подготовки устройств для Центра Интернета вещей Azure](./tutorial-set-up-cloud.md)

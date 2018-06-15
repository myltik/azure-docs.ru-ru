---
title: Подготовка имитированного устройства X.509 в Центре Интернета вещей Azure с помощью Java | Документация Майкрософт
description: Краткое руководство Azure. Создание и подготовка имитированного устройства X.509 с помощью пакета Java SDK для службы подготовки устройств Центра Интернета вещей
author: bryanla
ms.author: bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 1f7f044f46dded461d00d2ec78febf02271adcfa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631043"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Создание и подготовка имитированного устройства X.509 с помощью пакета Java SDK для службы подготовки устройств Центра Интернета вещей
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

В этом руководстве показано, как имитировать устройство X.509 на компьютере разработки под управлением ОС Windows, а также как с помощью примера кода подключить имитированное устройство к службе подготовки устройств и Центру Интернета вещей. 

Если вы не знакомы с процессом автоматической подготовки, обязательно прочтите статью [Принципы автоматической подготовки устройств](concepts-auto-provisioning.md). Кроме того, прежде чем продолжить, выполните инструкции по [настройке службы "Подготовка устройств к добавлению в Центр Интернета вещей" на портале Azure](./quick-setup-auto-provision.md). 

## <a name="prepare-the-environment"></a>Подготовка среды 

1. Убедитесь, что на вашем компьютере установлен [пакет SDK 8 для Java SE](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

2. Скачайте и установите [Maven](https://maven.apache.org/install.html).

3. Установите на компьютер систему Git и добавьте ее в переменные среды, доступные в командном окне. Последнюю версию средств `git` для установки, которая включает **Git Bash**, приложение командной строки для взаимодействия с локальным репозиторием Git, можно найти на [этой странице](https://git-scm.com/download/). 

4. Откройте окно командной строки. Клонируйте репозиторий GitHub для примера кода виртуального устройства:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Перейдите в корневой каталог `azure-iot-sdk-`java и создайте проект для скачивания всех необходимых пакетов.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Перейдите к проекту генератора сертификатов и выполните сборку этого проекта. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Создание самозаверяющего сертификата устройства X.509 и запись отдельной регистрации

1. С помощью командной строки перейдите к папке `target` и выполните созданный JAR-файл.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Введите **N** в ответ на запрос _Do you want to input common name_ (Хотите ли вы ввести общее имя). Скопируйте в буфер обмена выходные данные команды `Client Cert`, начиная со строки *-----BEGIN CERTIFICATE-----* и заканчивая строкой *-----END CERTIFICATE-----*.

   ![Генератор отдельного сертификата](./media/java-quick-create-simulated-device-x509/individual.png)

3. На компьютере с ОС Windows создайте файл **_X509individual.pem_**, откройте его в любом удобном редакторе и скопируйте в него содержимое буфера обмена. Сохраните файл и закройте редактор.

4. Введите в командной строке **N** в ответ на запрос _Do you want to input Verification Code_ (Хотите ли вы ввести код проверки?) и не закрывайте выходные данные программы, так как они понадобятся позже при работе с этим руководством. Позже вы скопируете значения `Client Cert` и `Client Cert Private Key` для использования в следующем разделе.

5. Войдите на [портал Azure](https://portal.azure.com), нажмите кнопку **Все ресурсы** в меню слева и откройте экземпляр службы подготовки устройств.

6. В колонке сводки службы подготовки устройств выберите **Управление регистрациями**. На вкладке **Отдельные регистрации** и нажмите кнопку **Добавить** сверху. 

7. На панели **Add enrollment** (Добавление регистрации) введите следующее:
    - Выберите **X.509** как *механизм* аттестации удостоверения.
    - В поле *PEM-файл или CER-файл первичного сертификата* щелкните *Выбрать файл*, чтобы выбрать созданный ранее файл сертификата **X509individual.pem**.  
    - При необходимости можно указать следующие сведения:
      - Выберите Центр Интернета вещей, связанный с вашей службой подготовки.
      - Укажите уникальный идентификатор устройства. Убедитесь, что при назначении имен устройства не используются конфиденциальные данные. 
      - Обновите **начальное состояние двойника устройства**, используя требуемую начальную конфигурацию для устройства.
   - Затем нажмите кнопку **Сохранить**. 

    [![Добавление индивидуальной регистрации для аттестации X.509 на портале](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     После успешной регистрации устройство X.509 отобразится как **microsoftriotcore** в столбце *Идентификатор регистрации* на вкладке *Индивидуальные регистрации*. 



## <a name="simulate-the-device"></a>Имитация устройства

1. В колонке сводной информации о службе подготовки устройств выберите **Обзор** и запишите указанные там значения _Область идентификатора_ и _Provisioning Service Global Endpoint_ (Глобальная конечная точка службы подготовки).

    ![Сведения о службе](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Откройте окно командной строки. Перейдите к папке с примером проекта в репозитории с пакетом SDK для Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Включите в код сведения о службе подготовки и удостоверении X.509. Они используются во время автоматической подготовки для аттестации имитированного устройства перед его регистрацией:

   - Измените файл `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, добавив значения _области идентификатора_ и _глобальной конечной точки службы подготовки_, как было указано ранее. Также добавьте в файл _сертификат клиента_ и _закрытый ключ сертификата клиента_, которые вы записали ранее.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - При копировании и вставке сертификата и закрытого ключа используйте следующий формат:
        
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

4. Выполните сборку примера. Перейдите к папке `target` и запустите созданный JAR-файл.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. На портале Azure перейдите в Центр Интернета вещей, связанный со службой подготовки, и откройте колонку **Обозреватель устройств**. Когда имитированное устройство X.509 будет успешно подготовлено для центра, в колонке **Device Explorer** появится идентификатор этого устройства со значением **Включено** в столбце *Состояние*.  Если вы уже открывали колонку, прежде чем запустить пример приложения для устройства, возможно, потребуется нажать кнопку **Обновить** вверху. 

    ![Устройство зарегистрировано в Центре Интернета вещей](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Если в записи регистрации для своего устройства вы изменили значение по умолчанию для *начального состояния двойника устройства*, требуемое состояние двойника будет извлечено из концентратора с последующим выполнением соответствующих действий. См. [общие сведения о двойниках устройств и их использовании в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с примером клиентского устройства, не удаляйте ресурсы, созданные в ходе работы с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера клиентского устройства на компьютере.
2. В меню слева на портале Azure щелкните **Все ресурсы** и откройте службу подготовки устройств. Откройте колонку **Управление регистрациями** для службы, затем откройте вкладку **Индивидуальные регистрации**. Выберите *идентификатор регистрации* устройства, которое вы зарегистрировали в процессе работы с этим руководством, и нажмите кнопку **Удалить** вверху. 
3. В меню слева на портале Azure нажмите кнопку **Все ресурсы** и выберите свой Центр Интернета вещей. Откройте колонку **Устройства Интернета вещей** для нужного концентратора, выберите *идентификатор устройства*, зарегистрированного в процессе работы с руководством, и нажмите кнопку **Удалить** вверху.


## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали имитированное устройство X.509 на компьютере Windows. Вы также настроили его регистрацию в службе "Подготовка устройств к добавлению в Центр Интернета вещей", а затем автоматически подготовили его для использования с Центром Интернета вещей. Чтобы узнать, как выполнить программную регистрацию устройства X.509, изучите соответствующее краткое руководство. 

> [!div class="nextstepaction"]
> [Краткое руководство Azure. Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей](quick-enroll-device-x509-java.md)

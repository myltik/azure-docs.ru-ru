---
title: Подготовка устройства с помощью службы подготовки устройств для Центра Интернета вещей Azure (.NET) | Документация Майкрософт
description: Подготовка устройства в одном Центре Интернета вещей с помощью службы подготовки устройств для Центра Интернета вещей Azure (.NET)
author: bryanla
ms.author: bryanla
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: e31c06268245946bd80e1328f4d7176f1e9a660f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629887"
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Регистрация устройства в Центре Интернета вещей с помощью клиента службы подготовки для Центра Интернета вещей Azure (.NET)

В предыдущем руководстве вы узнали, как настраивать устройство для подключения к службе подготовки устройств. В этом руководстве описывается, как использовать эту службу для подготовки устройства в одном Центре Интернета вещей с помощью как **_индивидуальной регистрации_**, так и **_групп регистраций_**. В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> * Регистрация устройства
> * запуск устройства;
> * проверка регистрации устройства.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем продолжить, настройте устройство и его *аппаратный модуль безопасности*, как описано в руководстве [Set up a device to provision using the Azure IoT Hub Device Provisioning Service](./tutorial-set-up-device.md) (Настройка устройства для подготовки с помощью службы подготовки устройств для Центра Интернета вещей Azure).

* Visual Studio 2015 или Visual Studio 2017

> [!NOTE]
> Visual Studio не требуется. Достаточно установить [.NET](https://www.microsoft.com/net), чтобы разработчики могли использовать предпочтительный редактор в ОС Windows или Linux.  

В этом руководстве моделируется добавление информации об устройстве в службу подготовки во время производства оборудования или сразу после этого. Обычно этот код выполняется на компьютере или на заводском устройстве, на котором можно запустить код .NET, который не добавляется на само устройство.


## <a name="enroll-the-device"></a>Регистрация устройства

Этот шаг подразумевает добавление уникальных артефактов безопасности устройства в службу подготовки устройств. Эти артефакты безопасности выглядят так:

- Для устройств на основе доверенного платформенного модуля (TPM):
    - *Ключ подтверждения*, который уникален для каждой микросхемы или имитации доверенного платформенного модуля. Дополнительные сведения см. в статье [Общие сведения о ключе подтверждения доверенного платформенного модуля](https://technet.microsoft.com/library/cc770443.aspx).
    - *Registration ID* (ИД регистрации), который позволяет уникально идентифицировать устройство в пространстве имен и (или) области. Он может как совпадать, так и не совпадать с идентификатором устройства. Идентификатор — обязателен для каждого устройства. Для устройств на основе доверенного платформенного модуля идентификатор регистрации может быть производным от самого TPM, например хэш SHA-256 ключа подтверждения доверенного платформенного модуля.

- Для устройств на основе X.509:
    - [Сертификат X.509 выдается устройству](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) в файле формата *PEM* или *CER*. Для отдельной регистрации необходимо использовать *конечный сертификат* для системы X.509, а для групп регистраций — *корневой сертификат* либо его эквивалент, *сертификат подписчика*.
    - *Registration ID* (ИД регистрации), который позволяет уникально идентифицировать устройство в пространстве имен и (или) области. Он может как совпадать, так и не совпадать с идентификатором устройства. Идентификатор — обязателен для каждого устройства. Для устройств на базе X.509 идентификатор регистрации является производным от общего имени сертификата (CN). Дополнительные сведения об этих требованиях см. в статье [Понятия устройства в контексте подготовки устройств в Центре Интернета вещей](https://docs.microsoft.com/azure/iot-dps/concepts-device).

Имеется два способа регистрации устройства в службе подготовки устройств:

- **Individual Enrollments** (Отдельные регистрации). Представляет собой запись для одного устройства, которое можно зарегистрировать с помощью службы подготовки устройств. В качестве механизмов аттестации отдельные регистрации могут использовать сертификаты X.509 или токены SAS (в реальном или виртуальном доверенном платформенном модуле). Советуем использовать отдельные регистрации для устройств, которым требуются уникальные начальные конфигурации, или для устройств, которые могут использовать только маркеры SAS через TPM в качестве механизма аттестации. Для отдельных регистраций можно указать нужный идентификатор устройства Центра Интернета вещей.

- **Enrollment Groups** (Группы регистраций). Представляет группу устройств, использующих определенный механизм аттестации. Мы советуем использовать группу регистраций для большого количества устройств, имеющих необходимую начальную конфигурацию, или для устройств, предназначенных для одного и того же клиента. Группы регистраций создаются только на базе сертификатов X.509 и используют сертификат для подписи в цепочке сертификатов X.509.

### <a name="enroll-the-device-using-individual-enrollments"></a>Индивидуальная регистрация устройства

1. С помощью шаблона проекта **Консольное приложение** создайте проект "Консольное приложение Visual C#" в Visual Studio. Назовите проект **DeviceProvisioning**.
    
1. В обозревателе решений щелкните правой кнопкой мыши проект **DeviceProvisioning** и выберите пункт **Manage NuGet Packages…** (Управление пакетами NuGet...).

1. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **microsoft.azure.devices.client**. Примите условия использования, затем выберите запись и щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Provisioning.Service**. В результате выполняется скачивание и установка пакета NuGet [SDK для службы подготовки устройств Azure Интернета вещей](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения DPS, записанной в предыдущем разделе.
   
    ```csharp
    static readonly string ServiceConnectionString = "{DPS connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Добавьте следующий код, чтобы реализовать регистрацию для устройства:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и начать регистрацию.
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Выберите пункт **Один запускаемый проект**, а затем в раскрывающемся меню выберите проект **DeviceProvisioning**.  

1. Запустите приложение для устройства .NET **DeviceProvisiong**. Оно должно настроить подготовку устройства. 

    ![Выполнение отдельной регистрации](./media/tutorial-net-provision-device-to-hub/individual.png)

При успешной регистрации устройства вы увидите на портале следующее:

   ![Успешная регистрация на портале](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Регистрация устройства с помощью группы регистраций

> [!NOTE]
> Для примера группы регистрации требуется сертификат X.509.

1. В обозревателе решений Visual Studio откройте проект **DeviceProvisioning**, созданный выше. 

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя на расположение сертификата X509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Добавьте следующий код в файл **Program.cs**, чтобы реализовать групповую регистрацию:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и начать групповую регистрацию.
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Запустите приложение для устройства .NET **DeviceProvisiong**. Оно должно настроить группы подготовки устройства. 

    ![Выполнение регистрации группы](./media/tutorial-net-provision-device-to-hub/group.png)

    При успешной регистрации группы устройств на портале можно увидеть следующее:

   ![Успешная регистрация группы на портале](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>запуск устройства;

На этом этапе для регистрации устройства готова следующая настройка:

1. Устройство или группа устройств регистрируется в службе подготовки устройств. 
2. Устройство готово и доступно через приложение, которое использует клиентский пакет SDK службы подготовки устройств.

Запустите устройство, чтобы разрешить клиентскому приложению начать регистрацию в службе подготовки устройств.  


## <a name="verify-the-device-is-registered"></a>проверка регистрации устройства.

После загрузки устройства необходимо выполнить следующие действия. Дополнительные сведения о примере приложения имитации доверенного платформенного модуля (TPM) см. на странице [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c). 

1. Устройство отправляет запрос на регистрацию в службу подготовки устройств.
2. Устройствам с доверенным платформенным модулем служба подготовки устройств отправляет обратный запрос регистрации, на который отвечает устройство. 
3. При успешной регистрации служба подготовки устройств отправляет обратно к устройству универсальный код ресурса Центра Интернета вещей, идентификатор устройства и зашифрованный ключ. 
4. Затем клиентское приложение Центра Интернета вещей на устройстве подключается к центру. 
5. При успешном подключении к центру устройство отобразится в средстве **Device Explorer** Центра Интернета вещей. 

    ![Успешное подключение к центру на портале](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Регистрация устройства
> * запуск устройства;
> * проверка регистрации устройства.

Перейдите к следующему руководству, чтобы узнать, как подготовить несколько устройств через концентраторы с балансировкой нагрузки. 

> [!div class="nextstepaction"]
> [Подготовка устройств в Центрах Интернета вещей с балансировкой нагрузки](./tutorial-provision-multiple-hubs.md)

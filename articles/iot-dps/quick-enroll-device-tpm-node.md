---
title: Регистрация устройств TPM в службе подготовки устройств Azure с помощью Node.js | Документация Майкрософт
description: Краткое руководство по Azure. Регистрация устройств TPM в службе подготовки устройств Центра Интернета вещей Azure с помощью пакета SDK для службы Node.js
author: bryanla
ms.author: bryanla
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 0d7d36fc207b44163264adf62e6d3e29d43489b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629615"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Регистрация устройств TPM в службе подготовки устройств Центра Интернета вещей с помощью пакета SDK для службы Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


В этой статье показано, как программными средствами создать отдельную регистрацию устройства TPM в службе подготовки устройств Центра Интернета вещей Azure с помощью [пакета SDK для службы Node.js](https://github.com/Azure/azure-iot-sdk-node) и примера приложения Node.js. С помощью этой записи отдельной регистрации вы можете по мере необходимости регистрировать имитированные устройства TPM в службе подготовки. В этой статье описана работа с компьютером под управлением Windows, но эта процедура применима как для Windows, так и для Linux.

## <a name="prerequisites"></a>предварительным требованиям

- Прежде чем продолжить, выполните инструкции по [настройке службы подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md). 
-  Убедитесь, что на вашем компьютере установлена платформа [Node.js 4.0 или более поздней версии](https://nodejs.org).
- Если вы намерены зарегистрировать имитированное устройство в конце этого руководства, выполните действия из статьи [Создание и подготовка имитированного устройства с помощью службы подготовки устройств Центра Интернета вещей](quick-create-simulated-device.md) вплоть до шага, на котором вы получаете ключ подтверждения для устройства. Запишите этот ключ подтверждения. Он понадобится вам позже при работе с этим руководством. **Не выполняйте шаги по созданию отдельной регистрации с помощью портала Azure.**
 
## <a name="create-the-individual-enrollment-sample"></a>Создание примера для отдельной регистрации 

 
1. В окне командной строки перейдите в рабочую папку и выполните следующую команду:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. С помощью текстового редактора создайте а рабочей папке файл **create_individual_enrollment.js**. Добавьте в этот файл следующий код и сохраните файл:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ````

## <a name="run-the-individual-enrollment-sample"></a>Запуск примера отдельной регистрации
  
1. Чтобы запустить этот пример, вам потребуется строка подключения к службе подготовки. 
    1. Войдите на портал Azure, нажмите кнопку **Все ресурсы** в меню слева и откройте службу подготовки устройств. 
    2. Щелкните **Политики общего доступа**, а затем выберите нужную политику доступа, чтобы открыть ее свойства. В окне **Политика доступа** скопируйте и запишите строку подключения первичного ключа. 

    ![Получение строки подключения к службе подготовки на портале](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Вам также потребуется ключ подтверждения для устройства. Если вы создали имитированное устройство TPM с помощью краткого руководства [Создание и подготовка имитированного устройства с помощью службы подготовки устройств Центра Интернета вещей](quick-create-simulated-device.md), используйте созданный в нем ключ для устройства. В противном случае вы можете создать пример отдельной регистрации с помощью стандартного ключа подтверждения, который входит в пакет SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Чтобы создать отдельную регистрацию устройства TPM, выполните следующую команду (сохранив кавычки для аргументов команд):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. После успешного создания в окне командной строки отобразятся свойства новой отдельной регистрации.

    ![Свойства регистрации в выходных данных команды](./media/quick-enroll-device-tpm-node/output.png) 

4. Убедитесь, что отдельная регистрация успешно создана. На портале Azure в колонке сводки для службы подготовки устройств выберите **Управление регистрациями**. Выберите вкладку **Индивидуальные регистрации** и щелкните новую (*первую*) запись регистрации, чтобы проверить ключ подтверждения и другие свойства этой записи.

    ![Свойства регистрации на портале](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Итак, вы создали отдельную регистрацию для устройства TPM. Если вы хотите сразу зарегистрировать имитированное устройство, выполните оставшиеся шаги из руководства [Создание и подготовка имитированного устройства с помощью службы подготовки устройств Центра Интернета вещей](quick-create-simulated-device.md). Пропустите все шаги по созданию отдельной регистрации с помощью портала Azure, включенные в это руководство.

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете изучить пример службы Node.js, не удаляйте ресурсы, которые вы создали при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера Node.js, если оно открыто на компьютере.
1. Если вы создавали имитированное устройство TPM, закройте окно симулятора.
2. Перейдите к службе подготовки устройств на портале Azure, щелкните **Управление регистрациями** и выберите вкладку **Индивидуальные регистрации**. Выберите *идентификатор регистрации* для записи регистрации, которую вы создали в процессе работы с этим руководством, и нажмите кнопку **Удалить** в верхней части колонки. 
 
## <a name="next-steps"></a>Дополнительная информация
При работе с этим руководством вы программным способом создали запись отдельной регистрации для устройства TPM, а также (необязательно) создали на компьютере имитированное устройство TPM и подготовили его для Центра Интернета вещей с помощью службы подготовки устройств Центра Интернета вещей Azure. Дополнительные сведения о подготовке устройств см. в руководстве по настройке службы подготовки устройств на портале Azure. 
 
> [!div class="nextstepaction"]
> [Руководства по службе подготовки устройств для Центра Интернета вещей Azure](./tutorial-set-up-cloud.md)
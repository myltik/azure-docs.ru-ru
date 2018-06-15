---
title: Регистрация устройств X.509 в службе подготовки устройств Azure с помощью Node.js | Документация Майкрософт
description: Краткое руководство по Azure. Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей Azure с помощью пакета SDK для службы Node.js
author: bryanla
ms.author: bryanla
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 207dcc4651a9f3e3712ad67fe1718bcbcd715e27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629938"
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей с помощью пакета SDK для службы Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


Здесь описан процесс, позволяющий программными средствами создать группу регистрации для сертификатов X.509 промежуточных или корневых центров сертификации с помощью [пакета SDK для службы Node.js](https://github.com/Azure/azure-iot-sdk-node) и примера для Node.js. В этой статье описана работа с компьютером под управлением Windows, но эта процедура применима как для Windows, так и для Linux.
 

## <a name="prerequisites"></a>предварительным требованиям

- Выполните инструкции по [настройке службы подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md). 

 
- Убедитесь, что на вашем компьютере установлена платформа [Node.js 4.0 или более поздней версии](https://nodejs.org).


- Вам потребуется PEM-файл, содержащий сертификат X.509 промежуточного или корневого центра сертификации, который вы заранее отправили в службу подготовки и проверили в ней. **Пакет SDK для Центра Интернета вещей Azure** содержит средства, которые помогут вам создать цепочку сертификатов X.509, передать корневой или промежуточный сертификат из этой цепочки и подтвердить владение сертификатом в службе. Чтобы использовать это средство, клонируйте [пакет SDK для Центра Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c) и выполните на локальном компьютере инструкции, описанные в файле [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="create-the-enrollment-group-sample"></a>Создание примера группы регистрации 

 
1. В окне командной строки перейдите в рабочую папку и выполните следующую команду:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. С помощью текстового редактора создайте а рабочей папке файл **create_enrollment_group.js**. Добавьте в этот файл следующий код и сохраните файл:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>Запуск примера группы регистрации
 
1. Чтобы запустить этот пример, вам потребуется строка подключения к службе подготовки. 
    1. Войдите на портал Azure, нажмите кнопку **Все ресурсы** в меню слева и откройте службу подготовки устройств. 
    2. Щелкните **Политики общего доступа**, а затем выберите нужную политику доступа, чтобы открыть ее свойства. В окне **Политика доступа** скопируйте и запишите строку подключения первичного ключа. 

    ![Получение строки подключения к службе подготовки на портале](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Как описано в разделе [Предварительные требования](#prerequisites), вам потребуется PEM-файл, содержащий сертификат X.509 промежуточного или корневого центра сертификации, который вы заранее отправили в службу подготовки и проверили в ней. Чтобы убедиться, что сертификат уже передан и проверен, щелкните элемент **Сертификаты** на странице сводной информации о службе подготовки устройств на портале Azure. Найдите сертификат, который вы намерены применить для группы регистрации и убедитесь, что он имеет статус *Проверен*.

    ![Проверенный сертификат на портале](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Чтобы создать группу регистрации для сертификата, выполните следующую команду (сохранив кавычки для аргументов командной строки):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. После успешного создания в окне командной строки отобразятся свойства новой группы регистрации.

    ![Свойства регистрации в выходных данных команды](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Убедитесь, что группа регистрации успешно создана. На портале Azure в колонке сводки для службы подготовки устройств выберите **Управление регистрациями**. Выберите **Группы регистрации** и убедитесь, что появилась новая запись регистрации (*первая в списке*).

    ![Свойства регистрации на портале](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете изучить пример службы Node.js, не удаляйте ресурсы, которые вы создали при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы Azure.
 
1. Закройте окно выходных данных примера Node.js, если оно открыто на компьютере.
2. Перейдите к службе подготовки устройств на портале Azure, щелкните **Управление регистрациями** и выберите вкладку **Группы регистрации**. Выберите *Идентификатор регистрации* для записи регистрации, которую вы создали в процессе работы с этим руководством, и нажмите кнопку **Удалить** в верхней части колонки.  
3. На странице службы подготовки устройств на портале Azure щелкните **Сертификаты**, выберите переданный для работы с руководством сертификат, а затем нажмите кнопку **Удалить** в верхней части окна  **Сведения о сертификате**.  
 
## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве вы создали группу регистрации для сертификата X.509 промежуточного или корневого центра сертификации с помощью службы подготовки устройств Центра Интернета вещей Azure. Дополнительные сведения о подготовке устройств см. в руководстве по настройке службы подготовки устройств на портале Azure. 
 
> [!div class="nextstepaction"]
> [Руководства по службе подготовки устройств для Центра Интернета вещей Azure](./tutorial-set-up-cloud.md)
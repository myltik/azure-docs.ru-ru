---
title: Регистрация устройств X.509 в службе подготовки устройств Azure с помощью Python | Документация Майкрософт
description: Краткое руководство по Azure. Регистрация устройств X.509 в службе подготовки устройств Центра Интернета вещей Azure с помощью пакета SDK для службы подготовки Python
author: bryanla
ms.author: bryanla
ms.date: 01/25/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: c677bece27d011c5618845d950dd87e5b0e6bd06
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629343"
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Регистрация устройств X.509 в службе подготовки устройств к добавлению в Центр Интернета вещей с помощью пакета SDK для службы подготовки Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

В этой статье на примере приложения Python показано, как программным способом зарегистрировать группу имитированных устройств X.509 в службе подготовки устройств Центра Интернета вещей Azure с помощью [пакета SDK для службы подготовки Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client). В этой статье процесс регистрации описан на примере компьютера под управлением Windows. Но пакет SDK для службы Java также успешно работает на Linux.

Прежде чем продолжить, [настройте службу подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md).

> [!NOTE]
> В этом кратком руководстве поддерживается только **группа регистраций**. **Индивидуальная регистрация** через _пакет SDK службы подготовки Python_ сейчас находится на стадии разработки.
> 

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Подготовка среды 

1. Скачайте и установите [Python 2.x или 3.x](https://www.python.org/downloads/). Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. 

1. Выберите один из следующих вариантов:

    - Создайте и скомпилируйте **пакет SDK для Azure IoT Python**. Следуйте [этим инструкциям](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) по созданию пакетов Python. Если вы работаете с ОС Windows, также установите [распространяемый пакет Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145), чтобы разрешить использовать собственные библиотеки DLL из Python.

    - [Установите или обновите *pip*, систему управления пакетами Python](https://pip.pypa.io/en/stable/installing/) и установите пакет с помощью следующей команды.

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Вам потребуется PEM-файл, содержащий сертификат X.509 промежуточного или корневого центра сертификации, который вы заранее отправили в службу подготовки и проверили в ней. **Пакет SDK для Центра Интернета вещей Azure** содержит средства, с помощью которых можно создать цепочку сертификатов X.509, передать корневой или промежуточный сертификат из этой цепочки и подтвердить владение сертификатом в службе. Чтобы использовать это средство, клонируйте [пакет SDK для Центра Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c) и выполните на локальном компьютере инструкции, описанные в файле [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


## <a name="modify-the-python-sample-code"></a>Изменение примера кода Python

В этом разделе показано, как добавить в пример кода сведения о подготовке устройства X.509. 

1. В текстовом редакторе создайте файл **EnrollmentGroup.py**.

1. Добавьте следующие инструкции `import` и переменные в начало файла **EnrollmentGroup.py**: Затем замените `dpsConnectionString` вашей строкой подключения, которая находится в разделе **Политики общего доступа** в **службе подготовки устройств** на **портале Azure**. Замените заполнитель сертификатов на сертификат, созданный ранее в разделе [Подготовка среды](quick-enroll-device-x509-python.md#prepareenvironment). Наконец, создайте уникальный идентификатор `registrationid` и убедитесь, что он состоит только из алфавитно-цифровых символов нижнего регистра и дефисов.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Добавьте следующую функцию и ее вызов, чтобы реализовать создание групповой регистрации:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Сохраните и закройте файл **EnrollmentGroup.py**.
 

## <a name="run-the-sample-group-enrollment"></a>Выполнение примера для регистрации группы

1. Откройте командную строку и выполните скрипт.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

1. Удостоверьтесь, что регистрация прошла успешно, просмотрев выходные данные.

1. Откройте службу подготовки на портале Azure. Щелкните **Управление регистрациями**. Убедитесь, что на вкладке **Группы регистрации** появилась созданная ранее группа устройств X.509 с именем `registrationid`. 

    ![Проверка успешной регистрации устройств X.509 на портале](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете изучить пример службы Java, не удаляйте ресурсы, которые создали при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера Java, если оно открыто на компьютере.
1. Закройте окно _генератора сертификатов X.509_, если оно открыто на компьютере.
1. Перейдите к службе подготовки устройств на портале Azure, щелкните **Управление регистрациями** и выберите вкладку **Группы регистрации**. Выберите *имя группы* устройств X.509, которую вы зарегистрировали в процессе работы с этим руководством, и нажмите кнопку **Удалить** в верхней части колонки.  


## <a name="next-steps"></a>Дополнительная информация
В рамках работы с этим кратким руководством вы зарегистрировали группу имитированных устройств X.509 в службе подготовки устройств. Дополнительные сведения о подготовке устройств см. в руководстве по настройке службы подготовки устройств на портале Azure. 

> [!div class="nextstepaction"]
> [Руководства по службе подготовки устройств для Центра Интернета вещей Azure](./tutorial-set-up-cloud.md)

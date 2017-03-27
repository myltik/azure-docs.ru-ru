---
title: "Azure IoT Suite и приложения логики | Документация Майкрософт"
description: "Руководство по подключению приложений логики к Azure IoT Suite для бизнес-процесса."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 5e3221395082513f842863615d40f7d3ebf2562e
ms.lasthandoff: 03/10/2017


---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Руководство. Подключение приложения логики к предварительно настроенному решению для удаленного мониторинга Azure IoT Suite
Предварительно настроенное решение для удаленного мониторинга [Microsoft Azure IoT Suite][lnk-internetofthings] позволяет быстро начать работу с полным набором возможностей, которыми обладает решение Интернета вещей. В этом учебнике описаны действия по добавлению приложения логики к предварительно настроенному решению для удаленного мониторинга Microsoft Azure IoT Suite. Эти шаги показывают, как продвинуть свое решение IoT еще дальше, подключив его к бизнес-процессу.

*Если вы ищете пошаговое руководство по подготовке предварительно настроенного решения для удаленного мониторинга, обратитесь к статье [Учебник: начало работы с предварительно настроенными решениями][lnk-getstarted].*

Перед началом работы с данным учебником необходимо выполнить описанные ниже действия.

* Подготовьте в своей подписке Azure предварительно настроенное решение для удаленного мониторинга.
* Создайте учетную запись SendGrid для отправки сообщений электронной почты, которые будут запускать бизнес-процесс. Бесплатную пробную учетную запись можно создать на сайте [SendGrid](https://sendgrid.com/) , нажав кнопку **Try for Free**(Попробовать бесплатно). После регистрации бесплатной пробной учетной записи необходимо создать в SendGrid [ключ API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) , который предоставляет разрешения на отправку электронной почты. Ключ API используется далее в этом учебнике.

Для работы с этим руководством вам понадобится Visual Studio 2015 или Visual Studio 2017, чтобы изменять действия на серверной стороне предварительно настроенных решений.

Если вы уже подготовили предварительно настроенное решение для удаленного мониторинга, то перейдите к группе ресурсов для этого решения на [портале Azure][lnk-azureportal]. Имя группы ресурсов совпадает с именем решения, которое было выбрано при подготовке решения для удаленного мониторинга. В группе ресурсов можно просмотреть все подготовленные ресурсы Azure для вашего решения (за исключением приложения Azure Active Directory, которое можно найти на классическом портале Azure). На следующем рисунке показан пример колонки **Группа ресурсов** для предварительного настроенного решения для удаленного мониторинга:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Для начала настройте приложение логики, которое будет использоваться с предварительно настроенным решением.

## <a name="set-up-the-logic-app"></a>Настройка приложения логики
1. Щелкните **Добавить** в верхней части колонки группы ресурсов на портале Azure.
2. Найдите **Приложение логики**, выберите его и нажмите кнопку **Создать**.
3. Заполните поле **Имя**. Затем укажите те же подписку и группу ресурсов в полях **Подписка** и **Группа ресурсов**, которые использовались при подготовке решения для удаленного мониторинга. Щелкните **Создать**.
   
    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)
4. После завершения развертывания приложение логики отобразится в качестве ресурса в вашей группе ресурсов.
5. Щелкните приложение логики, чтобы перейти к колонке "Приложение логики". Выберите шаблон **Blank Logic App** (Пустое приложение логики). При этом откроется **Конструктор Logic Apps**.
   
    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)
6. Выберите **Запрос**. Это действие указывает, что в качестве триггера выступает входящий HTTP-запрос с полезными данными в определенном формате JSON.
7. Вставьте следующий код в поле "Схема JSON текста запроса":
   
    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Примечание. Сохранив приложение логики, можно скопировать URL-адрес метода HTTP POST, но сначала необходимо добавить действие.
   > 
   > 
8. Щелкните **+ Новый шаг** под своим триггером. Затем выберите **Добавить действие**.
   
    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)
9. Найдите действие **SendGrid - Send email** (SendGrid — отправить письмо) и щелкните его.
   
    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)
10. Введите имя подключения, например **SendGridConnection**, в соответствующем поле укажите **ключ API SendGrid**, созданный при настройке учетной записи SendGrid, и нажмите кнопку **Создать**.
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)
11. Добавьте свои адреса электронной почты в поля **From** (От) и **To** (Кому). В поле **Тема** добавьте **Remote monitoring alert [DeviceId]** (Оповещение удаленного мониторинга [DeviceId]). В поле **Текст сообщения электронной почты** укажите **Device [DeviceId] has reported [measurementName] with value [measuredValue]** (Устройство [DeviceId] передало значение [measuredValue] для измерения [measurementName]). Параметры **[DeviceId]**, **[measurementName]** и **[measuredValue]** можно добавить с помощью раздела **Можно вставить данные из предыдущих шагов**.
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)
12. Щелкните **Сохранить** в верхнем меню.
13. Щелкните триггер **Запрос** и скопируйте значение **HTTP POST на этот URL-адрес**. Этот URL-адрес потребуется далее в этом учебнике.

> [!NOTE]
> Приложения логики позволяют запускать [множество действий различных типов][lnk-logic-apps-actions], в том числе действия в Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Настройка веб-задания EventProcessor
В этом разделе вы подключите предварительно настроенное решение к созданному приложению логики. Чтобы выполнить эту задачу, необходимо добавить URL-адрес для вызова приложения логики к действию, которое запускается, когда значение датчика устройства превышает пороговое значение.

1. С помощью клиента git клонируйте последнюю версию [репозитория github azure-iot-remote-monitoring][lnk-rmgithub]. Например:
   
    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. В Visual Studio откройте файл **RemoteMonitoring.sln** из локальной копии репозитория.
3. Откройте файл **ActionRepository.cs** в папке **Infrastructure\\Repository**.
4. Измените словарь **actionIds**, указав в нем значение **HTTP POST на этот URL-адрес**, записанное из приложения логики.
   
    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Сохраните изменения в решении и закройте Visual Studio.

## <a name="deploy-from-the-command-line"></a>Развертывание из командной строки
В этом разделе вы развернете обновленную версию решения для удаленного мониторинга, заменив ту версию, которая в настоящее время работает в Azure.

1. Следуйте инструкциям [по настройке для разработчиков][lnk-devsetup], чтобы настроить свою среду для развертывания.
2. Для локального развертывания следуйте инструкциям [по локальному развертыванию][lnk-localdeploy].
3. Для развертывания в облако с обновлением существующего развертывания в облаке следуйте [этим][lnk-clouddeploy] инструкциям. В качестве имени развертывания используйте имя исходного развертывания. Например, если исходное развертывание называлось **demologicapp**, используйте следующую команду:
   
   ``
   build.cmd cloud release demologicapp
   ``
   
   При запуске сценария сборки обязательно используйте те же учетную запись, подписку, регион Azure и экземпляр Active Directory, которые применялись при подготовке решения.

## <a name="see-your-logic-app-in-action"></a>Проверка приложения логики в действии
При подготовке предварительно настроенного решения для удаленного мониторинга в нем по умолчанию настроены два правила. Оба правила применяются к устройству **SampleDevice001** :

* Температура > 38,00
* Влажность > 48,00

Правило для температуры активирует действие **Raise Alarm**, а правило для влажности — действие **SendMessage**. При условии, что для обоих действий в классе **ActionRepository** используется один и тот же URL-адрес, приложение логики срабатывает для каждого правила. Оба правила используют SendGrid для отправки электронной почты с подробными сведениями о предупреждении на адрес, указанный в поле **Кому** .

> [!NOTE]
> Приложение логики будет срабатывать при каждом превышении порогового значения. Чтобы избежать получения ненужных электронных сообщений, можно отключить правила на портале решения или приложение логики на [портале Azure][lnk-azureportal].
> 
> 

Наряду с получением электронных писем вы также можете наблюдать за работой приложения логики на портале:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь когда вы использовали приложение логики для подключения предварительно настроенного решения к бизнес-процессу, можете ознакомиться с дополнительными сведениями о настройке таких решений:

* [Использование динамической телеметрии с предварительно настроенным решением для удаленного мониторинга][lnk-dynamic]
* [Метаданные сведений об устройстве в предварительно настроенном решении для удаленного мониторинга][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md


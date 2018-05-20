---
title: Создание блокчейн-приложения в Azure Blockchain Workbench
description: Сведения о создании блокчейн-приложения в Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/9/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 485f761e22871396dace71333868ba7712e41f67
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Создание блокчейн-приложения в Azure Blockchain Workbench

Вы можете использовать Azure Blockchain Workbench для создания блокчейн-приложений, которые представляют собой многосторонние рабочие процессы, определенные конфигурацией и кодом интеллектуального контракта.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * настроить блокчейн-приложение;
> * создать файл с кодом интеллектуального контракта;
> * добавить блокчейн-приложение в Blockchain Workbench;
> * добавить участников в блокчейн-приложение.

## <a name="prerequisites"></a>предварительным требованиям

* Развертывание Blockchain Workbench. Дополнительные сведения о развертывании Azure Blockchain Workbench см. в [этой](blockchain-workbench-deploy.md) статье.
* Пользователи Azure Active Directory в клиенте, связанном с Blockchain Workbench. Дополнительные сведения см. в разделе о [добавлении пользователей Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Учетная запись администратора Blockchain Workbench. Дополнительные сведения см. в разделе об [управлении администраторами Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

Давайте создадим базовое приложение, в котором один пользователь отправляет запрос, а второй дает на него ответ. Например, на запрос "Привет, как дела?" поступает ответ "Отлично!". Запрос и ответ записываются в базовый блокчейн. 

Выполните шаги, чтобы создать файл приложения, или же [загрузите пример на сайте GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Файл конфигурации

Метаданные конфигурации определяют высокоуровневые рабочие процессы и модель взаимодействия блокчейн-приложения. Метаданные конфигурации представляют собой этапы рабочего процесса и модель взаимодействия блокчейн-приложения.

1. В любом редакторе создайте файл с именем `HelloBlockchain.json`.
2. Добавьте следующий код JSON, чтобы определить конфигурацию блокчейн-приложения.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Сохраните файл `HelloBlockchain.json`.

Файл конфигурации содержит несколько разделов. Подробная информация о каждом разделе приведена ниже.

### <a name="application-metadata"></a>Метаданные приложения

В начале файла конфигурации содержится информация о приложении, включая его имя и описание.

### <a name="application-roles"></a>Роли приложений

В разделе ролей приложения определяются роли пользователей, которые могут выполнять действия или быть участниками в блокчейн-приложении. Вы определяете набор различных ролей на основе функций. В сценарии запроса и ответа есть различие между функциями запрашивающей стороны как сущности, которая создает запросы, и респондента как сущности, которая предоставляет ответы.

### <a name="workflows"></a>Рабочие процессы

Рабочие процессы определяют один или несколько этапов и действий контракта. В сценарии запроса и ответа первый этап (состояние) рабочего процесса такой: источник запроса (роль) выполняет действие (переход) для отправки запроса (функция). Следующий этап (состояние): респондент (роль) выполняет действие (переход) для отправки ответа (функция). Рабочий процесс приложения может включать в себя свойства, функции и состояния, необходимые для описания потока контракта. 

Дополнительные сведения о содержимом файлов конфигурации см. в статье [справочных сведений о конфигурации рабочего процесса Azure Blockchain](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>Файл с кодом интеллектуального контракта

Интеллектуальные контракты представляют собой бизнес-логику блокчейн-приложения. В настоящее время Blockchain Workbench поддерживает сеть Ethereum для реестра блокчейна. Сеть Ethereum использует [Solidity](https://solidity.readthedocs.io) как язык программирования для написания самореализуемой бизнес-логики для интеллектуальных контрактов.

Интеллектуальные контракты в Solidity аналогичны классам в объектно-ориентированных языках. Каждый контракт содержит состояние и функции для реализации этапов и действий интеллектуального контракта.

В любом редакторе создайте файл с именем `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Директива версии

Рекомендуется указать версию Solidity, которую вы собираетесь использовать. Указание версии помогает избежать проблем с совместимостью с будущими версиями Solidity. 

Добавьте следующую директиву версии в верхнюю область файла с кодом интеллектуального контракта `HelloBlockchain.sol`.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Базовый класс

Базовый класс **WorkbenchBase** позволяет Blockchain Workbench создавать обновление контракта. Этот класс является обязательным для конкретного кода интеллектуального контракта Blockchain Workbench. Контракт должен наследовать значения от базового класса **WorkbenchBase**.

В файл кода интеллектуального контракта `HelloBlockchain.sol` добавьте класс **WorkbenchBase** в начале файла. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
Базовый класс включает две важные функции:

|Функция базового класса  | Назначение  | Когда вызывать  |
|---------|---------|---------|
| ContractCreated() | Уведомляет Blockchain Workbench о создании контракта | Перед выходом из конструктора контракта |
| ContractUpdated() | Уведомляет Blockchain Workbench об обновлении состояния контракта | Перед выходом из функции контракта |

### <a name="configuration-and-smart-contract-code-relationship"></a>Связь между конфигурацией и кодом интеллектуального контракта

Blockchain Workbench использует файл конфигурации и файл с кодом интеллектуального контракта для создания блокчейн-приложения. Между конфигурацией и кодом интеллектуального контракта есть определенная связь. Для создания приложения необходимо сопоставить подробные сведения о контракте, функции, параметры и типы. Перед созданием приложения Blockchain Workbench проверяет файлы. 

### <a name="contract"></a>Контракт

Для Blockchain Workbench контракты должны наследовать значения от базового класса **WorkbenchBase**. При объявлении контракта вам необходимо передать имя приложения и рабочего процесса в качестве аргументов.

Добавьте заголовок **contract** в файл кода интеллектуального контракта `HelloBlockchain.sol`. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Контракт должен наследовать значения от базового класса **WorkbenchBase** и передавать параметры **ApplicationName** и **имя** рабочего процесса, как определено в файле конфигурации. В этом случае имя приложения и имя рабочего процесса совпадают.

### <a name="state-variables"></a>Переменные состояния

Переменные состояния хранят значения состояния каждого экземпляра контракта. Переменные состояния в вашем контракте должны соответствовать свойствам рабочего процесса, определенным в файле конфигурации.

Добавьте переменные состояния в контракт в файле кода интеллектуального контракта `HelloBlockchain.sol`. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Конструктор

Конструктор определяет входные параметры для нового экземпляра интеллектуального контракта рабочего процесса. Он объявляется как функция с тем же именем, что и у контракта. Обязательные параметры для конструктора определяются как параметры конструктора в файле конфигурации. Количество, порядок и тип параметров должны совпадать в обоих файлах.

Перед созданием контракта в функции конструктора напишите любую бизнес-логику, которую вы хотите выполнить. Например, инициализируйте переменные состояния с начальными значениями.

Перед выходом из функции конструктора вызовите функцию `ContractCreated()`. Эта функция уведомит Blockchain Workbench о создании контракта.

Добавьте функцию конструктора в контракт в файле кода интеллектуального контракта `HelloBlockchain.sol`. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Функции Azure

Функции являются исполняемыми единицами бизнес-логики в контракте. Обязательные параметры для функции определяются как параметры функции в файле конфигурации. Количество, порядок и тип параметров должны совпадать в обоих файлах. Функции связаны с переходами в рабочем процессе Blockchain Workbench в файле конфигурации. Переход — это действие, выполняемое, чтобы перейти к следующему этапу рабочего процесса приложения, как определено в контракте.

Напишите любую бизнес-логику, которую вы хотите выполнить в функции. Например, чтобы изменить значение переменной состояния.

Перед выходом из функции вызовите функцию `ContractUpdated()`. Она уведомит Blockchain Workbench об обновлении состояния контракта. Чтобы отменить изменения состояния, внесенные в функцию, вызовите функцию revert(). Эта функция отменит изменения состояния, произошедшие со времени последнего вызова функции ContractUpdated().

1. Добавьте следующие функции в контракт в файле кода интеллектуального контракта `HelloBlockchain.sol`. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Сохраните файл кода интеллектуального контракта `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Добавление блокчейн-приложения в Blockchain Workbench

Чтобы добавить блокчейн-приложение в Blockchain Workbench, для определения приложения нужно передать файлы конфигурации и интеллектуального контракта.

1. В веб-браузере перейдите по адресу Blockchain Workbench. Например, `https://{workbench URL}.azurewebsites.net/`. При развертывании Blockchain Workbench будет создано веб-приложение. Сведения о том, как найти веб-адрес Blockchain Workbench, см. в разделе об [URL-адресе Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url).
2. Войдите как администратор Blockchain Workbench. Дополнительные сведения об управлении пользователями в Azure Blockchain Workbench см. в [этой](blockchain-workbench-manage-users.md) статье.
3. Выберите **Приложения** > **Создать**. Появится область **нового приложения**.
4. Выберите **Upload the contract configuration** (Передать конфигурацию контракта)  > **Обзор**, чтобы найти созданный файл конфигурации **HelloBlockchain.json**. Файл конфигурации пройдет автоматическую проверку. Щелкните ссылку **Показать** для отображения ошибок проверки. Исправьте ошибки проверки перед развертыванием приложения.
5. Выберите **Upload the contract code** (Передать код контракта)  > **Обзор**, чтобы найти файл кода интеллектуального контракта **HelloBlockchain.sol**. Файл кода пройдет автоматическую проверку. Щелкните ссылку **Показать** для отображения ошибок проверки. Исправьте ошибки проверки перед развертыванием приложения.
6. Выберите **Развернуть** для создания блокчейн-приложения на основе файлов конфигурации и интеллектуального контракта.

Развертывание блокчейн-приложения займет несколько минут. После завершения развертывания новое приложение появится в области **приложений**. 

> [!NOTE]
> Вы также можете создать блокчейн-приложения с помощью [REST API Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Добавление участников блокчейн-приложения

Добавьте участников приложения в свое приложение, чтобы выполнять действия с контрактами. Чтобы добавить участников приложения, вы должны быть [администратором Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Выберите **Приложения** > **Hello, Blockchain!**.
2. Количество участников, связанных с приложением, будет отображаться в правом верхнем углу страницы. В новом приложении количество участников будет равно нулю.
3. Щелкните ссылку **Участники** в правом верхнем углу страницы. Появится текущий список участников приложения.
4. В списке участников выберите **Добавить участников**.
5. Выберите или введите имя участника, которого вы хотите добавить. В списке будут представлены только пользователи Azure AD, которые есть в клиенте Blockchain Workbench. Если пользователь не найден, вам необходимо [добавить пользователей Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Выберите **Роль** для участника. Для первого участника выберите роль **Запрашивающая сторона**.
7. Выберите **Добавить**, чтобы добавить участника со связанной ролью в приложение.
8. Добавьте другого участника в приложение, назначив ему роль **Респондент**.

Дополнительные сведения об управлении пользователями в Blockchain Workbench см. в [этой](blockchain-workbench-manage-users.md) статье.

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали базовое приложение для выполнения запроса и отправки ответа. Сведения об использовании приложения см. в следующей статье.

> [!div class="nextstepaction"]
> [Using applications in Azure Blockchain Workbench](blockchain-workbench-use.md) (Использование приложений в Azure Blockchain Workbench)

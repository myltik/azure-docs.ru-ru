---
title: Справочник по конфигурации Azure Blockchain Workbench
description: Обзор конфигурации приложения Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Справочник по конфигурации Azure Blockchain Workbench

 Приложения Azure Blockchain Workbench — это рабочие процессы с несколькими участниками, определяемые метаданными конфигурации и кодом интеллектуального контракта. Метаданные конфигурации определяют высокоуровневые рабочие процессы и модель взаимодействия блокчейн-приложения. Интеллектуальные контракты определяют бизнес-логику блокчейн-приложения. Для создания возможностей блокчейн-приложения Workbench использует конфигурацию и код интеллектуального контракта.

Метаданные конфигурации указывают для каждого блокчейн-приложения следующие сведения: 

* Имя и описание блокчейн-приложения.
* Уникальные роли для пользователей, которые могут выполнять действия или быть участниками в блокчейн-приложении.
* Один или несколько рабочих процессов. Каждый рабочий процесс выступает в качестве конечного автомата для управления потоком бизнес-логики. Рабочие процессы могут быть независимыми друг от друга или взаимодействовать друг с другом.

В каждом определенном рабочем процессе указывается следующее:

* Имя и описание рабочего процесса.
* Состояния рабочего процесса.  Каждое состояние — это ступень в потоке управления бизнес-логики. 
* Действия для перехода в следующее состояние.
* Роли пользователей, у которых есть разрешения инициировать каждое действие.
* Интеллектуальные контракты, которые представляют бизнес-логику в файлах кода.

## <a name="application"></a>Приложение

Блокчейн-приложение содержит метаданные конфигурации, рабочие процессы и роли пользователей, которые могут выполнять действия или быть участниками в приложении.

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ApplicationName | Уникальное имя приложения. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **ApplicationName**, что и для применимого класса контракта.  | Yes |
| DisplayName | Понятное отображаемое имя приложения. | Yes |
| ОПИСАНИЕ | Описание приложения. | Нет  |
| ApplicationRoles | Коллекция ролей [ApplicationRoles](#application-roles). Роли пользователей, которые могут выполнять действия или быть участниками в приложении.  | Yes |
| Рабочие процессы | Коллекция рабочих процессов [Workflows](#workflows). Каждый рабочий процесс выступает в качестве конечного автомата для управления потоком бизнес-логики. | Yes |

См. [пример файла конфигурации](#configuration-file-example).

## <a name="workflows"></a>Рабочие процессы

Бизнес-логику приложения можно смоделировать в качестве конечного автомата, когда при выполнении действия поток бизнес-логики переходит из одного состояния в другое. Рабочий процесс — это совокупность таких состояний и действий. Каждый рабочий процесс состоит из одного или нескольких интеллектуальных контрактов, которые представляют бизнес-логику в файлах кода. Исполняемый контракт — это экземпляр рабочего процесса.

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ИМЯ | Уникальное имя рабочего процесса. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **Name**, что и для применимого класса контракта. | Yes |
| DisplayName | Понятное отображаемое имя рабочего процесса. | Yes |
| ОПИСАНИЕ | Описание рабочего процесса. | Нет  |
| Инициаторы | Коллекция ролей [ApplicationRoles](#application-roles). Роли, назначаемые пользователям, которым разрешено создавать контракты в рабочем процессе. | Yes |
| StartState | Имя первоначального состояния рабочего процесса. | Yes |
| properties | Коллекция [идентификаторов](#identifiers). Представляет данные, которые могут считываться вне сети или визуализируются в средстве пользователя. | Yes |
| Конструктор | Определяет входные параметры для создания экземпляра рабочего процесса. | Yes |
| Функции Azure | Коллекция [функций](#functions), которые могут выполняться в рабочем процессе. | Yes |
| States | Коллекция [состояний](#states) рабочего процесса. | Yes |

См. [пример файла конфигурации](#configuration-file-example).

## <a name="type"></a>type

Поддерживаемые типы данных.

| type | ОПИСАНИЕ |
|-------|-------------|
| address  | Тип адреса блокчейна, такой как *контракты* или *пользователи* |
| bool     | Логический тип данных |
| contract | Адрес типа контракта |
| int      | Целочисленный тип данных |
| money;    | Денежный тип данных |
| state    | Состояние рабочего процесса |
| строка   | Строковый тип данных |
| user     | Адрес типа пользователя |
| Twitter в режиме реального     | Временной тип данных |
|`[ Application Role Name ]`| Любое имя, указанное в роли приложения. Ограничивает пользователей этим типом роли. |

## <a name="constructor"></a>Конструктор

Определяет входные параметры для экземпляра рабочего процесса.

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| Параметры | Коллекция [идентификаторов](#identifiers), необходимых для инициирования интеллектуального контракта. | Yes |

### <a name="constructor-example"></a>Пример конструктора

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Функции Azure

Определяет функции, которые могут выполняться в рабочем процессе.

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ИМЯ | Уникальное имя функции. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **Name**, что и для применимой функции. | Yes |
| DisplayName | Понятное отображаемое имя функции. | Yes |
| ОПИСАНИЕ | Описание функции. | Нет  |
| Параметры | Коллекция [идентификаторов](#identifiers), соответствующих параметрам функции. | Yes |

### <a name="functions-example"></a>Пример функций

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

Коллекция уникальных состояний рабочего процесса. Каждое состояние охватывает ступень в потоке управления бизнес-логики. 

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ИМЯ | Уникальное имя состояния. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **Name**, что и для применимого состояния. | Yes |
| DisplayName | Понятное отображаемое имя состояния. | Yes |
| ОПИСАНИЕ | Описание состояния. | Нет  |
| PercentComplete | Целочисленное значение в пользовательском интерфейсе Blockchain Workbench, которое отображает ход выполнения в потоке управления бизнес-логики. | Yes |
| Style | Визуальная подсказка, указывающая, каким является состояние: успешным или неуспешным. Есть два допустимых значения: `Success` или `Failure`. | Yes |
| Transitions | Коллекция доступных [переходов](#transitions) из текущего состояния в следующий набор состояний. | Нет  |

### <a name="states-example"></a>Пример раздела с состояниями

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

Доступные действия для перехода в следующее состояние. Одна или несколько ролей пользователей могут выполнять действие в каждом состоянии, при этом действие может переходить из одного состояния в другое в рабочем процессе. 

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| AllowedRoles | Список ролей приложения, которым разрешено инициировать переход. Все пользователи в указанной роли могут выполнять действия. | Нет  |
| AllowedInstanceRoles | Список ролей пользователей, участвующих или указанных в интеллектуальном контракте, которым разрешено инициировать переход. Роли экземпляра определены в поле **Properties** в разделе Workflows. Это пользователи, участвующие или указанные в интеллектуальном контракте, а не все пользователи типа роли. | Нет  |
| DisplayName | Понятное отображаемое имя перехода. | Yes |
| ОПИСАНИЕ | Описание перехода. | Нет  |
| Функция | Имя функции, которая инициирует переход. | Yes |
| NextStates | Коллекция возможных следующих состояний после успешного перехода. | Yes |

### <a name="transitions-example"></a>Пример раздела с переходами

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Роли приложений

Роли приложения определяют набор ролей, которые могут назначаться пользователям, которые хотят выполнять действия или быть участниками в приложении. Роли приложений можно использовать для ограничения действий и участия в блокчейн-приложении и соответствующих рабочих процессах. 

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ИМЯ | Уникальное имя роли приложения. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **Name**, что и для применимой роли. Имена базового типа зарезервированы. Роли приложения нельзя присвоить имя, которое уже используется для типа [Type](#type)| Yes |
| ОПИСАНИЕ | Описание роли приложения. | Нет  |

### <a name="application-roles-example"></a>Пример раздела с ролями приложения

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>"Identifiers" (Идентификаторы)

Идентификаторы представляют собой коллекцию сведений, используемых для описания свойств рабочего процесса, конструктора и параметров функции. 

| Поле | ОПИСАНИЕ | Обязательно |
|-------|-------------|:--------:|
| ИМЯ | Уникальное имя свойства или параметра. Для соответствующего интеллектуального контракта должно использоваться то же самое имя **Name**, что и для применимого свойства или параметра. | Yes |
| DisplayName | Понятное отображаемое имя свойства или параметра. | Yes |
| ОПИСАНИЕ | Описание свойства или параметра. | Нет  |

### <a name="identifiers-example"></a>Пример раздела с идентификаторами

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Пример файла конфигурации

В следующем примере определяется базовое приложение типа "запрос и ответ", в котором инициатор запроса отправляет запрос, а респондент отправляет ответ на этот запрос.

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
      "Name": "RequestResponse",
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
## <a name="next-steps"></a>Дополнительная информация

[Развертывание Azure Blockchain Workbench](blockchain-workbench-deploy.md)


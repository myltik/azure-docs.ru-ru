---
title: Справочник по конфигурации Azure Blockchain Workbench
description: Обзор конфигурации приложения Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 178c2c1d4f727241338d6d933cd5eecbbffe65bb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303820"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Справочник по конфигурации Azure Blockchain Workbench

 Приложения Azure Blockchain Workbench — это рабочие процессы с несколькими участниками, определяемые метаданными конфигурации и кодом интеллектуального контракта. Метаданные конфигурации определяют высокоуровневые рабочие процессы и модель взаимодействия блокчейн-приложения. Интеллектуальные контракты определяют бизнес-логику блокчейн-приложения. Workbench создает возможности блокчейн-приложения с помощью конфигурации и кода интеллектуального контракта.

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
| AllowedInstanceRoles | Список ролей пользователей, участвующих или указанных в интеллектуальном контракте, которым разрешено инициировать переход. Роли экземпляра определены в поле **Properties** в рабочих процессах. Поле AllowedInstanceRoles представляет пользователя, участвующего в интеллектуальном контракте. AllowedInstanceRoles дает возможность ограничить действия для роли пользователя в экземпляре контракта.  Например, вы можете разрешить завершить работу пользователю, который создал контракт (InstanceOwner), а не всем пользователям в типе роли (Владелец), если вы указали роль в AllowedRoles. | Нет  |
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

Перемещение актива — это сценарий интеллектуального контракта для покупки и продажи высокоценных активов, для которого требуется инспектор и оценщик. Продавцы могут перечислять свои активы, создавая интеллектуальный контракт перемещения активов. Покупатели могут делать предложения, выполняя действия в соответствии с интеллектуальным контрактом, а другие стороны могут выполнять проверку или оценку актива. После того как актив будет отмечен как проверенный и оцененный, покупатель и продавец подтвердят продажу снова до завершения контракта. На каждом этапе процесса все участники могут просматривать состояние контракта по мере его обновления. 

Дополнительные сведения, включая файлы кода, см. в статье [Asset Transfer Sample for Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer) (Пример перемещения активов для Azure Blockchain Workbench).

Следующий файл конфигурации предназначен для примера перемещения активов:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
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
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
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
      },
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
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
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
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
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
    }
  ]
}
```
## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench) (REST API Azure Blockchain Workbench)


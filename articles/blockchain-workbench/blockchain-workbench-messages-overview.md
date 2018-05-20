---
title: Обзор сообщений Azure Blockchain Workbench
description: Общие сведения об использовании сообщений в Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9fa0d74b2c07d3e460abc54ea6ef9733f07a64d1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Обзор сообщений Azure Blockchain Workbench

Помимо REST API, Azure Blockchain Workbench также предоставляет интеграции на основе обмена сообщениями. Workbench публикует связанные с реестром события через службу "Сетка событий Azure", позволяя нисходящим объектам-получателям получать данные или предпринимать действия на основе этих событий. Для клиентов, которым требуется надежный обмен сообщениями, Azure Blockchain Workbench также доставляет сообщения в конечную точку служебной шины Azure.

Разработчики также заинтересованы в возможности внешних систем инициировать транзакции для создания пользователей, создания контрактов и обновления контрактов в реестре. Хотя эти функции в текущее время не предоставляются в общедоступной предварительной версии, пример, предоставляющий эти возможности, можно найти по адресу [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Уведомления о событиях

Уведомление о событиях можно использовать для уведомления пользователей и подчиненных систем о событиях, происходящих в Workbench и сети блокчейна, к которой оно подключено. Уведомление о событии можно использовать непосредственно в коде или с такими инструментами, как Logic Apps и Flow, чтобы активировать поток данных в подчиненные системы.

Дополнительные сведения о различных сообщениях, которые могут быть получены, см. в [справочниках по сообщениям об уведомлениях](#notification-message-reference).

### <a name="consuming-event-grid-events-with-azure-functions"></a>Использование событий службы "Сетка событий Azure" с решением "Функции Azure"

Если пользователь хочет получать уведомления о событиях, которые происходят в Blockchain Workbench, через службу "Сетка событий",события из этой службы можно получать, используя решение "Функции Azure".

1. Создайте **приложение-функцию Azure** на портале Azure.
2. Создайте функцию.
3. Найдите шаблон для службы "Сетка событий". Будет показан базовый код шаблона для чтения сообщения. При необходимости измените код.
4. Сохраните функцию. 
5. Выберите службу "Сетка событий" из группы ресурсов Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Использование событий службы "Сетка событий Azure" со службой Logic Apps

1.  Создайте **приложение логики Azure** на портале Azure.
2.  При открытии этого приложения на портале Azure будет предложено выбрать триггер. Выберите **Azure Event Grid -- When a resource event occurs** (Сетка событий Azure — при возникновении события ресурса).
3. При появлении конструктора рабочих процессов вам будет предложено войти в систему.
4. Выберите подписку. Вам нужен ресурс в виде **Microsoft.EventGrid.Topics**. Выберите **имя ресурса** среди доступных в группе ресурсов Azure Blockchain Workbench.
5. Выберите службу "Сетка событий" из группы ресурсов Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Использование разделов служебной шины для уведомлений

Разделы служебной шины могут использоваться для уведомления пользователей о событиях, произошедших в Blockchain Workbench. 

1.  Перейдите к служебной шине в группе ресурсов Workbench.
2.  Выберите **Разделы**.
3.  Выберите **workbench-external**.
4.  Создайте подписку для этого раздела. Получите ключ для нее.
5.  Создайте программу, которая подписывается на события из этой подписки.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Использование сообщений служебной шины с Logic Apps

1. Создайте **приложение логики Azure** на портале Azure.
2.  При открытии этого приложения на портале Azure будет предложено выбрать триггер. Введите **Служебная шина** в поле поиска и выберите соответствующий триггер для типа взаимодействия со служебной шиной. Например, **Service Bus -- When a message is received in a topic subscription (auto-complete)** (Служебная шина — при получении сообщения в подписке раздела (автозавершение)).
3. При отображении конструктора рабочих процессов укажите информацию о подключении для служебной шины.
4. Выберите подписку и укажите раздел **workbench-external**.
5. Разработайте логику приложения, которая использует сообщения из этого триггера.

## <a name="notification-message-reference"></a>Справочник по сообщениям об уведомлениях

В зависимости от имени операции сообщения об уведомлениях будут иметь один из следующих типов сообщений.

### <a name="accountcreated"></a>AccountCreated

Указывает, что новая учетная запись была запрошена для добавления в указанную цепочку.

| ИМЯ    | ОПИСАНИЕ  |
|----------|--------------|
| UserId  | Идентификатор созданного пользователя |
| ChainIdentifier | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это будет адрес пользователя в цепочке. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Указывает, что запрос был сделан для вставки или обновления контракта в распределенном реестре.

| ИМЯ | ОПИСАНИЕ |
|-----|--------------|
| ChainID | Уникальный идентификатор цепочки, связанной с запросом.|
  BlockId | Уникальный идентификатор блока в реестре.|
  ContractId | Уникальный идентификатор контракта.|
  ContractAddress |       Адрес контракта в реестре.|
  TransactionHash  |     Хэш транзакции в реестре.|
  OriginatingAddress |   Адрес инициатора транзакции.|
  ActionName       |     Имя действия.|
  IsUpdate        |      Определяет, является ли это обновлением.|
  Параметры       |     Список объектов, которые определяют имя, значение и тип данных параметров, отправленных действию.|
  TopLevelInputParams |  В сценариях, где контракт подключен к одному или нескольким контрактам, это параметры из контракта верхнего уровня. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Указывает, что запрос был сделан для выполнения действия в определенном контракте распределенного реестра.

| ИМЯ                     | ОПИСАНИЕ                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Уникальный идентификатор этого действия контракта.                                                                                                                                |
| ChainIdentifier          | Уникальный идентификатор цепочки.                                                                                                                                           |
| ConnectionId             | Уникальный идентификатор подключения.                                                                                                                                      |
| UserChainIdentifier      | Адрес пользователя, который был создан в сети блокчейна. В Ethereum это будет адрес пользователя в цепочке.                                                     |
| ContractLedgerIdentifier | Адрес контракта в реестре.                                                                                                                                        |
| WorkflowFunctionName     | Имя функции рабочего процесса.                                                                                                                                                |
| WorkflowName             | Имя рабочего процесса.                                                                                                                                                         |
| WorkflowBlobStorageURL   | URL-адрес контракта в хранилище BLOB-объектов.                                                                                                                                      |
| ContractActionParameters | Параметры для действия контракта.                                                                                                                                           |
| TransactionHash          | Хэш транзакции в реестре.                                                                                                                                    |
| ProvisioningStatus      | Текущее состояние подготовки действия.</br>0 — Created (Создано).</br>1 — In Process (Выполняется).</br>2 — Complete (Завершено).</br> Состояние Complete указывает на подтверждение успешного добавления от реестра.                                               |
|                          |                                                                                                                                                                               |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Указывает, что был выполнен запрос на обновление пользовательского баланса в определенном распределенном реестре.

> [!NOTE]
> Это сообщение создается только для реестров, в которых требуется финансирование учетных записей.
> 

| ИМЯ    | ОПИСАНИЕ                              |
|---------|------------------------------------------|
| Адрес | Адрес финансируемого пользователя. |
| Balance | Пользовательский баланс.         |
| ChainID | Уникальный идентификатор цепочки.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Сообщение указывает, что запрос был сделан для добавления блока в распределенном реестре.

| ИМЯ           | ОПИСАНИЕ                                                            |
|----------------|------------------------------------------------------------------------|
| ChainID        | Уникальный идентификатор цепочки, в которую был добавлен блок.             |
| BlockId        | Уникальный идентификатор блока в Azure Blockchain Workbench. |
| BlockHash      | Хэш блока.                                                 |
| BlockTimestamp | Метка времени блока.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Сообщение содержит сведения о запросе на добавление транзакции в распределенный реестр.

| ИМЯ            | ОПИСАНИЕ                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainID         | Уникальный идентификатор цепочки, в которую был добавлен блок.             |
| BlockId         | Уникальный идентификатор блока в Azure Blockchain Workbench. |
| TransactionHash | Хэш транзакции.                                           |
| Из            | Адрес инициатора транзакции.                      |
| Кому              | Адрес целевого получателя транзакции.              |
| Значение           | Значение, включенное в транзакцию.                                 |
| IsAppBuilderTx  | Определяет, является ли это транзакцией Blockchain Workbench.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Содержит сведения о назначении идентификатора цепочки для контракта. Например, в блокчейне Ethereum это адрес контракта в реестре.

| ИМЯ            | ОПИСАНИЕ                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Уникальный идентификатор контракта в Azure Blockchain Workbench. |
| ChainIdentifier | Идентификатор контракта в цепочке.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Дополнительная информация

* [Архитектура Azure Blockchain Workbench](blockchain-workbench-architecture.md)
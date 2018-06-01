---
title: Использование приложений в Azure Blockchain Workbench
description: Использование контрактов приложения в Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258045"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Использование приложений в Azure Blockchain Workbench

Blockchain Workbench позволяет создавать контракты и выполнять действия по ним. Также вы здесь можете просмотреть некоторые сведения, например текущее состояние и журнал транзакций.

## <a name="prerequisites"></a>предварительным требованиям

* Развертывание Blockchain Workbench. Дополнительные сведения о развертывании Azure Blockchain Workbench см. в [этой статье](blockchain-workbench-deploy.md).
* Развернутое блокчейн-приложение в Blockchain Workbench. Сведения см. в статье [Создание блокчейн-приложения в Azure Blockchain Workbench]().

[Откройте Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) в браузере.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Необходимо выполнить вход от имени члена Blockchain Workbench. Если в списке нет приложений, значит вы являетесь членом Blockchain Workbench, но не являетесь членом какого-либо приложения. Назначить членство в приложении может администратор Blockchain Workbench.

## <a name="create-new-contract"></a>Создание контракта 

Чтобы создать новый контракт, необходимо быть членом, который указан как **инициатор** контракта. Сведения об определении ролей приложения и инициаторов контракта см. в разделе [Рабочие процессы](blockchain-workbench-configuration-overview.md#workflows). Сведения о назначении членам ролей приложения см. в разделе [Добавление участника в приложение](blockchain-workbench-manage-users.md#add-member-to-application).

1. В разделе приложения Blockchain Workbench выберите плитку приложения, которое содержит создаваемый контракт. Отобразится список активных контрактов.

2. Чтобы создать контракт, выберите **Создать контракт**.

    ![Кнопка создания контракта](media/blockchain-workbench-use/contract-list.png)

3. Отобразится панель **создания контракта**. Введите исходные значения для параметров. Нажмите кнопку **Создать**.

    ![Панель добавления нового контракта](media/blockchain-workbench-use/new-contract.png)

    Созданный контракт сразу же отобразится в списке рядом с остальными контрактами.

    ![Список активных контрактов](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Действия с контрактами

В зависимости от состояния контракта члены могут выполнять действия для перехода в следующее состояние контракта. Действия определяются как [переходы](blockchain-workbench-configuration-overview.md#transitions) в [состояние](blockchain-workbench-configuration-overview.md#states). Члены, относящиеся к разрешенным приложениям или экземплярам ролей для перехода, могут выполнять действия. 

1. В разделе приложения Blockchain Workbench выберите плитку приложения, которое содержит контракт, с которым вы хотите выполнить действия.
2. В списке выберите контракт. Сведения о контракте отображаются в других разделах. 

    ![Сведения о контракте](media/blockchain-workbench-use/contract-details.png)

    | Раздел  | ОПИСАНИЕ  |
    |---------|---------|
    | Status | Отображение текущего состояния по стадиям контракта |
    | Сведения | Текущие значения контракта |
    | Действие | Сведения о последнем действии |
    | Действие | Журнал транзакций для контракта |
    
3. В разделе **Действия** выберите **Выполнить действие**.

4. На панели отобразятся сведения о текущем состоянии контракта. Выберите в раскрывающемся списке действие, которое вы намерены выполнить. 

    ![Выбор действия](media/blockchain-workbench-use/choose-action.png)

5. Чтобы инициировать действие, выберите **Выполнить действие**.
6. Если параметры являются обязательными для действия, укажите соответствующие значения.

    ![Выполнить действие](media/blockchain-workbench-use/take-action.png)

7. Чтобы выполнить действие, выберите **Выполнить действие**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Как устранить неполадки в Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)
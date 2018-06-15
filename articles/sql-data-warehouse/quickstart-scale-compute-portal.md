---
title: Краткое руководство. Горизонтальное масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure | Документация Майкрософт
description: Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure Масштабируйте вычислительные ресурсы, чтобы повысить производительность, или выполняйте обратное масштабирование, чтобы сократить расходы.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d707b9a4b7b683e75bb9313dae2611fd59dbb155
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182465"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Краткое руководство. Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure

Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure [Горизонтально масштабируйте вычислительные ресурсы](sql-data-warehouse-manage-compute-overview.md), чтобы повысить производительность, или уменьшайте их масштаб, чтобы сократить затраты. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Выполните вход на портал Azure.

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Перед началом работы

Вы можете масштабировать уже существующее хранилище данных или с помощью краткого руководства [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md) создать хранилище данных с именем **mySampleDataWarehouse**.  В этом кратком руководстве масштабируется хранилище **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

В хранилище данных SQL вы можете увеличивать и уменьшать объем вычислительных ресурсов, изменяя число единиц использования хранилища данных (DWU). В статье [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md) мы создали хранилище **mySampleDataWarehouse** и инициализировали его со значением 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Изменить число единиц использования хранилища данных можно так:

1. На портале Azure на странице слева щелкните **Хранилища данных SQL**.
2. На странице **Хранилища данных SQL** выберите **mySampleDataWarehouse**. Откроется хранилище данных.
3. Щелкните пункт **Масштаб**.

    ![Щелкните пункт Масштаб](media/quickstart-scale-compute-portal/click-scale.png)

2. В области "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

    ![Переместите ползунок](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Выберите команду **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Щелкните Сохранить](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Дополнительная информация
Вы узнали, как масштабировать вычислительные ресурсы для хранилища данных. Чтобы узнать больше о хранилище данных SQL Azure, перейдите к руководству по загрузке данных.

> [!div class="nextstepaction"]
>[Загрузка данных в хранилище данных SQL](load-data-from-azure-blob-storage-using-polybase.md)

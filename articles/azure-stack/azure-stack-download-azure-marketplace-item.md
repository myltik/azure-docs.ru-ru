---
title: "Скачивание элементов Marketplace из Azure | Документация Майкрософт"
description: "Скачивание элементов Marketplace из Azure в развертывание Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Скачивание элементов Marketplace из Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Когда вы решаете, какое содержимое включить в ваш Azure Stack Marketplace, нужно рассмотреть содержимое, доступное в Azure Stack Marketplace. Можно скачать проверенный список элементов Azure Marketplace, который был заранее протестирован для выполнения в Azure Stack. В этот список часто добавляются новые элементы, поэтому его следует периодически проверять.

Чтобы скачать элементы Marketplace, сначала необходимо [зарегистрироваться в Azure Stack с помощью Azure](azure-stack-register.md). 

## <a name="download"></a>Загрузить
1. Войдите на портал администратора Azure Stack (https://portal.local.azurestack.external).
2. Некоторые элементы Marketplace могут быть очень большими.  Убедитесь, что в системе есть достаточно места, щелкнув **Поставщики ресурсов** > **Хранилище**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace).

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Щелкните **Add from Azure** (Добавить из Azure), чтобы просмотреть список доступных для скачивания элементов. Можно щелкнуть каждый элемент в списке, чтобы просмотреть его описание и размер скачиваемого файла.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Выберите нужный элемент из списка и щелкните **Скачать**. Начнется скачивание образа виртуальной машины для выбранного элемента. Время скачивания может различаться.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. После завершения скачивания можно развернуть новый элемент Marketplace от имени оператора или пользователя Azure Stack. Щелкните **+Создать**, выберите новый элемент Marketplace, выполнив поиск в категориях.
7. Щелкните **Создать**, чтобы начать процесс создания для скачанного элемента. Выполните пошаговые инструкции для развертывания элемента.

## <a name="next-steps"></a>Дальнейшие действия

[Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)

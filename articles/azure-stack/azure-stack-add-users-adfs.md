---
title: "Добавление пользователей для работы с ADFS в Azure Stack | Документация Майкрософт"
description: "Сведения о добавлении пользователей для работы с развертываниями Azure Stack с помощью служб федерации Active Directory"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 2cad56caa182a9abdca02944000b1506953af3f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="add-users-in-the-azure-stack-development-kit"></a>Добавление пользователей в пакет SDK для Azure Stack

*Область применения: пакет SDK для Azure Stack*

Чтобы добавить дополнительных пользователей в среду развертывания пакета SDK, добавьте их в каталог пакета SDK для Azure Stack, используя консоль управления (MMC) на хост-компьютере Azure Stack.
1.  Откройте консоль MMC на хост-компьютере Azure Stack.
2.  Щелкните **Файл > Добавить или удалить оснастку**.
3.  Выберите **Active Directory — пользователи и компьютеры** > **AzureStack.local** > **Пользователи**.
4.  Щелкните **Действия** > **Создать** > **Пользователь**.
5.  В окне "Новый объект — пользователь", укажите и подтвердите пароль.
6.  Нажмите кнопку **Далее**, чтобы подтвердить значения, и кнопку "Готово", чтобы создать пользователя.



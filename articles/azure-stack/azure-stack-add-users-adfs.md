---
title: Добавление пользователей для работы с ADFS в Azure Stack | Документация Майкрософт
description: Сведения о добавлении пользователей для работы с развертываниями Azure Stack с помощью служб федерации Active Directory
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 5774750edc5b7380275d4f20aee3be47f2f62b4d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
ms.locfileid: "29554592"
---
# <a name="add-users-in-the-azure-stack-development-kit"></a>Добавление пользователей в Пакет средств разработки Azure Stack

*Область применения: Пакет средств разработки Azure Stack*

Чтобы добавить дополнительных пользователей в среду развертывания пакета средств разработки, добавьте их в каталог Пакета средств разработки Azure Stack, используя консоль управления (MMC) на хост-компьютере Azure Stack.
1.  Откройте консоль MMC на хост-компьютере Azure Stack.
2.  Щелкните **Файл > Добавить или удалить оснастку**.
3.  Выберите **Active Directory — пользователи и компьютеры** > **AzureStack.local** > **Пользователи**.
4.  Щелкните **Действия** > **Создать** > **Пользователь**.
5.  В окне "Новый объект — пользователь", укажите и подтвердите пароль.
6.  Нажмите кнопку **Далее**, чтобы подтвердить значения, и кнопку "Готово", чтобы создать пользователя.



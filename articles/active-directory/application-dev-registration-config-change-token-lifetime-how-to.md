---
title: "Как изменить стандартное время существования токена для специально разработанного приложения | Документы Майкрософт"
description: "Обновление политик времени существования токена для приложения, разрабатываемого в Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 54c30123bb582f515dfb0324cdfd897a6c4af5c0
ms.lasthandoff: 04/14/2017


---


# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Как изменить стандартное время существования токена для специально разработанного приложения

Azure AD Premium позволяет разработчикам приложений и администраторам клиентов настраивать время существования токенов, выданных для неконфиденциальных клиентов. Политики времени существования токенов настраиваются на уровне клиента или ресурсов, к которым осуществляется доступ.

 * Чтобы задать политику времени существования токена, нужно скачать [модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Выполните команду **Connect-AzureAD-Confirm**.

 * Ниже приведен пример политики, в котором задается токен обновления максимального возраста с одним фактором. Создайте политику: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```.

 * Дополнительные сведения о создании пользовательских параметров см. в документе [Настройка времени существования токенов](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes).

## <a name="next-steps"></a>Дальнейшие действия
[Настройка времени существования токенов](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Справочник по токенам в Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)



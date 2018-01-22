---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Настройка | Документация Майкрософт"
description: "Здесь описывается, как классическое приложение для Windows .NET (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 922fd0e24334eb45995b66dfaaa49d3d4f835e9a
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2018
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации
На этом шаге вам нужно добавить идентификатор приложения в свой проект.

1.  Откройте файл `App.xaml.cs` и замените строку, содержащую `ClientId`, на:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Дальнейшие действия

[!INCLUDE  [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]

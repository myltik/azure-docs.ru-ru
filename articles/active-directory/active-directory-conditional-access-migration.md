---
title: "Перенос классических политик на портале Azure | Документация Майкрософт"
description: "Прочитайте о том, для миграции политик классического портала Azure."
services: active-directory
keywords: "условный доступ к приложениям, условный доступ посредством Azure Active Directory, безопасный доступ к ресурсам организации, политики условного доступа"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Перенос классических политик на портале Azure 


С помощью [условного доступа](active-directory-conditional-access-azure-portal.md) Azure Active Directory (Azure AD) можно контролировать доступ авторизованных пользователей к облачным приложениям. Хотя назначение остается неизменным, выпуск новый портал Azure внес значительные улучшения в принципах работы условного доступа.

Рекомендуется использовать перенос политики, так как вы не создали на портале Azure.

- Теперь можно исправить, сценарии, которые не удалось обработать до.

- Можно уменьшить количество политик, которыми необходимо управлять, объединяя их.   

- Можно управлять в одном централизованном месте все политики условного доступа.

- Классический портал Azure будет прекращено.   

В этой статье объясняется, что необходимо перенести существующие политики условного доступа для новой платформы.
 
## <a name="classic-policies"></a>Классические политики

В [портал Azure](https://portal.azure.com), [условного доступа — политики](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) страница является политики точку входа для вашего условного доступа. Тем не менее в вашей среде, может также потребоваться политики условного доступа, не созданных с помощью этой страницы. Эти политики называются *классический политики*. Классический являются политиками условного доступа, созданных в:

- Классический портал Azure
- Классический портал Intune
- Портал защиты приложений Intune


На **условного доступа** страницы, классический политик можно открыть, щелкнув [ **политики классический (Предварительная версия)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) в **управление** раздел. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


**Политики классический** представление предоставляет возможность:

- Фильтрация классический политик.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Отключите классический политики.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Проверьте параметры классический политик (и отключить его).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


При отключении классический политики больше нельзя вернуть этот шаг. Таким образом, можно изменить список членов группы в классическом политики с помощью **сведения** представления. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Либо изменив выбранные группы или исключить определенных групп можно протестировать влияние классический отключенную политику для нескольких тестовых пользователей перед отключением политики для всех включаемых пользователей и групп. 



## <a name="azure-ad-conditional-access-policies"></a>Политики условного доступа Azure AD

С помощью условного доступа на портале Azure вы можете управлять всех политик в одном централизованном месте. Поскольку реализации как условный доступ значительно изменился, следует ознакомиться с основными понятиями, перед переносом классический политик.

См.:

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md) Дополнительные сведения об основных понятиях и терминах.

- [Советы и рекомендации для условного доступа в Azure Active Directory](active-directory-conditional-access-best-practices.md) получить некоторые рекомендации по развертыванию условного доступа в вашей организации.

- [Начало работы с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) для ознакомления с помощью пользовательского интерфейса на портале Azure.


 
## <a name="migration-considerations"></a>Рекомендации по переносу

В этой статье политики условного доступа Azure AD также называются *новые политики*.
Классический политик по-прежнему работать параллельно с вашей новой политики, пока отключить или удалить их. 

В контексте консолидации политики важны следующие аспекты:

- Хотя классический политики привязаны к определенной облачного приложения, можно выбрать столько облачных приложений, необходимое для новой политики.

- Элементы управления классический политики и политику для облачного приложения требуют все элементы управления (*AND*) отработки. 


- В новой политики можно:
 
    - Объединить несколько условий, если это требуется для вашего сценария. 

    - Выберите несколько предоставить требований в виде доступа элемента управления и объединять их с логического *или* (требуется одна из выбранных элементов управления) или с помощью логического *AND* (требуются все выбранные элементы управления).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Если вы хотите перенести классический политики для **Office 365 Exchange online** , которые включают **Exchange Active Sync** в качестве условия клиентских приложений, не можно объединить в одну новую политику. 

Такое случается, например, если требуется поддержка всех типов клиентских приложений. В новую политику, которая имеет **Exchange Active Sync** в качестве условия клиентского приложения, невозможно выбрать другие клиентские приложения.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Объединение в одну новую политику можно также не Если классический политик содержат несколько условий. Новая политика, которая имеет **Exchange Active Sync** как клиентские приложения настроены условия не поддерживает другие условия:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Если имеется новая политика, которая имеет **Exchange Active Sync** как клиентские приложения условие настроен, необходимо убедитесь в том, что не настроены другие условия. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Приложение под управлением](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) классический политики для Office 365 Exchange Online, которые содержат **Exchange Active Sync** разрешать в клиентских приложениях условие **поддерживается** и **неподдерживается** [платформ устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition). Пока вы не можете настроить отдельных платформах связанные новую политику, можно ограничить в службу поддержки для [поддерживаемых платформ устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition) только. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Можно объединить несколько классический политики, которые содержат **Exchange Active Sync** в качестве условия приложений клиента, если они имеют:

- Только **Exchange Active Sync** в качестве условия 

- Несколько требований для предоставления доступа настроены

Одним из типичных сценариев является объединением:

- Устройства под управлением классический политику из классического портала Azure 
- Приложение классический политики на основе Intune приложения на портале защиты 
 
В этом случае классический политик можно объединить в одну новую политику, которая имеет оба требования.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Платформы устройств

Классический политик с [элементов управления на основе приложения](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) предварительно настроены с iOS и Android как [условие платформы устройства](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

В новой политики, необходимо выбрать [платформ устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition) необходима поддержка по отдельности.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Дальнейшие действия

- Если вы хотите узнать, как настроить политику условного доступа, см. статью о том, как [начать работу с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md). 

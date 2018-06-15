---
title: Перенос классических политик на портале Azure | Документация Майкрософт
description: Узнайте, что нужно для переноса классических политик на портал Azure.
services: active-directory
keywords: условный доступ к приложениям, условный доступ посредством Azure Active Directory, безопасный доступ к ресурсам организации, политики условного доступа
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b6285381833526cdbdd7c1392894458a47c1cf34
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723734"
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Перенос классических политик на портале Azure 


С помощью [условного доступа](active-directory-conditional-access-azure-portal.md) Azure Active Directory (Azure AD) можно контролировать доступ авторизованных пользователей к облачным приложениям. Хотя назначение этой функции осталось без изменений, в новом выпуске портала Azure возможности условного доступа значительно расширены с помощью усовершенствований.

Рекомендуем перенести политики, которые вы не создали на портале Azure, по следующим причинам:

- Теперь вы можете работать со сценариями, которые невозможно было обрабатывать ранее.

- Можно уменьшить количество политик, которыми необходимо управлять, объединяя их.   

- Всеми политиками условного доступа можно управлять централизованно.

- Использование классического портала Azure будет прекращено.   

В этой статье объясняется, что необходимо, чтобы перенести существующие политики условного доступа на новую платформу.
 
## <a name="classic-policies"></a>Классические политики

Страница [Условный доступ — политики](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) на [портале Azure](https://portal.azure.com) является точкой входа для работы с политиками условного доступа. Тем не менее в вашей среде также могут быть установлены политики условного доступа, не созданные с помощью этой страницы. Эти политики называются *классическими политиками*. Классическими считаются политики условного доступа, созданные с помощью:

- классического портала Azure;
- классического портала Intune;
- портала службы "Защита приложений Intune".


На странице **Условный доступ** вы можете перейти к своим классическим политикам, щелкнув [**Classic policies (preview)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) (Классические политики (предварительная версия)) в разделе **Управление**. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


Представление **Классические политики** предоставляет возможность:

- Фильтровать классические политики.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Отключать классические политики.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Проверять параметры классических политик (и отключать их).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Отключение классический политики невозможно отменить. Поэтому вы можете изменить членство в группах в классической политике с помощью представления **Сведения**. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Изменив выбранные группы или исключив определенные группы, можно проверить влияние отключения классический политики на несколько тестовых пользователей, прежде чем отключить ее для всех охваченных пользователей и групп. 



## <a name="azure-ad-conditional-access-policies"></a>Политики условного доступа Azure AD

Всеми политиками можно управлять централизованно, с помощью службы условного доступа на портале Azure. Так как реализация условного доступа значительно изменилась, перед переносом классических политик рекомендуется ознакомиться с основными понятиями.

См.:

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md): получите дополнительные сведения об основных понятиях и терминологии.

- [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md): изучите рекомендации по развертыванию условного доступа в организации.

- [Начало работы с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md): ознакомьтесь с пользовательским интерфейсом на портале Azure.


 
## <a name="migration-considerations"></a>Рекомендации по переносу

В этой статье политики условного доступа Azure AD также называются *новыми политиками*.
Классические политики по-прежнему будут работать параллельно с новыми политиками, пока вы не отключите или не удалите их. 

В контексте объединения политик важны следующие аспекты.

- Классические политики привязаны к определенному облачному приложению, а для новой политики можно выбрать столько облачных приложений, сколько необходимо.

- Элементы управления классический политики и политики для облачного приложения требуют выполнения всех условий (логическая операция *И*). 


- В новой политике можно сделать следующее.
 
    - Объединить несколько условий, если это требуется для вашего сценария. 

    - Выбрать несколько требований для предоставления доступа в качестве элементов управления доступом и объединить их логической операцией *ИЛИ* (требуется один из выбранных элементов управления) или логической операцией *И* (требуются все выбранные элементы управления).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Если вы хотите перенести классические политики для **Office 365 Exchange Online**, которые включают в себя условие клиентских приложений **Exchange Active Sync**, возможно, вам не удастся объединить их в одну новую политику. 

Такое случается, например, если требуется поддержка всех типов клиентских приложений. В новой политике, содержащей условие клиентских приложений **Exchange Active Sync**, невозможно выбрать другие клиентские приложения.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Объединение в одну новую политику также невозможно, если классические политики содержат несколько условий. В новой политике, содержащей условие клиентских приложений **Exchange Active Sync**, невозможно указать другие условия.   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Если имеется новая политика, в которой настроено условие клиентских приложений **Exchange Active Sync**, то в ней не должны быть настроены какие-либо другие условия. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

Классические политики [на основе приложений](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) для Office 365 Exchange Online, которые содержат условие клиентских приложений **Exchange Active Sync**, разрешают **поддерживаемые** и **неподдерживаемые** [платформы устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition). Хотя невозможно настроить отдельные платформы устройств в связанной новой политике, можно ограничить их поддержку только [поддерживаемыми платформами устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Можно объединить несколько классических политик, которые содержат **Exchange Active Sync** в качестве условия клиентских приложений, если они:

- содержат только **Exchange Active Sync** в качестве условия; 

- содержат несколько настроенных требований для предоставления доступа.

Одним из типичных сценариев является объединение:

- классической политики на основе устройств с классического портала Azure; 
- классической политики на основе приложений с портала службы "Защита приложений Intune". 
 
В этом случае классические политики можно объединить в одну новую политику, которая содержит оба выбранных требования.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Платформы устройств

Классические политики с [элементами управления на основе приложений](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) предварительно настроены в iOS и Android в качестве [условий платформы устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

В новой политике необходимо отдельно выбрать [платформы устройств](active-directory-conditional-access-technical-reference.md#device-platform-condition), поддержка которых необходима.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Дополнительная информация

- Если вы хотите узнать, как настроить политику условного доступа, см. статью о том, как [начать работу с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md). 

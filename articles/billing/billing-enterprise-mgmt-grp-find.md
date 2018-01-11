---
title: "Поиск подписки Azure или группы управления в Azure | Документация Майкрософт"
description: "Переход между несколькими каталогами, чтобы просмотреть группы управления и подписки"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: f1b9c1ec2af8240ff71f6907516d8894c36ac9c3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Поиск подписки Azure или группы управления

Если вам сложно найти подписку или группу управления в Azure, возможно, вы используете неверный каталог. Такое может произойти, если ваша учетная запись имеется в нескольких каталогах Azure Active Directory. Каждый [каталог Active Directory является независимым](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-directory-independence), а доступ в них не наследуется.      

![Меню "Переключение каталога"](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Переключение каталогов 
На портале Azure можно легко переключить каталоги.
1.  Войдите на [портал Azure](https://portal.azure.com).
2.  Выберите свое имя в верхней правой области экрана. 
3.  В нижней области меню будут перечислены все каталоги, к которым у вас есть доступ.
4.  Выберите имя каталога для доступа. 

![Меню "Переключение каталога"](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>Ресурс недоступен? 
Если при попытке получить доступ к подписке или группе управления вы получили сообщение об ошибке о недоступности ресурса, значит вам не назначена определенная роль, чтобы просмотреть этот элемент.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Свяжитесь с административной группой подписки или группой управления, чтобы получить доступ.  
* Чтобы получить доступ к подпискам, ознакомьтесь со статьей [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure), чтобы узнать о необходимой роли.
* Для групп управления доступ RBAC пока недоступен, но ожидается в скором времени. Свяжитесь с административной группой корпоративного портала, чтобы получить доступ.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Улучшение взаимодействия с группами управления и подписками в одном каталоге 
Вы можете передать подписки или группы управления в выбранный каталог, чтобы вам ничего не приходилось искать.  Объединение подписок и групп управления в одном каталоге помогает снизить необходимость переключения между каталогами и разрешить наследуемые политики.  


### <a name="transfer-your-subscriptions"></a>Передача подписок 
Вы можете переместить подписку в каталог, связанный с вашими группами управления. Такое перемещение может выполнить администратор регистрации — переместить подписку в учетную запись в целевом каталоге. Чтобы получить дополнительные сведения, войдите на [корпоративный портал](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 







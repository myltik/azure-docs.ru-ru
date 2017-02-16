---
title: "Именованные сети в Azure Active Directory | Документация Майкрософт"
description: "Настроив именованные сети, вы избавитесь от ситуации, при которой IP-адреса вашей организации вызывают ложное срабатывание для событий риска &quot;Операции входа из нескольких географических регионов&quot; и &quot;Входы с IP-адресов с подозрительными действиями&quot;."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 71116ade676677c5b6d14b07a038d043fba46032
ms.openlocfilehash: 02a1aae805aa36aa68f341197e44b19bc6236b7b


---
# <a name="named-networks-in-azure-active-directory"></a>Именованные сети в Azure Active Directory

> [!div class="op_single_selector"]
> * [Портал Azure](active-directory-known-networks-azure-portal.md)
> * [классический портал Azure](active-directory-known-networks.md)
> 
> 


Azure Active Directory создает запись для каждого обнаруженного [события риска](active-directory-identity-protection-risk-events.md). С помощью сведений о событиях риска, доступных в отчетах о безопасности Azure Active Directory, можно получить ценную информацию о вероятности наличия скомпрометированных учетных записей пользователей в вашей среде.   

Возможна ситуация, при которой Azure Active Directory обнаружит ложное срабатывание, вызываемое IP-адресами, фактически принадлежащими вашей организации, для таких [типов событий риска](active-directory-identity-protection-risk-events.md#risk-event-types): *Невозможно переместиться в нетипичные расположения* и *Входы с IP-адресов с подозрительными действиями*. 

Например, это может произойти, когда: 

- пользователь в офисе в Бостоне удаленно подключился к центру обработки данных в Сан-Франциско, что привело к обнаружению события риска *Операции входа из нескольких географических регионов*; 

- пользователь вашей организации пытается войти несколько раз, указывая неправильный пароль, что приводит к обнаружению события риска *Входы с IP-адресов с подозрительными действиями*. 

Чтобы в таких случаях не создавались ложные отчеты о событиях риска, следует добавить именованные диапазоны IP-адресов в список общедоступных IP-адресов вашей организации.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Чтобы добавить диапазоны IP-адресов в список общедоступных IP-адресов вашей организации, выполните следующие действия.

1. Войдите на портал управления Azure.
 
2. В левой панели щелкните **Active Directory**.

    ![Известные сети](./media/active-directory-known-networks-azure-portal/01.png)

3. В колонке каталога в разделе **Управление** щелкните **Named networks** (Именованные сети).

    ![Известные сети](./media/active-directory-known-networks-azure-portal/02.png)


4. Щелкните **Добавить расположение**.

    ![Известные сети](./media/active-directory-known-networks-azure-portal/03.png)

5. В колонке **Добавление** выполните следующие действия:

    ![Известные сети](./media/active-directory-known-networks-azure-portal/04.png)

    а. В текстовом поле **Имя** введите имя.

    b. В текстовом поле **Диапазон IP-адресов** введите диапазон IP-адресов. Диапазон IP-адресов должен быть в формате CIDR. Чтобы выполнить массовое обновление, можно передать CSV-файл с диапазонами IP-адресов. С помощью этого действия диапазоны IP-адресов из файла добавляются в список, и их не нужно переписывать.

    c. Щелкните **Создать**.


**Дополнительные ресурсы:**

* [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md)
* [Операции входа с IP-адресов с подозрительными действиями](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Операции входа из нескольких географических регионов](active-directory-reporting-sign-ins-from-multiple-geographies.md)




<!--HONumber=Jan17_HO3-->



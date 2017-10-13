---
title: "Именованные расположения в Azure Active Directory | Документация Майкрософт"
description: "Настроив именованные расположения, вы предотвратите ситуацию, когда IP-адреса вашей организации вызывают ложное срабатывание для типа события риска \"Невозможно переместиться в нетипичные расположения\"."
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
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Именованные расположения в Azure Active Directory

Функция "Именованные расположения" в Azure Active Directory позволяет пометить диапазоны доверенных IP-адресов в вашей организации. В своей среде именованные расположения можно использовать в контексте обнаружения [событий риска](active-directory-reporting-risk-events.md). Эта функция позволяет сократить количество сообщаемых ложных срабатываний для типа событий риска *Невозможно переместиться в нетипичные расположения*. 

## <a name="configuration"></a>Конфигурация

Чтобы настроить именованное расположение, сделайте следующее.

1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор.

2. В левой панели щелкните **Azure Active Directory**.

    ![Ссылка на Azure Active Directory на левой панели](./media/active-directory-named-locations/01.png)

3. В колонке **Azure Active Directory** в разделе **Безопасность** щелкните **Условный доступ**.

    ![Команда "Условный доступ"](./media/active-directory-named-locations/05.png)


4. В колонке **Условный доступ** в разделе **Управление** щелкните **Именованные расположения**.

    ![Команда "Именованные расположения"](./media/active-directory-named-locations/06.png)


5. В колонке **Именованные расположения** щелкните **Создать расположение**.

    ![Команда "Создать расположение"](./media/active-directory-named-locations/07.png)


6. В колонке **Создать** сделайте следующее:

    ![Колонка "Создать"](./media/active-directory-named-locations/56.png)

    а. В поле **Имя** введите имя именованного расположения.

    b. В поле **Диапазоны IP-адресов** введите диапазон IP-адресов. Диапазон IP-адресов должен иметь формат *бесклассовой междоменной маршрутизации* (CIDR).  

    c. Щелкните **Создать**.



## <a name="what-you-should-know"></a>Необходимая информация

**Bulk updates** (Массовые обновления). При создании или обновлении именованных расположений для массовых обновлений можно передать или скачать CSV-файл с диапазонами IP-адресов. С помощью этого действия диапазоны IP-адресов из файла добавляются в список, и их не нужно переписывать.

![Ссылки для передачи и скачивания](./media/active-directory-named-locations/09.png)


**Ограничения.** Вы можете определить не более 60 именованных расположений, каждому из которых назначен один диапазон IP-адресов. Если у вас есть только одно настроенное именованное расположение, для него можно определить до 500 диапазонов IP-адресов.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о событиях риска см. в статье [События риска Azure Active Directory](active-directory-reporting-risk-events.md).


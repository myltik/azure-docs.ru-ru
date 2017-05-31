---
title: "Именованные расположения в Azure Active Directory | Документация Майкрософт"
description: "Настроив именованные расположения, вы предотвратите ситуацию, когда IP-адреса вашей организации вызывают ложное срабатывание для типа события риска &quot;Невозможно переместиться в нетипичные расположения&quot;."
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
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Именованные расположения в Azure Active Directory

Именованные расположения — это функция Azure Active Directory, которая позволяет пометить диапазоны доверенных IP-адресов в вашей организации. Вы можете использовать в вашей среде именованные расположения в контексте обнаружения [событий риска](active-directory-reporting-risk-events.md), чтобы сократить число сообщаемых ложных срабатываний для типа события риска *Невозможно переместиться в нетипичные расположения*. 




## <a name="configuration"></a>Конфигурация

**Чтобы настроить именованное расположение:**

1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор.

2. В левой панели щелкните **Azure Active Directory**.

    ![Именованные расположения](./media/active-directory-named-locations/01.png)

3. В колонке **Azure Active Directory** в разделе **Безопасность** щелкните **Условный доступ**.

    ![Именованные расположения](./media/active-directory-named-locations/05.png)


4. В колонке **Условный доступ** в разделе **Управление** щелкните **Именованные расположения**.

    ![Именованные расположения](./media/active-directory-named-locations/06.png)


5. В колонке **Именованные расположения** в меню вверху щелкните **New locations** (Новые расположения).

    ![Именованные расположения](./media/active-directory-named-locations/07.png)


6. В колонке **Создать** выполните следующие действия.

    ![Именованные расположения](./media/active-directory-named-locations/08.png)

    а. В текстовом поле **Имя** введите имя именованного расположения.

    b. В текстовом поле **Диапазон IP-адресов** введите диапазон IP-адресов. Диапазон IP-адресов должен иметь формат *бесклассовой междоменной маршрутизации* (CIDR).  

    c. Щелкните **Создать**.



## <a name="what-you-should-know"></a>Необходимая информация

**Bulk updates** (Массовые обновления). При создании или обновлении именованных расположений для массовых обновлений можно передать или скачать CSV-файл с диапазонами IP-адресов. С помощью этого действия диапазоны IP-адресов из файла добавляются в список, и их не нужно переписывать.

![Именованные расположения](./media/active-directory-named-locations/09.png)


**Ограничения**. Вы можете определить не более 60 именованных расположений, каждому из которых назначен один диапазон IP-адресов. Если у вас есть только одно настроенное именованное расположение, для него можно определить до 500 диапазонов IP-адресов.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительная информация:

- Дополнительные сведения см. в статье о [событиях риска в Azure Active Directory](active-directory-reporting-risk-events.md).



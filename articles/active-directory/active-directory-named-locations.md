---
title: "Именованные расположения в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, что такое именованные расположения и как их настраивать."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b6f80cde24edcbec68309ba033d4da16ee97b731
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="named-locations-in-azure-active-directory"></a>Именованные расположения в Azure Active Directory

С помощью именованных расположений можно пометить доверенные диапазоны IP-адресов в вашей организации. Именованные расположения в Azure Active Directory используются в следующих контекстах:

- обнаружение [событий риска](active-directory-reporting-risk-events.md) для уменьшения количества ложных положительные результатов;  

- [условный доступ на основе расположения](active-directory-conditional-access-locations.md).


В этой статье объясняется, как настроить именованные расположения в конкретной среде.


## <a name="entry-points"></a>Точки входа

Чтобы открыть страницу настройки именованных расположений в разделе **Безопасность** на странице Azure Active Directory, щелкните следующие элементы:

![Точки входа](./media/active-directory-named-locations/34.png)

- **Условный доступ:**

    - В разделе **Управление** щелкните **Именованные расположения**.
    
        ![Команда "Именованные расположения"](./media/active-directory-named-locations/06.png)

- **Рискованные входы в систему:**

    - На панели инструментов в верхней части страницы щелкните **Добавить известные диапазоны IP-адресов**.

       ![Команда "Именованные расположения"](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Пример конфигурации

**Чтобы настроить именованное расположение:**

1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор.

2. В левой панели щелкните **Azure Active Directory**.

    ![Ссылка на Azure Active Directory на левой панели](./media/active-directory-named-locations/01.png)

3. На странице **Azure Active Directory** в разделе **Безопасность** щелкните **Условный доступ**.

    ![Команда "Условный доступ"](./media/active-directory-named-locations/05.png)


4. На странице **Условный доступ** в разделе **Управление** щелкните **Именованные расположения**.

    ![Команда "Именованные расположения"](./media/active-directory-named-locations/06.png)


5. На странице **Именованные расположения** щелкните **Новое расположение**.

    ![Команда "Создать расположение"](./media/active-directory-named-locations/07.png)


6. На странице **Создать** сделайте следующее:

    ![Колонка "Создать"](./media/active-directory-named-locations/56.png)

    a. В поле **Имя** введите имя именованного расположения.

    Б. В поле **Диапазоны IP-адресов** введите диапазон IP-адресов. Диапазон IP-адресов должен иметь формат *бесклассовой междоменной маршрутизации* (CIDR).  

    c. Нажмите кнопку **Создать**.



## <a name="what-you-should-know"></a>Необходимая информация

**Bulk updates** (Массовые обновления). При создании или обновлении именованных расположений для массовых обновлений можно передать или скачать CSV-файл с диапазонами IP-адресов. С помощью этого действия диапазоны IP-адресов из файла добавляются в список, и их не нужно переписывать.

![Ссылки для передачи и скачивания](./media/active-directory-named-locations/09.png)


**Ограничения.** Вы можете определить не более 60 именованных расположений, каждому из которых назначен один диапазон IP-адресов. Если у вас есть только одно настроенное именованное расположение, для него можно определить до 500 диапазонов IP-адресов.


## <a name="next-steps"></a>Дополнительная информация

Дополнительная информация:

- Список **событий риска** см. в статье о [События риска Azure Active Directory](active-directory-reporting-risk-events.md).

- Сведения об **условном доступе** см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- **Отчеты о событиях входа, представляющих риск**, см. [на портале Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  

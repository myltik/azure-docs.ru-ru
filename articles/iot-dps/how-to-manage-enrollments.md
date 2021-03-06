---
title: Управление регистрацией устройств с помощью портала Azure | Документация Майкрософт
description: Как управлять регистрацией устройств в службе DPS на портале Azure.
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 55486d9a37968351f5313c708e9ef26e5b89063c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Как управлять регистрацией устройств с помощью портала Azure

При *регистрации устройства* создается запись одного устройства или группы устройств, которые можно в будущем зарегистрировать с помощью службы подготовки устройств для Центра Интернета вещей Azure. Запись регистрации содержит необходимую начальную конфигурацию устройств как часть регистрации, включая нужный Центр Интернета вещей. В этой статье описывается, как управлять регистрацией устройств для службы подготовки.


## <a name="create-a-device-enrollment"></a>Создание регистрации устройств

Зарегистрировать устройства с помощью службы подготовки можно двумя способами.

* **Группа регистраций** — это запись для группы устройств, которые используют общий механизм аттестации сертификатов X.509 с применением одного сертификата для подписи, который может быть [корневым сертификатом](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) или [промежуточным сертификатом](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), использующимся для создания сертификата для физического устройства. Мы советуем использовать группу регистраций для большого количества устройств, имеющих необходимую начальную конфигурацию, или для устройств, предназначенных для одного и того же клиента. Обратите внимание, что как *группы регистраций* можно регистрировать только устройства, использующие механизм аттестации X.509. 

    Вы можете создать группу регистраций на портале для группы устройств, выполнив указанные ниже действия:

    1. Войдите на портал Azure и выберите **Все ресурсы** в меню слева.  
    2. Выберите службу подготовки устройств, в которой нужно зарегистрировать устройство, из списка ресурсов.  
    3. В службе подготовки сделайте следующее:  
       a. Выберите **Управление регистрациями**, а затем — вкладку **Группы регистрации**.  
       Б. Нажмите кнопку **Добавить** вверху.  
       c. Когда появится панель Add Enrollment Group (Добавление группы регистрации), введите сведения об элементе списка регистрации.  Поле **Имя группы** является обязательным. Также выберите **тип сертификата** "Сертификат ЦС" или Intermediate Certificate (Промежуточный сертификат) и отправьте корневой **основной сертификат** для группы устройств.  
       d. Выберите команду **Сохранить**. При успешном создании группы регистраций под вкладкой **Enrollment Groups** (Группы регистраций) должно отобразиться название группы.  

       [![Группа регистраций на портале](./media/how-to-manage-enrollments/group-enrollment.png)]  (./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Отдельная регистрация** — это запись одного устройства, которое можно зарегистрировать. При аттестации таких устройств используются сертификаты X.509 или токены SAS (от физических или виртуальных доверенных платформенных модулей). Мы советуем использовать отдельные регистрации для устройств, которым требуются уникальные начальные конфигурации, или для устройств, которые могут использовать только маркеры SAS через TPM или виртуальный TPM в качестве механизма аттестации. Для отдельных регистраций можно указать нужный идентификатор устройства Центра Интернета вещей.

    Вы можете создать отдельную регистрацию на портале, выполнив указанные ниже действия:

    1. Войдите на портал Azure и выберите **Все ресурсы** в меню слева.
    2. Выберите службу подготовки устройств, в которой нужно зарегистрировать устройство, из списка ресурсов.
    3. В службе подготовки сделайте следующее:  
       a. Откройте раздел **Управление регистрациями**, а затем выберите вкладку **Индивидуальные регистрации**.  
       Б. Нажмите кнопку **Добавить** вверху.   
       c. Когда появится панель Add Enrollment (Добавление регистрации), введите сведения об элементе списка регистрации. Сначала выберите **механизм** аттестации для устройства (X.509 или доверенный платформенный модуль (TPM)). Для аттестации X.509 необходимо отправить **основной сертификат** конечного объекта для устройства. Для аттестации TPM необходимо указать **ключ аттестации** и **идентификатор регистрации** устройства.  
       d. Выберите команду **Сохранить**. При успешном создании группы регистраций под вкладкой **Individual Enrollments** (Отдельные регистрации) должно отобразиться устройство.  

       [![Индивидуальная регистрация на портале](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Обновление записи регистрации
Вы можете обновить имеющуюся запись регистрации на портале, выполнив указанные ниже действия:

1. Откройте службу подготовки устройств на портале Azure и выберите **Manage Enrollments** (Управление регистрациями). 
2. Перейдите к записи регистрации, которую необходимо изменить. Щелкните запись, после чего откроется сводная информация о регистрации устройства. 
3. На этой странице можно изменить элементы, отличные от типа безопасности и учетных данных, с которыми должно быть связано устройство Центра Интернета вещей, а также идентификатор устройства. Вы также можете изменить первоначальное состояние двойника устройства. 
4. После завершения выберите **Сохранить**, чтобы обновить регистрацию устройства. 

    ![Обновление регистрации на портале](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Удаление регистрации устройств
Если вам не нужно подготавливать устройства для Центра Интернета вещей, на портале можно удалить связанную запись регистрации, выполнив указанные ниже действия:

1. Откройте службу подготовки устройств на портале Azure и выберите **Manage Enrollments** (Управление регистрациями). 
2. Найдите и выберите запись регистрации, которую необходимо удалить. 
3. Нажмите кнопку **Удалить** в верхней части страницы, а затем — кнопку **Да** при появлении запроса на подтверждение. 
5. После завершения действия запись будет удалена из списка регистрации устройств. 
 
    ![Удаление регистрации на портале](./media/how-to-manage-enrollments/remove-enrollment.png)



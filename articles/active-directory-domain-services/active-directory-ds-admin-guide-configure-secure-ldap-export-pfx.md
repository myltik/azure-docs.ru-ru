---
title: Настройка защищенного протокола LDAP (LDAPS) в доменных службах Azure AD | Документация Майкрософт
description: Настройка защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 178302bd28e83da73590faf2be98fe5d2853ce56
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586891"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Настройка защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD

## <a name="before-you-begin"></a>Перед началом работы
Убедитесь, что вы выполнили [Задачу 1. Получение сертификата для защищенного протокола LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Задача 2. Экспорт сертификата защищенного протокола LDAP в PFX-файл
К выполнению этой задачи следует приступать только после получения сертификата защищенного протокола LDAP из общедоступного центра сертификации либо создания самозаверяющего сертификата.

Чтобы экспортировать сертификат LDAPS в PFX-файл, сделайте следующее.

1. Нажмите кнопку **Пуск** и введите **R**. В диалоговом окне **Выполнить** введите **mmc** и нажмите кнопку **ОК**.

    ![Запуск консоли MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. В окне **Контроль учетных записей пользователей** щелкните **ДА**, чтобы запустить консоль управления (MMC) от имени администратора.
3. В меню **Файл** выберите **Добавить или удалить оснастку…**.

    ![Добавление оснастки в консоль MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. В диалоговом окне **Добавление и удаление оснасток** выберите оснастку **Сертификаты** и нажмите кнопку **Добавить >**.

    ![Добавление оснастки диспетчера сертификатов в консоль MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. В мастере **Оснастка диспетчера сертификатов** установите переключатель в положение **Учетная запись компьютера** и нажмите кнопку **Далее**.

    ![Добавление оснастки диспетчера сертификатов для учетной записи компьютера](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. На странице **Выбор компьютера** установите переключатель в положение **Локальный компьютер (на котором выполняется оснастка)** и нажмите кнопку **Готово**.

    ![Добавление оснастки диспетчера сертификатов — выбор компьютера](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. В диалоговом окне **Добавление и удаление оснасток** нажмите кнопку **ОК**, чтобы добавить оснастку "Сертификаты" в MMC.

    ![Оснастка диспетчера сертификатов добавлена в консоль MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. В окне MMC разверните узел **Корень консоли**. Должна появиться загруженная оснастка диспетчера сертификатов. Разверните узел **Сертификаты (локальный компьютер)** . Разверните узел **Личное**, а затем узел **Сертификаты**.

    ![Открытие хранилища личных сертификатов](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. В окне должен отобразиться только что созданный самозаверяющий сертификат. Чтобы убедиться, что отпечаток совпадает с тем, который был указан в окнах PowerShell при создании сертификата, просмотрите свойства этого сертификата.
10. Выберите самозаверяющий сертификат и **щелкните его правой кнопкой мыши**. В контекстном меню последовательно выберите пункты **Все задачи** и **Экспорт…**.

    ![Экспорт сертификата](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. В **мастере экспорта сертификатов** нажмите кнопку **Далее**.

    ![Мастер экспорта сертификатов](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. На странице **Экспортирование закрытого ключа** установите переключатель в положение **Да, экспортировать закрытый ключ** и нажмите кнопку **Далее**.

    ![Экспорт закрытого ключа сертификата](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > НЕОБХОДИМО экспортировать закрытый ключ вместе с сертификатом. Включение защищенного LDAP для управляемого домена завершится ошибкой, если указать PFX-файл, в котором не содержится закрытый ключ для сертификата.
    >
    >
13. На странице **Формат экспортируемого файла** для экспортируемого сертификата установите переключатель в положение **Файл обмена личной информацией — PKCS #12 (.PFX)**.

    ![Формат файла экспортируемого сертификата](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Поддерживается только формат PFX. Не экспортируйте сертификат в формат CER.
    >
    >
14. На странице **Безопасность** установите флажок **Пароль** и введите пароль для защиты PFX-файла. Запомните этот пароль, так как он понадобится при выполнении следующей задачи. Нажмите кнопку **Далее** , чтобы продолжить.

    ![Пароль для экспортируемого сертификата ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Запомните этот пароль. Он потребуется при включении защищенного протокола LDAP для управляемого домена во время выполнения действий в разделе [Задача 3. Включение защищенного протокола LDAP для управляемого домена](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. На странице **Файл для экспорта** укажите имя файла и расположение, в которое необходимо экспортировать сертификат.

    ![Путь для экспорта сертификата](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. На следующей странице нажмите кнопку **Готово** , чтобы экспортировать сертификат в PFX-файл. После экспорта сертификата появится диалоговое окно подтверждения.

    ![Экспорт сертификата выполнен](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Дальнейшие действия
[Задача 3. Включение защищенного протокола LDAP для управляемого домена](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

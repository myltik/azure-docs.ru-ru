---
title: 'Azure AD Connect: параметры устройств | Документация Майкрософт'
description: В этом документе описаны параметры устройств, доступные в Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354529"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: параметры устройств

В этом документе содержится сведения о различных параметрах устройств, доступных в Azure AD Connect. С помощью Azure AD Connect можно настроить две следующие операции: 
* **Гибридное присоединение устройств к Azure AD**. Если в вашей среде действует локальная служба AD и вы также хотите воспользоваться возможностями Azure Active Directory, можно применить гибридные устройства, присоединенные к Azure AD. Это устройства, которые присоединены и к локальной среде Active Directory, и к Azure Active Directory.
* **Обратная запись устройств**. Обратная запись устройств используется для включения условного доступа для устройств в AD FS (2012 R2 или более поздней версии).

## <a name="configure-device-options-in-azure-ad-connect"></a>Настройка параметров устройств в Azure AD Connect

1.  Запустите Azure AD Connect. На странице **Дополнительные задачи** выберите **Настройка параметров устройств**.
    ![Настройка параметров устройств](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    После нажатия кнопки "Далее" откроется страница **Обзор** с информацией о возможных операциях.
    ![Обзор](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Новая функция настройки параметров устройств доступна только в версии 1.1.819.0 и более поздних.

2.  Указав учетные данные для Azure AD, на странице "Параметры устройств" можно выбрать операцию для выполнения.
    ![Операции с устройствами](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Дополнительная информация

* [Настройка гибридных устройств, присоединенных к Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Azure AD Connect: включение обратной записи устройств](./active-directory-aadconnect-feature-device-writeback.md)


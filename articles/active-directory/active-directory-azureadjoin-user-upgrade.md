---
title: "Настройка устройства с Windows 10 для работы с Azure AD с помощью меню &quot;Параметры&quot; | Документация Майкрософт"
description: "Сведения о том, как пользователи могут присоединиться к Azure AD, используя меню &quot;Параметры&quot;."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: b844e1d9-ad43-4e4a-a398-5c4a43bf2f5c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70610ec2c5a056d63ed854b33d84e29951940c2f


---
# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Настройка устройства с Windows 10 для работы с Azure AD с помощью меню «Параметры»
Если вы уже используете Windows 7 или Windows 8, и система на вашем компьютере или устройстве была обновлена до Windows 10, его можно присоединить к Azure Active Directory (Azure AD) через меню "Параметры".

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Присоединение к Azure AD с помощью меню "Параметры"
1. В меню **Пуск** выберите пункт **Параметры**.
2. В разделе **Параметры** выберите **Система**->**About** (О системе)->**Присоединиться к Azure AD**.
   
   <center>
   ![Присоединение к Azure AD с помощью меню "Параметры"](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>
3. В окне сообщения о присоединении к Azure AD нажмите кнопку **Продолжить** .
   
   <center>
   ![Окно сообщения о присоединении к Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Введите учетные данные для входа. На этом этапе вы выполните все действия, необходимые для прохождения аутентификации. Если вы входите в федеративный клиент, администратор вашей организации обеспечит возможность федеративного входа.
   <center>
   ![Ввод учетных данных для входа](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Если для присоединения к Azure AD в организации настроена Azure Multi-Factor Authentication, перед продолжением введите данные для второй части проверки.
6. На экране с запросом **Allow this device to be managed** (Разрешить управление этим устройством?) нажмите кнопку **Принять**.
7. Вы увидите сообщение "Теперь устройство присоединено к вашей организации в Azure AD".

## <a name="additional-information"></a>Дополнительная информация
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)
* [Проверка подлинности удостоверений без использования паролей с помощью службы Microsoft Passport](active-directory-azureadjoin-passport.md)




<!--HONumber=Nov16_HO3-->



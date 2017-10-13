---
title: "Условный доступ Azure Active Directory для подключения к виртуальной частной сети (предварительная версия) | Документация Майкрософт"
description: "Узнайте больше о том, как работает условный доступ Azure Active Directory для подключения к виртуальной частной сети. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>Условный доступ Azure Active Directory для подключения к виртуальной частной сети (предварительная версия)

Благодаря [условному доступу Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно настроить доступ авторизованных пользователей к вашим ресурсам. Условный доступ Azure AD для подключения к виртуальной частной сети (VPN) позволяет использовать условный доступ для защиты VPN-подключений.


Чтобы настроить условный доступ Azure AD для VPN-подключений, необходимо выполнить следующие действия: 

1.  Настройка VPN-сервера.
2.  Настройка VPN-клиента 
3.  Настройка политики условного доступа.
4.  Проверка


## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что вы знакомы со следующими статьями:

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN и условный доступ](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Вы также можете ознакомиться со статьей [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Усовершенствование удаленного доступа в Windows 10 с помощью автоматического VPN-профиля), чтобы узнать, как корпорация Майкрософт реализовала эту функцию.   


## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить условный доступ Azure Active Directory для подключения к виртуальной частной сети, необходимо настроить VPN-сервер. 



## <a name="step-1---configure-your-vpn-server"></a>Шаг 1. Настройка VPN-сервера 

Цель этого шага — настройка корневых сертификатов для проверки подлинности VPN с помощью Azure AD. Чтобы настроить условный доступ для подключения к виртуальной частной сети, необходимо:

1. Создать VPN-сертификат на портале Azure.
2. Загрузить VPN-сертификат.
2. Развернуть сертификат на VPN-сервере.

VPN-сертификат выступает в качестве издателя, используемого Azure AD для подписывания сертификатов, выпущенных для клиентов Windows 10, при проверке подлинности в Azure AD для VPN-подключения. Представьте, что запрашиваемый клиентом Windows 10 маркер — это сертификат,предоставляемый приложению, которое в данном случае является VPN-сервером.

![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

На портале Azure можно создать два сертификата: для управления переходами, когда срок действия сертификата заканчивается. При создании сертификата можно выбрать, является ли он основным. Именно основной сертификат используется во время проверки подлинности для подписания сертификата для подключения.


**Чтобы создать VPN-сертификат:**

1. Войдите на [портал Azure](https://portal.azure.com) с правами глобального администратора.

2. На панели навигации слева щелкните **Azure Active Directory**. 

    ![VPN-подключение](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![VPN-подключение](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. На странице **Условный доступ** в разделе **Управление** щелкните **VPN connectivity (preview)** (VPN-подключение (предварительная версия)).

    ![VPN-подключение](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. На странице **VPN connectivity** (VPN-подключение) щелкните **Новый сертификат**.

    ![VPN-подключение](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. На странице **Создать** выполните следующие действия:

    ![VPN-подключение](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    а. Для параметра **срока действия** выберите **1 год**.

    b. Для параметра **Основной** выберите **Да**.

    c. Щелкните **Создать**.

7. На странице VPN connectivity (VPN-подключение) нажмите кнопку **Скачать сертификат**.


На этом этапе вы готовы развернуть созданный сертификат на VPN-сервере. На VPN-сервере необходимо добавить загруженный сертификат как *сертификат доверенного корневого ЦС для проверки подлинности VPN*.

Для развертываний на основе Windows RRAS на NPS-сервере необходимо добавить корневой сертификат в хранилище *Enterprise NTauth*, выполнив следующие команды:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>Шаг 2. Настройка VPN-клиента 

На этом шаге необходимо настроить профиль подключения VPN-клиента, как описано в статье [VPN и условный доступ](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3---configure-your-conditional-access-policy"></a>Шаг 3. Настройка политики условного доступа

В этом разделе приводятся инструкции по настройке политики условного доступа для VPN-подключения.

**Чтобы настроить политику условного доступа:** 

1. На странице **Условный доступ** на панели инструментов сверху нажмите кнопку **Добавить**.

    ![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. На странице **Создать** в текстовом поле **Имя** введите имя политики, например **Политика VPN**.

    ![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. В разделе **Назначение** щелкните **Пользователи и группы**.

    ![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. На странице **Пользователи и группы** выполните следующие действия:

    ![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    а. Щелкните **Выбрать пользователей и группы**.

    b. Нажмите кнопку **Выбрать**.

    c. На странице **Выбор** выберите тестового пользователя и нажмите кнопку **Выбрать**.

    d. На странице **Пользователи и группы** нажмите кнопку **Готово**.

7. На странице **Создать** выполните следующие действия.

    ![Условный доступ](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    а. В разделе **Назначения** щелкните **Облачные приложения**.

    b. На странице **Облачные приложения** щелкните **Выбрать приложения**, а затем нажмите кнопку **Выбрать**.

    c. На странице **Выбор** в текстовом поле **Приложения** введите **vpn**.

    d. Выберите **VPN-сервер**.

    д. Нажмите кнопку **Выбрать**.


13. На странице **Создать** в разделе **Элементы управления** щелкните **Предоставить**, чтобы открыть страницу **Предоставить**.

    ![Условный доступ](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. На странице **Предоставить** выполните следующие действия:

    ![Условный доступ](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    а. Выберите **Требовать многофакторную проверку подлинности**.

    b. Нажмите кнопку **Выбрать**.

15. На странице **Создать** в разделе **Включить политику** нажмите кнопку **Вкл**.

    ![Условный доступ](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. На странице **Создать** щелкните **Создать**.



## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как корпорация Майкрософт реализовала эту функцию, ознакомьтесь со статьей [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Усовершенствование удаленного доступа в Windows 10 с помощью автоматического VPN-профиля).    


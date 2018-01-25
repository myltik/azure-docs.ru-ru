---
title: "Условный доступ Azure Active Directory для подключения к виртуальной частной сети (предварительная версия) | Документация Майкрософт"
description: "Узнайте, как работает условный доступ Azure Active Directory для подключения к виртуальной частной сети. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Условный доступ Azure Active Directory для подключения к виртуальной частной сети (предварительная версия)

[Условный доступ Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) предоставляет широкие возможности для настройки доступа авторизованных пользователей к ресурсам. Условный доступ Azure AD для подключения к виртуальной частной сети (VPN) помогает защитить VPN-подключения.


Чтобы настроить условный доступ для VPN-подключений, необходимо выполнить следующие действия. 

1.  Настройка VPN-сервера.
2.  Настройка VPN-клиента.
3.  Настройка политики условного доступа.


## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что вы знакомы со следующими темами:

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN и условный доступ](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Чтобы узнать, как корпорация Майкрософт реализовала эту функцию, ознакомьтесь со статьей [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Расширение возможностей удаленного доступа в Windows 10 с помощью автоматического VPN-профиля).   


## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить условный доступ Azure Active Directory для VPN-подключения, сначала следует настроить VPN-сервер. 



## <a name="step-1-configure-your-vpn-server"></a>Шаг 1. Настройка VPN-сервера 

На этом шаге вы настроите корневые сертификаты для аутентификации VPN-подключения через Azure AD. Чтобы настроить условный доступ для VPN-подключения, выполните следующие действия:

1. Создайте VPN-сертификат на портале Azure.
2. Скачайте этот VPN-сертификат.
2. Разверните сертификат на VPN-сервере.

Azure AD использует VPN-сертификат для подписывания сертификатов, выдаваемых клиентам Windows 10 при аутентификации VPN-подключения через Azure AD. Клиент Windows 10 запрашивает маркер, который выполняет роль сертификата и предъявляется приложению, в нашем примере — VPN-серверу.

![Скачивание сертификата для условного доступа](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

На портале Azure можно создать два сертификата, чтобы переключаться между ними по истечении срока действия одного из них. При создании сертификата вы можете указать, будет ли он считаться основным, то есть использоваться при аутентификации для подписывания сертификата подключения.

Процесс создания VPN-сертификата выглядит так:

1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор.

2. В меню слева щелкните **Azure Active Directory**. 

    ![Выберите Azure Active Directory.](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![Выбор элемента "Условный доступ"](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. На странице **Условный доступ** в разделе **Управление** щелкните **VPN connectivity (preview)** (VPN-подключение (предварительная версия)).

    ![Выбор элемента "VPN-подключение"](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. На странице **VPN connectivity** (VPN-подключение) щелкните **Новый сертификат**.

    ![Выбор элемента "Новый сертификат"](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. На странице **Создать** выполните следующие действия:

    ![Выбор срока действия и статуса основного сертификата](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Для параметра **Срок действия** выберите значение **1 год**.

    Б. Для параметра **Основной** выберите **Да**.

    c. Нажмите кнопку **Создать**.

7. На странице VPN connectivity (VPN-подключение) нажмите кнопку **Скачать сертификат**.


Итак, теперь вы можете развернуть созданный сертификат на VPN-сервере. На VPN-сервере добавьте скачанный сертификат как *сертификат доверенного корневого ЦС для проверки подлинности VPN*.

Если вы используете развертывание на основе Windows RRAS, добавьте корневой сертификат в хранилище *Enterprise NTauth* на NPS-сервере, выполнив следующие команды:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Шаг 2. Настройка VPN-клиента 

На этом шаге вы настроите профиль подключения для VPN-клиента, как описано в статье [VPN, и условный доступ](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Шаг 3. Настройка политики условного доступа

В этом разделе приводятся инструкции по настройке политики условного доступа для VPN-подключения.


1. На странице **Условный доступ** на панели инструментов сверху нажмите кнопку **Добавить**.

    ![Выбор элемента "Добавить" на странице условного доступа](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. На странице **Создать** в текстовом поле **Имя** введите имя политики. Например, **Политика VPN**.

    ![Добавление имени политики на странице условного доступа](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. В разделе **Назначение** щелкните **Пользователи и группы**.

    ![Выбор элемента "Пользователи и группы"](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. На странице **Пользователи и группы** выполните следующие действия:

    ![Выбор тестового пользователя](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Щелкните **Выбрать пользователей и группы**.

    Б. Нажмите кнопку **Выбрать**.

    c. На странице **Выбор** выберите тестового пользователя и нажмите кнопку **Выбрать**.

    d. На странице **Пользователи и группы** нажмите кнопку **Готово**.

7. На странице **Создать** выполните следующие действия:

    ![Выбор облачных приложений](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. В разделе **Назначения** щелкните **Облачные приложения**.

    Б. На странице **Облачные приложения** щелкните **Выбрать приложения**, а затем нажмите кнопку **Выбрать**.

    c. На странице **Выбор** в поле **Приложения** введите **vpn**.

    d. Выберите **VPN-сервер**.

    д. Нажмите кнопку **Выбрать**.


13. На странице **Создать** в разделе **Элементы управления** щелкните **Предоставить**, чтобы открыть страницу **Предоставить**.

    ![Выбор элемента "Предоставить"](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. На странице **Предоставить** выполните следующие действия:

    ![Выбор элемента "Требовать многофакторную проверку подлинности"](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Выберите **Требовать многофакторную проверку подлинности**.

    Б. Нажмите кнопку **Выбрать**.

15. На странице **Создать** в разделе **Включить политику** нажмите кнопку **Вкл**.

    ![Включение политики](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. На странице **Создать** щелкните **Создать**.



## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как корпорация Майкрософт реализовала эту функцию, ознакомьтесь со статьей [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Расширение возможностей удаленного доступа в Windows 10 с помощью автоматического VPN-профиля).    


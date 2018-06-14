---
title: Защита аутентификации RADIUS VPN-шлюза Azure с использованием NPS-сервера для обеспечения Многофакторной идентификации | Документация Майкрософт
description: Описывается интеграция аутентификации RADIUS шлюза Azure с NPS-сервером для обеспечения Многофакторной идентификации.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: 665e1914f44d7c5e650a1b632d8b11c6d8a5931a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793279"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Интеграция аутентификации RADIUS шлюза Azure с NPS-сервером для обеспечения Многофакторной идентификации 

В статье описано, как интегрировать сервер политики сети (NPS-сервер) с аутентификацией RADIUS VPN-шлюза Azure, чтобы обеспечить Многофакторную идентификацию (MFA) для VPN-подключений типа "точка — сеть". 

## <a name="prerequisite"></a>Предварительные требования

Для использования MFA пользователи должны быть добавлены в службу Azure Active Directory (Azure AD), которая должна синхронизироваться с локальной или облачной средой. Кроме того, пользователь должен выполнить процесс автоматической регистрации для использования MFA.  Дополнительные сведения см. в разделе [Настройка учетной записи для двухфакторной проверки подлинности](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="detailed-steps"></a>Подробные инструкции

### <a name="step-1-create-a-virtual-network-gateway"></a>Шаг 1. Создание шлюза виртуальной сети

1. Войдите на [портал Azure](https://portal.azure.com).
2. В виртуальной сети, в которой будет размещен шлюз виртуальной сети, выберите **Подсети**, а затем выберите **Подсеть шлюза**, чтобы создать подсеть. 

    ![Рисунок: добавление подсети шлюза](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Создайте шлюз виртуальной сети, указав следующие параметры.

    - **Тип шлюза**. Выберите **VPN**.
    - **Тип VPN**. Выберите **На основе маршрута**.
    - **SKU**. Выберите номер SKU в соответствии с требованиями.
    - **Виртуальная сеть**. Выберите виртуальную сеть, в которой была создана подсеть шлюза.

        ![Рисунок: параметры шлюза виртуальной сети](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Шаг 2. Настройка сервера политики сети для Azure MFA

1. [Установите расширение NPS для MFA Azure](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension) на NPS-сервере.
2. Откройте консоль NPS, щелкните правой кнопкой мыши **RADUIS Clients** (Клиенты RADUIS), а затем выберите **Создать**. Создайте клиент RADUIS, указав следующие параметры.

    - **Понятное имя**. Введите любое имя.
    - **Адрес (IP или DNS)**. Введите подсеть шлюза, созданную на шаге 1.
    - **Общий секрет**. Введите любой секретный ключ и запомните его для последующего использования.

    ![Рисунок: параметры клиента RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  На вкладке **Дополнительно** () укажите имя поставщика **RADIUS Standard** и убедитесь, что флажок **Дополнительные параметры** не установлен.

    ![Рисунок: дополнительные параметры клиента RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Выберите **Политики** > **Политики сети**, дважды щелкните политику **Подключения к серверу маршрутизации и удаленного доступа (Microsoft)** политики, выберите **Предоставить доступ**, а затем нажмите кнопку **ОК**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Шаг 3. Настройка шлюза виртуальной сети

1. Войдите на [портал Azure](https://portal.azure.com).
2. Откройте шлюз виртуальной сети, который вы создали. Убедитесь, что для него указан тип шлюза **VPN** и тип VPN **На основе маршрута**.
3. Выберите **Конфигурация " точка-сеть"** > **Настроить сейчас**, а затем укажите следующие параметры.

    - **Пул адресов**. Введите подсеть шлюза, созданную на шаге 1.
    - **Тип проверки подлинности**. Выберите **Проверка подлинности RADIUS**.
    - **IP-адрес сервера**. Введите IP-адрес NPS-сервера.

    ![Рисунок: параметры подключения типа "точка — сеть"](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Дополнительная информация

- [Многофакторная идентификация Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Интеграция имеющейся инфраструктуры NPS со службой Многофакторной идентификации Azure](../active-directory/authentication/howto-mfa-nps-extension.md)

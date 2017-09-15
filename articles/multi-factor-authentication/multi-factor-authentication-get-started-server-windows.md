---
title: "Проверка подлинности Windows и сервер Многофакторной идентификации Azure | Документация Майкрософт"
description: "Это страница Azure Multi-Factor Authentication, которая будет полезна при развертывании проверки подлинности Windows и сервера Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: e3ad33dd12b4fb831ba43738bc5af4e5561a0682
ms.contentlocale: ru-ru
ms.lasthandoff: 02/07/2017

---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Проверка подлинности Windows и сервер Azure Multi-Factor Authentication
Раздел проверки подлинности Windows сервера Многофакторной идентификации Azure позволяет включить и настроить проверку подлинности Windows для приложений. Перед тем как настроить проверку подлинности Windows, обратите внимание на следующий список.

* После завершения установки перезапустите Многофакторную идентификацию для служб терминалов, чтобы изменения вступили в силу.
* Если установлен флажок «Требуется сопоставление пользователей Multi-Factor Authentication» и вас нет в списке пользователей, после перезагрузки вы не сможете войти в систему.
* Надежные IP-адреса зависят от того, может ли приложение предоставлять IP-адрес клиента с проверкой подлинности. В настоящее время поддерживаются только службы терминалов.  

> [!NOTE]
> Для обеспечения безопасности служб терминалов в Windows Server 2012 R2 эта функция не поддерживается.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Для защиты приложения с помощью проверки подлинности Windows используйте следующую процедуру.
1. На сервере Многофакторной идентификации Azure щелкните значок проверки подлинности Windows.
   ![Проверка подлинности Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Установите флажок **Включить проверку подлинности Windows**. По умолчанию этот флажок не установлен.
3. На вкладке «Приложения» администратор может настроить одно или несколько приложений для проверки подлинности Windows.
4. Выберите сервер или приложение — укажите, включен ли сервер или приложение. Нажмите кнопку **ОК**.
5. Щелкните **Добавить…**
6. На вкладке «Надежные IP-адреса» можно пропустить Azure Multi-Factor Authentication для сеансов Windows с конкретных IP-адресов. Например, если сотрудники используют приложение как в офисе, так и дома, то можно сделать так, чтобы их телефоны не звонили для службы Azure Multi-Factor Authentication, пока они находятся в офисе. Для этого для офисной подсети установите значение «Надежные IP-адреса».
7. Щелкните **Добавить…**
8. Выберите **один IP-адрес**, если необходимо пропустить один IP-адрес.
9. Выберите **диапазон IP-адресов**, если необходимо пропустить целый диапазон IP-адресов. Пример: 10.63.193.1-10.63.193.100.
10. Выберите **подсеть**, если необходимо указать диапазон IP-адресов с помощью подсети. Введите начальный IP-адрес подсети и выберите соответствующую маску сети в раскрывающемся списке.
11. Нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия

- [Расширенные сценарии с использованием Многофакторной идентификации Azure и VPN-решений сторонних поставщиков](multi-factor-authentication-advanced-vpn-configurations.md)

- [Включение расширения NPS для Многофакторной идентификации Azure (общедоступная предварительная версия) в существующую инфраструктуру аутентификации](multi-factor-authentication-nps-extension.md)


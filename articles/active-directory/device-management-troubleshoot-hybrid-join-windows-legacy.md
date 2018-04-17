---
title: Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory | Документация Майкрософт
description: Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0d21a8848222c4b09723e22d2d51ec43b2154553
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory 

Это руководство касается только устройств под управлением следующих ОС: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Сведения об устройствах под управлением Windows 10 и Windows Server 2016 см. в статье [Troubleshooting hybrid Azure Active Directory joined Windows 10 and Windows Server 2016 devices](device-management-troubleshoot-hybrid-join-windows-current.md) (Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory).

В этом разделе предполагается, что вы [настроили гибридное присоединение устройств к Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) для поддержки следующих сценариев:

- Условный доступ на основе устройств

- [Корпоративное перемещение параметров](active-directory-windows-enterprise-state-roaming-overview.md)

- [Настройка Windows Hello для бизнеса](active-directory-azureadjoin-passport-deployment.md) 





В этом руководстве содержатся рекомендации по устранению неполадок для решения потенциальных проблем.  

**Важная информация** 

- Максимальное количество устройств для пользователя зависит от типа устройств. Например, если *jdoe* и *jharnett* войдут в устройство, на вкладке сведений **о пользователе** для каждого из них будет создана отдельная регистрация (DeviceID).  

- Первоначальная регистрация или присоединение устройств настраиваются, чтобы выполнить попытку входа в систему, заблокировать или разблокировать устройство. Возможна 5-минутная задержка, связанная с выполнением задачи планировщика задач. 

- При переустановке операционной системы или отмене регистрации либо повторной регистрации вручную в Azure AD может быть создана новая регистрация. Из-за этого на вкладке сведений о пользователе на портале Azure появляется несколько записей. 

## <a name="step-1-retrieve-the-registration-status"></a>Шаг 1. Получение сведений о состоянии регистрации 

**Чтобы проверить сведения о состоянии регистрации, сделайте следующее:**  

1. Запустите командную строку от имени администратора. 

2. Введите `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`.

Эта команда отображает диалоговое окно, содержащее дополнительные сведения о состоянии присоединения.

![Присоединение к рабочей области для Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Шаг 2. Оценка состояния гибридного присоединения к Azure AD 

Если не удалось выполнить гибридное присоединение к Azure AD, в диалоговом окне появятся подробные сведения о возникшей проблеме.

**Ниже перечислены наиболее распространенные проблемы.**

- Службы федерации Active Directory или служба Azure AD настроены неправильно

    ![Присоединение к рабочей области для Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Вы не вошли в качестве пользователя домена

    ![Присоединение к рабочей области для Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Это может происходить по нескольким причинам:
    
    1. Если пользователь, который выполнил вход, не является пользователем домена (например, локальный пользователь). Гибридное присоединение к Azure AD на низкоуровневых устройствах поддерживается только для пользователей домена.
    
    2. Если по какой-либо причине программе Autoworkplace.exe не удалось выполнить автоматическую аутентификацию с помощью Azure AD или AD FS. Причинами могут быть проблемы с исходящим сетевым подключением по URL-адресам Azure AD (проверьте предварительные требования) или ситуация, когда для пользователя включена или настроена многофакторная проверка подлинности, но настройка WIAORMUTLIAUTHN на сервере федерации не выполнена (проверьте действия по настройке). Другой вариант — страница обнаружения домашней области (HRD) ожидает взаимодействия с пользователем, что предотвращает автоматическое получение токена программой Autoworkplace.exe.
    
    3. Если в организации используется эффективный единый вход Azure AD, в настройках интрасети IE устройства отсутствует следующий URL-адрес:
    
       - https://autologon.microsoftazuread-sso.com

    
       Для зоны интрасети необходимо включить параметр "Разрешить обновление строки состояния в сценарии".

- Достигнут предел квоты

    ![Присоединение к рабочей области для Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Служба не отвечает 

    ![Присоединение к рабочей области для Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Сведения о состоянии можно также найти в журнале событий в разделе **Applications and Services Log\Microsoft-Workplace Join** (Журнал приложений и служб > Microsoft — присоединение к рабочей области).
  
**Наиболее распространенные причины сбоя гибридного присоединения к Azure AD:** 

- Компьютер может быть не подключен к внутренней сети организации или к VPN без подключения к локальному контроллеру домена AD.

- Вы вошли в систему с помощью учетной записи локального компьютера. 

- Проблемы конфигурации службы: 

  - сервер федерации настроен для поддержки **WIAORMULTIAUTHN**; 

  - нет объекта точки подключения службы, указывающего на имя проверенного домена в Azure AD в лесу AD, к которому относится компьютер;

  - пользователь достиг предела подключенных устройств. 

## <a name="next-steps"></a>Дополнительная информация

Ответы на вопросы можно найти в статье [Azure Active Directory device management FAQ](device-management-faq.md) (Часто задаваемые вопросы по управлению устройствами Azure Active Directory).  

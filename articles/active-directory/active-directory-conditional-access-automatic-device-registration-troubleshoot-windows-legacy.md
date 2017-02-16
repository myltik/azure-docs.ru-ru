---
title: "Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для клиентов Windows нижнего уровня | Документация Майкрософт"
description: "Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для клиентов Windows нижнего уровня."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 535badb5444fd4c29211f83556c0a750ee8adf70
ms.openlocfilehash: 0e031760b84aa59fefda2469606475b36dbc1ff7


---
# <a name="troubleshooting-the-auto-registration-of-azure-ad-domain-joined-computers-for-windows-down-level-clients"></a>Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для клиентов Windows нижнего уровня 

Это руководство касается только следующих клиентов: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Сведения о Windows 10 или Windows Server 2016 см. в статье [Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для Windows 10 и Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md).

В этом руководстве предполагается, что вы настроили автоматическую регистрацию присоединенных к домену устройств в соответствии со сведениями, указанными в статье [Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory](active-directory-conditional-access-automatic-device-registration-get-started.md).
 
В этом руководстве содержатся рекомендации по устранению неполадок для решения потенциальных проблем.  
Чтобы получить успешный результат, обратите внимание на следующее: 

- Регистрация клиентов в Azure AD выполняется для каждого пользователя или устройства. Например, если jdoe и jharnett войдут в устройство, в таблице сведений о пользователе для каждого из них будет создана отдельная регистрация (DeviceID).  

- Регистрация клиентов по умолчанию выполняется при входе или блокировке либо разблокировке. Эта операция может активироваться с помощью задачи планировщика задач с 5-минутной задержкой. 

- При переустановке операционной системы или отмене регистрации либо повторной регистрации вручную в Azure AD может быть создана новая регистрация. Из-за этого в таблице сведений о пользователе на портале Azure появится несколько записей. 


## <a name="step-1-retrieve-the-registration-status"></a>Шаг 1. Получение сведений о состоянии регистрации 

**Чтобы проверить сведения о состоянии регистрации, сделайте следующее:**  

1. Запустите командную строку от имени администратора. 

2. Введите `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`.

Эта команда отображает диалоговое окно, содержащее дополнительные сведения о состоянии присоединения.

![Присоединение к рабочей области для Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>Шаг 2. Анализ сведений о состоянии регистрации 

Если не удалось выполнить присоединение, в диалоговом окне появятся подробные сведения о возникшей проблеме.

**Ниже перечислены наиболее распространенные проблемы.**

- Службы федерации Active Directory или служба Azure AD настроены неправильно

    ![Присоединение к рабочей области для Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/02.png)

- Вы не вошли в качестве пользователя домена

    ![Присоединение к рабочей области для Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/03.png)

- Достигнут предел квоты

    ![Присоединение к рабочей области для Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/04.png)

- Служба не отвечает 

    ![Присоединение к рабочей области для Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/05.png)

Сведения о состоянии можно также найти в журнале событий в разделе **Applications and Services Log\Microsoft-Workplace Join** (Журнал приложений и служб > Microsoft — присоединение к рабочей области).
  
**Ниже перечислены основные причины сбоя регистрации.** 

- Компьютер может быть не подключен к внутренней сети организации или к VPN без подключения к локальному контроллеру домена AD.

- Вы вошли в систему с помощью учетной записи локального компьютера. 

- Проблемы конфигурации службы: 

  - сервер федерации настроен для поддержки **WIAORMULTIAUTHN**; 

  - нет объекта точки подключения службы, указывающего на имя проверенного домена в Azure AD в лесу AD, к которому относится компьютер;

  - пользователь достиг предела подключенных устройств. Дополнительные сведения см. в статье Get Started with Azure Active Device Registration (Знакомство с регистрацией устройств в Azure Active Directory).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Automatic device registration FAQ](active-directory-conditional-access-automatic-device-registration-faq.md) (Часто задаваемые вопросы об автоматической регистрации устройств) 


<!--HONumber=Feb17_HO1-->



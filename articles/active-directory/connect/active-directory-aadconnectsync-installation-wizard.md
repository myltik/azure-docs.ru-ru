---
title: "Повторное выполнение мастера установки Azure AD Connect | Документация Майкрософт"
description: "Объясняется, как работает мастер установки при повторном запуске."
keywords: "При повторном запуске мастера установки Azure AD Connect он позволяет настроить параметры обслуживания."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f18e11ac7482b78925d1885ceb20696146603ad2
ms.contentlocale: ru-ru
ms.lasthandoff: 03/28/2017

---
# Синхронизация Azure AD Connect sync: повторный запуск мастера установки
<a id="azure-ad-connect-sync-running-the-installation-wizard-a-second-time" class="xliff"></a>
При первом запуске мастера установки Azure AD Connect выполняется пошаговая настройка установки. При повторном запуске мастера установки предлагается настроить параметры обслуживания.

Мастер установки можно найти в меню "Пуск" с именем **Azure AD Connect**.

![Меню "Пуск"](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

При запуске мастера установки появится страница со следующими параметрами:

![Страница со списком дополнительных задач](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Если вы установили службы AD FS с Azure AD Connect, вам будет доступно еще больше возможностей. Дополнительные возможности служб AD FS описаны в разделе [Управление AD FS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Чтобы продолжить, выберите одну из задач и нажмите кнопку **Далее** .

> [!IMPORTANT]
> Когда открыт мастер установки, приостанавливаются все операции в модуле синхронизации. Убедитесь, что сразу после внесения изменений в конфигурацию вы завершили работу мастера установки.
>
>

## Просмотр текущей конфигурации
<a id="view-current-configuration" class="xliff"></a>
Этот параметр позволяет быстро просмотреть текущие настройки параметров.

![Страница со списком всех параметров и их состоянием](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Чтобы вернуться, нажмите кнопку **Назад** . Если выбрать **Выход**, окно мастера установки закроется.

## Настройка параметров синхронизации
<a id="customize-synchronization-options" class="xliff"></a>
Этот параметр используется для изменения конфигурации синхронизации. Вы увидите набор параметров из пути установки пользовательской конфигурации. Они отобразятся, даже если изначально использовались параметры экспресс-установки.

* [Добавить дополнительные каталоги](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Сведения об удалении каталога см. [здесь](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Изменить фильтрацию доменов и подразделений](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Удалить групповую фильтрацию.
* [Изменить дополнительные возможности](active-directory-aadconnect-get-started-custom.md#optional-features).

Другие параметры из начальной установки нельзя изменить, поэтому они недоступны. Доступны следующие параметры:

* Изменить атрибут, используемый для userPrincipalName и sourceAnchor.
* Изменить метод соединения для объектов из другого леса.
* Включить фильтрацию на основе группы.

## Обновление схемы каталога
<a id="refresh-directory-schema" class="xliff"></a>
Этот параметр используется при изменении схемы в одном из локальных лесов AD DS. Например, когда выполнена установка Exchange или обновление до схемы Windows Server 2012 с объектами устройства. В этом случае необходимо настроить Azure AD Connect на повторное чтение схемы из AD DS и обновление кэша. Это действие также повторно создает правила синхронизации. Если добавить, например, схему Exchange, то правила синхронизации для Exchange добавятся в конфигурацию.

При выборе этого параметра отобразятся все каталоги в конфигурации. Можно оставить значение по умолчанию и обновить все леса или отменить выбор некоторых из них.

![Страница со списком всех каталогов в среде](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## Настройка промежуточного режима
<a id="configure-staging-mode" class="xliff"></a>
Этот параметр позволяет включить и отключить на сервере промежуточный режим. Дополнительные сведения о промежуточном режиме и его использовании см. [здесь](active-directory-aadconnectsync-operations.md#staging-mode).

Рядом с параметром будет показано, включен или отключен промежуточный режим в данный момент:   
![Параметр, который также отображает текущее состояние промежуточного режима](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Для изменения состояния выберите этот параметр и установите или снимите флажок.  
![Параметр, который также отображает текущее состояние промежуточного режима](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## Изменение параметров входа пользователя
<a id="change-user-sign-in" class="xliff"></a>
Этот параметр позволяет заменить синхронизацию паролей на федерацию или наоборот. Нельзя изменить на значение **Не настраивать**.

Дополнительные сведения о входе пользователя см. [здесь](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
* Дополнительные сведения о модели конфигурации, используемой в синхронизации Azure AD Connect, см. в статье о [принципах декларативной подготовки](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Обзорные статьи**

* [Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)


---
title: Самостоятельная или пробная регистрация в Azure Active Directory | Документация Майкрософт
description: Узнайте, как использовать самостоятельную регистрацию в клиенте Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 67fd5ba9be2de1f79511c806ffaa0ae3001bfdcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33760295"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Что такое самостоятельная регистрация для Azure Active Directory?
В этой статье объясняется, что такое самостоятельная регистрация и как ее использовать в Azure Active Directory (Azure AD). Если требуется сменить доменное имя из неуправляемого клиента Azure AD, то см. статью [Смена неуправляемого каталога от имени администратора в Azure Active Directory](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Зачем нужна самостоятельная регистрация?
* Предоставить клиентам нужные службы как можно быстрее.
* Создать предложения на основе электронной почты для службы.
* Создать потоки регистрации на основе электронной почты, которые позволяют пользователям быстро создавать удостоверения с помощью легко запоминаемых псевдонимов рабочих электронных адресов.
* Самостоятельно созданный каталог Azure AD можно преобразовать в управляемый каталог, который может использоваться для других служб.

## <a name="terms-and-definitions"></a>Термины и определения
* **Самостоятельная регистрация** — метод, с помощью которого пользователь регистрируется в облачной службе, после чего для него в Azure AD на основании домена электронной почты автоматически создается удостоверение.
* **Неуправляемый каталог Azure AD** — каталог, где создается это удостоверение. Неуправляемый каталог — это каталог, в котором нет глобального администратора.
* **Проверяемый с помощью электронной почты пользователь**: тип учетной записи пользователя в Azure AD. Пользователь, который получает автоматически созданное после регистрации для предложения самообслуживания удостоверение, называется проверяемым с помощью электронной почты пользователем. Проверяемый с помощью электронной почты пользователь является регулярным участником каталога с тегом creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Как контролировать параметры самообслуживания?
На сегодняшний день у администраторов есть два способа самообслуживаемого управления. Они могут управлять:

* возможностью присоединения пользователей к каталогу с помощью электронной почты;
* лицензированием пользователями самих себя для приложений и служб.

### <a name="how-can-i-control-these-capabilities"></a>Как контролировать эти возможности?
Администратор может настраивать эти возможности с помощью следующих параметров командлета Azure AD Set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** отвечает за то, может ли пользователь создать или присоединить неуправляемый каталог. Если задать для этого параметра значение $false, учетные записи пользователей, проверяемые с помощью электронной почты, не смогут присоединиться к каталогу.
* **AllowAdHocSubscriptions** управляет самостоятельной регистрацией пользователей. Если задать для этого параметра значение $false, пользователи не смогут выполнять самостоятельную регистрацию. 
  
  > [!NOTE]
  > Вы не можете управлять пробными подписками Flow и PowerApps с помощью параметра **AllowAdHocSubscriptions**. Дополнительные сведения см. в следующих статьях:
  > * [Как запретить имеющимся пользователям начать работу с Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Вопросы и ответы об использовании Flow в организации](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Как совместно использовать эти элементы управления?
Эти два параметра можно использовать совместно для определения более точного управления самостоятельной регистрацией. Например, следующая команда позволит пользователям выполнять самостоятельную регистрацию, но только если у этих пользователей уже есть учетная запись в Azure AD (другими словами, пользователям нужно будет иметь проверенную с помощью электронной почты учетную запись, чтобы получить возможность выполнить самостоятельную регистрацию):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
На блок-схеме ниже объясняются различные сочетания этих параметров и результирующие условия для каталога и самостоятельной регистрации.

![][1]

Дополнительные сведения об этих параметрах и примеры их использования см. в статье [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Дополнительная информация
* [Добавление имени личного домена в Azure Active Directory](add-custom-domain.md)
* [Как установить и настроить Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Справка по командлетам Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

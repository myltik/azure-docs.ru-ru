---
title: "Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10 | Документация Майкрософт"
description: "Описание настройки групповой политики, которая позволяет присоединять устройства к домену в корпоративной сети."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.contentlocale: ru-ru
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10
Организации уже более 15 лет используют присоединение к домену для подключения устройств. Оно позволяет пользователям входить на устройства с помощью рабочих учетных записей Windows Server Active Directory (Active Directory), а ИТ-специалистам — полностью управлять этими устройствами. Организации обычно полагаются на создание образов для подготовки устройств для пользователей и используют для управления ими System Center Configuration Manager (SCCM) или групповые политики.


Присоединение к домену в Windows 10 обеспечивает следующие преимущества после подключения устройств к Azure Active Directory (Azure AD):

* единый вход для доступа к ресурсам Azure AD из любого расположения;
* доступ к корпоративному хранилищу Windows с помощью рабочих учетных записей (не требуется учетная запись Майкрософт);
* перенос пользовательских параметров между устройствами, которые используют рабочую учетную запись (с соблюдением корпоративных требований; не требуется учетная запись Майкрософт);
* строгая аутентификация и удобный вход с использованием рабочих или учебных учетных записей с помощью Windows Hello для бизнеса и Windows Hello;
* возможность ограничить доступ только для устройств, соответствующих параметрам групповой политики устройств организации.

## <a name="prerequisites"></a>Предварительные требования
Присоединение к домену доступно, как и раньше. Однако чтобы получить преимущества Azure AD в отношении единого входа, перемещения параметров с помощью рабочей учетной записи и доступа к Магазину Windows с рабочей учетной записью, потребуется следующее:

* Подписка Azure AD
* Azure AD Connect для расширения локального каталога до Azure AD;
* политика, настроенная для подключения к Azure AD устройств, присоединенных к домену;
* сборка Windows 10 (сборка 10551 или более поздней версии) для устройств.

Для включения Windows Hello для бизнеса и Windows Hello вам также потребуется следующее:

- **инфраструктура открытых ключей (PKI)** для выдачи сертификатов пользователей;

- **System Center Configuration Manager (текущая версия)**. Необходимо установить версию 1606 или более позднюю.  
Дополнительные сведения можно найти в разделе  
    - [Документация по System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx)
    - [Блог команды разработчиков Microsoft System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [Параметры Windows Hello для бизнеса с помощью System Center Configuration Manager](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

В качестве альтернативы для требования развертывания PKI можно реализовать следующую конфигурацию:

* Несколько контроллеров домена с доменными службами Active Directory Windows Server 2016.

Для включения условного доступа можно создать параметры групповой политики, разрешающие доступ к присоединенным к домену устройствам без дополнительных развертываний. Для управления доступом в зависимости от соответствия устройства требованиям необходимо следующее:

* Текущая версия System Center Configuration Manager (1606 и выше) для сценариев Windows Hello для бизнеса.

## <a name="deployment-instructions"></a>Инструкции по развертыванию

Инструкции по развертыванию см. в статье [Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="next-step"></a>Дальнейшие действия
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)



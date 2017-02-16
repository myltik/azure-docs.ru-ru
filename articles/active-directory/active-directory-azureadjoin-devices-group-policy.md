---
title: "Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10 | Документация Майкрософт"
description: "Описание настройки групповой политики, которая позволяет присоединять устройства к домену в корпоративной сети."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 7d141adf04cfb99e57c63ba62de4a7dad9ab8326
ms.openlocfilehash: 290645b920bc4a83c610e80266854450b6e1509a


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10
Организации уже более 15 лет используют присоединение к домену для подключения устройств. Оно позволяет пользователям входить на устройства с помощью рабочих учетных записей Windows Server Active Directory (Active Directory), а ИТ-специалистам — полностью управлять этими устройствами. Организации обычно полагаются на создание образов для подготовки устройств для пользователей и используют для управления ими System Center Configuration Manager (SCCM) или групповые политики.

Присоединение к домену в Windows 10 обеспечивает следующие преимущества после подключения устройств к Azure Active Directory (Azure AD):

* единый вход для доступа к ресурсам Azure AD из любого расположения;
* доступ к корпоративному хранилищу Windows с помощью рабочих учетных записей (не требуется учетная запись Майкрософт);
* перенос пользовательских параметров между устройствами, которые используют рабочую учетную запись (с соблюдением корпоративных требований; не требуется учетная запись Майкрософт);
* строгая проверка подлинности и удобный вход для рабочих учетных записей с помощью Microsoft Passport и Windows Hello;
* возможность ограничить доступ только для устройств, соответствующих параметрам групповой политики устройств организации.

## <a name="prerequisites"></a>Предварительные требования
Присоединение к домену доступно, как и раньше. Однако чтобы получить преимущества Azure AD в отношении единого входа, перемещения параметров с помощью рабочей учетной записи и доступа к Магазину Windows с рабочей учетной записью, потребуется следующее:

* Подписка Azure AD
* Azure AD Connect для расширения локального каталога до Azure AD;
* политика, настроенная для подключения к Azure AD устройств, присоединенных к домену;
* сборка Windows 10 (сборка 10551 или более поздней версии) для устройств.

Для включения Microsoft Passport for Work и Windows Hello также потребуется следующее:

* инфраструктура открытых ключей (PKI) для выдачи сертификатов пользователей;
* System Center Configuration Manager (ознакомительная техническая версия 1509). Дополнительные сведения см. в разделе [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Это необходимо для развертывания сертификатов пользователей на основе ключей Microsoft Passport.

В качестве альтернативы для требования развертывания PKI можно реализовать следующую конфигурацию:

* Несколько контроллеров домена с доменными службами Active Directory Windows Server 2016.

Для включения условного доступа можно создать параметры групповой политики, разрешающие доступ к присоединенным к домену устройствам без дополнительных развертываний. Для управления доступом в зависимости от соответствия устройства требованиям необходимо следующее:

* System Center Configuration Manager (ознакомительная техническая версия 1509) для сценариев с использованием Microsoft Passport.

## <a name="deployment-instructions"></a>Инструкции по развертыванию

Инструкции по развертыванию см. в статье [Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="additional-information"></a>Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO4-->



---
title: 'Гибридная идентификация: сравнение инструментов интеграции каталогов | Документация Майкрософт'
description: На этой странице приведено исчерпывающее сравнение различных инструментов, которые можно использовать для интеграции каталогов.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: 5d189af9b08f2b6e9ea194c15bfba683afc75a54
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Сравнение инструментов интеграции каталогов гибридной идентификации
На протяжении многих лет инструменты интеграции каталогов развивались и расширялись.  Этот документ предназначен, чтобы помочь предоставить единое представление об этих инструментах и сравнение функций, доступных в каждом из них.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect применяет компоненты и функции, ранее выпущенные как Dirsync и служба синхронизации AAD. Они больше не выпускаются по отдельности. Все усовершенствования будут включаться в обновления для Azure AD Connect, поэтому вам всегда будут доступны самые последние функции.
> 
> Компоненты DirSync и Azure AD Sync являются устаревшими. Дополнительные сведения см. [здесь](active-directory-aadconnect-dirsync-deprecated.md).
> 
> 

Воспользуйтесь следующей подсказкой для каждой таблицы.

● — доступно сейчас;  
FR — в следующем выпуске;  
PP — в общедоступной предварительной версии.  

## <a name="on-premises-to-cloud-synchronization"></a>Синхронизация из локальной среды в облако
| Функция | Подключение Azure Active Directory | Службы синхронизации Azure Active Directory (AAD Sync) | Средство синхронизации Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Подключение к одному локальному лесу AD |● |● |● |● |● |
| Подключение к нескольким локальным лесам AD |● |● | |● |● |
| Подключение к нескольким локальным организациям Exchange |● | | | | |
| Подключение к одному локальному каталогу LDAP | | | |● |● |
| Подключение к нескольким локальным каталогам LDAP |  | | |● |● |
| Подключение к локальным каталогам AD и LDAP | | | |● |● |
| Подключение к пользовательским системам (т. е. SQL, Oracle, MySQL и т. д.) |СВ | | |● |● |
| Синхронизация определенных клиентом атрибутов (расширения каталогов) |● | | | | |
| Подключение к локальной системе управления персоналом (SAP, Oracle eBusiness, PeopleSoft) |СВ | | |● |● |
| Поддержка правил синхронизации FIM и соединителей для подготовки к локальным системам | | | |● |● |


## <a name="cloud-to-on-premises-synchronization"></a>Синхронизации из облака в локальную среду
| Функция | Подключение Azure Active Directory | Службы синхронизации Azure Active Directory | Средство синхронизации Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Обратная запись устройств |● | |● | | |
| Обратная запись атрибутов (для гибридного развертывания Exchange) |● |● |● |● |● |
| Обратная запись объектов групп |● | | | | |
| Обратная запись паролей (от самостоятельного сброса пароля (SSPR) и смены пароля) |● |● | | | |

## <a name="authentication-feature-support"></a>Поддержка функции аутентификации
| Функция | Подключение Azure Active Directory | Службы синхронизации Azure Active Directory | Средство синхронизации Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Синхронизация паролей для одного локального леса AD |● |● |● | | |
| Синхронизация паролей для нескольких локальных лесов AD |● |● | | | |
| Единый вход с помощью федерации |● |● |● |● |● |
| Обратная запись паролей (от самостоятельного сброса пароля и смены пароля) |● |● | | | |

## <a name="set-up-and-installation"></a>Установка и настройка
| Функция | Подключение Azure Active Directory | Службы синхронизации Azure Active Directory | Средство синхронизации Azure Active Directory (DirSync) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Поддержка установки на контроллере домена |● |● |● | |
| Поддержка установки с помощью SQL Express |● |● |● | |
| Простота обновления из DirSync |● | | | |
| Локализация интерфейса администратора на языки Windows Server |● |● |● | |
| Локализация интерфейса конечного пользователя на языки Windows Server | | | |● |
| Поддержка Windows Server 2008 и Windows Server 2008 R2 |● для синхронизации, не для федерации |● |● |● |
| Поддержка Windows Server 2012 и Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Фильтрация и конфигурация
| Функция | Подключение Azure Active Directory | Службы синхронизации Azure Active Directory | Средство синхронизации Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Фильтрация по доменам и подразделениям |● |● |● |● |● |
| Фильтрация по значениям атрибутов объектов |● |● |● |● |● |
| Разрешение синхронизации минимального набора атрибутов (MinSync) |● |● | | | |
| Разрешение применения различных шаблонов служб к потокам атрибутов |● |● | | | |
| Разрешение удаления атрибутов из потока из AD в Azure AD |● |● | | | |
| Разрешение дополнительной настройки потоков атрибутов |● |● | |● |● |

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).


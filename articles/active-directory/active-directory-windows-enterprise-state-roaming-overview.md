<properties
	pageTitle="Обзор службы Enterprise State Roaming | Microsoft Azure"
	description="Здесь содержится информация о параметрах Enterprise State Roaming для устройств Windows. Служба Enterprise State Roaming представляет собой единое решение для всех устройств Windows и сокращает процесс настройки нового устройства."
	services="active-directory"
    keywords="что такое Enterprise State Roaming, корпоративная синхронизация, облако Windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Обзор службы Enterprise State Roaming

Используя Windows 10, пользователи [Azure Active Directory (Azure AD)](active-directory-whatis.md) получают возможность безопасно синхронизировать свои параметры пользователя и данные параметров приложения в облаке. Служба Enterprise State Roaming представляет собой единое решение для всех устройств Windows и сокращает процесс настройки нового устройства. Enterprise State Roaming работает так же, как и стандартная [синхронизация параметров потребителя](http://windows.microsoft.com/ru-RU/windows-8/sync-settings-pcs), которая впервые представлена в Windows 8. Кроме того, Enterprise State Roaming обеспечивает следующее:

- **Разделение корпоративных и потребительских данных**. Организации управляют своими данными, поэтому корпоративные данные не попадают в облачную учетную запись потребителя, а данные потребителя — в корпоративную облачную учетную запись. 
- **Повышенная безопасность**. Перед отправкой с устройства Windows 10 данные шифруются с помощью службы управления правами Azure (Azure RMS) и остаются зашифрованными в облаке. Все содержимое, хранящееся в облаке, остается зашифрованным, кроме пространств имен, таких как имена параметров и приложений Windows.  
- **Управление службами и наблюдение за ними**. Дополнительные контроль и видимость, позволяющие определить, кто синхронизирует параметры в организации и на каких устройствах. 
- **Географическое расположение данных в облаке**. Данные будут храниться в регионе Azure с учетом страны, в которой расположен домен Azure AD. 



| Статья | Описание |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Включение службы Enterprise State Roaming в Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) | Служба Enterprise State Roaming доступна для любой компании с подпиской Premium Azure Active Directory (Azure AD). Дополнительные сведения о том, как получить подписку Azure AD, см. на странице о продукте Azure AD. |
| [Часто задаваемые вопросы о перемещении параметров и данных](active-directory-windows-enterprise-state-roaming-faqs.md) | В этой статье содержится информация о синхронизации параметров и данных приложений, которая может быть полезной для ИТ-администраторов. |
| [Group policy and MDM settings for settings sync (Параметры групповой политики и управления мобильными устройствами)](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 предоставляет параметры групповой политики и управления мобильными устройствами (MDM) для ограничения синхронизации параметров. |
| [Справочник по перемещаемым параметрам в Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | Ниже приведен полный список всех параметров, для которых в Windows 10 будет выполнено перемещение и/или резервное копирование. |

<!---HONumber=AcomDC_0204_2016-->
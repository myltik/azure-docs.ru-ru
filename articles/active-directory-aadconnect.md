<properties pageTitle="Подключение Azure Active Directory" description="Мастер подключения Azure Active Directory - это универсальное средство и руководство по подключению локального сервера Windows к Azure Active Directory" services="active-directory" documentationCenter="" authors="Gayana" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="02/27/2015" ms.author="gabag" />

<h1 id="vnettut1">Подключение Azure Active Directory</h1>

Мастер подключения Azure Active Directory - это универсальное средство и руководство по подключению локальных каталогов к Azure Active Directory  Мастер производит развертывание и настраивает все необходимые компоненты для интеграции и запуска каталогов, включая службы синхронизации, синхронизацию паролей или службы федерации Active Directory (AD FS), а также такие необходимые компоненты, как модуль Azure AD PowerShell.

>[AZURE.NOTE] **Подключение Azure Active Directory включает в себя функции, которые раньше выполняли службы синхронизации DirSync и AAD Sync. Эти инструменты больше не выпускаются по отдельности.** 

> **Подключение Azure Active Directory теперь представляет собой единую службу для синхронизации, входа и других функций локальных серверов с интеграцией с Azure AD.**


Если вы сейчас анализируете или используете средство синхронизации Azure Active Directory (DirSync), службу синхронизации Azure Active Directory (AAD Sync) или Forefront Identity Manager 2010 R2, дополнительные сведения см. в разделе [Средства интеграции каталогов](http://msdn.microsoft.com/library/azure/dn757582.aspx "Directory Integration Tools").


##  Предварительная версия подключения Azure Active Directory 

Текущая предварительная версия подключения Azure Active Directory включает руководство по интеграции одного или нескольких лесов Windows Server Active Directory с Azure AD. 

[Скачать предварительную версию подключения Azure AD](http://connect.microsoft.com/site1164/program8612 "Azure Active Directory Connect") 

В предварительной версии подключения Azure Active Directory выполните следующее. 

- Выберите "Стандартные параметры" для быстрой и простой настройки одного леса за 4 щелчка мыши.
- Выберите "Пользовательские параметры" для настройки нескольких лесов или AD FS для единого входа (SSO).
- Настройте дополнительные параметры синхронизации, например "Exchange", в гибридном режиме, обратную запись паролей и альтернативные атрибуты идентификатора.

##  Дополнительные ресурсы
[Документация Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/)

<!--HONumber=47-->

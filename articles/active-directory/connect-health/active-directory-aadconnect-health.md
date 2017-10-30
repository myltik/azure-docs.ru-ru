---
title: "Мониторинг локальной инфраструктуры идентификации в облаке."
description: "В этой статье описаны принципы работы и предназначение службы Azure AD Connect Health."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 24552ef153f5aa601b2998129a7bbacc8f9da6bf
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Мониторинг локальной инфраструктуры идентификации и служб синхронизации в облаке.
Служба Azure Active Directory (Azure AD) Connect Health помогает отслеживать локальную инфраструктуру идентификации и службы синхронизации. Она обеспечивает надежное подключение к службам Office 365 и Microsoft Online Services и предоставляет возможности мониторинга ключевых компонентов идентификации, таких как серверы служб федерации Active Directory (AD FS), серверы Azure AD Connect (также называемые модулями синхронизации), контроллеры домена Active Directory и т. п. С помощью этой службы пользователи могут с легкостью получить доступ к общим сведениям о компонентах, в частности к информации об использовании и другим важным данным, необходимым для принятия обоснованных решений.

Эти сведения можно найти на портале [Azure AD Connect Health](https://aka.ms/aadconnecthealth). На портале Azure AD Connect Health можно просматривать оповещения, отслеживать производительность, аналитику использования и другие сведения. Служба Azure AD Connect Health позволяет следить за состоянием работоспособности ключевых компонентов идентификации из одного места.

![Что такое Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Так как возможности службы Azure AD Connect Health расширяются, на портале предоставлена одна панель мониторинга для просмотра идентификации, предоставляющая вам еще более надежную, работоспособную и интегрированную среду. Ее могут использовать ваши пользователи для повышения собственной продуктивности.
## <a name="why-use-azure-ad-connect-health"></a>Зачем использовать Azure AD Connect Health?
Интеграция локальных каталогов с Azure AD помогает повысить продуктивность ваших пользователей, предоставляя им единую идентификацию для доступа к облачным и локальным ресурсам. Однако такая интеграция требует обеспечения работоспособности среды для надежного доступа пользователей к локальным и облачным ресурсам с любого устройства. Azure AD Connect Health позволяет выполнять мониторинг и получать четкое представление о локальной инфраструктуре идентификации, которая используется для доступа к Office 365 и другим приложениям Azure AD. Использовать службу так же просто, как установить агент на локальных серверах удостоверений.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health для AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health для AD FS поддерживает AD FS 2.0 в Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2. Поддерживается также мониторинг прокси-серверов AD FS и веб-приложений, которые обеспечивают проверку подлинности для доступа к экстрасети. Azure AD Connect Health для AD FS предоставляет следующий набор основных возможностей:

* мониторинг и отправка оповещений о проблемах работоспособности AD FS и прокси-серверов AD FS;
* уведомления по электронной почте для критических оповещений;
* анализ тенденций в данных производительности, что удобно для планирования ресурсов AD FS;
* аналитика по входам в систему AD FS с предоставлением сводных данных (о приложениях, пользователях, сетевом расположении и т. д.), которые позволяют понять, как используется AD FS;
* создание отчетов AD FS, например о 50 пользователях, выполнивших наибольшее количество неудачных попыток входа с последнего IP-адреса с указанием неправильного имени пользователя или пароля.
  
Дополнительные сведения см. в статье [Мониторинг AD FS с помощью Azure AD Connect Health](active-directory-aadconnect-health-adfs.md).

В следующем видео представлен обзор Azure AD Connect Health для AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>

>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health для синхронизации](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health для синхронизации отслеживает и предоставляет сведения о синхронизациях, которые выполняются между вашей локальной службой Active Directory и Azure AD. Azure AD Connect Health для синхронизации предоставляет следующие возможности:

* мониторинг и отправка оповещений о проблемах работоспособности серверов Azure AD Connect (также называемых модулями синхронизации);
* уведомления по электронной почте для критических оповещений;
* оперативная аналитика синхронизации, включая диаграммы задержки для операций синхронизации и тренды различных операций, таких как добавление, обновление и удаление;
* предоставление общей информации о свойствах синхронизации и последнем успешном экспорте в Azure AD.
* Для получения отчетов об ошибках синхронизации на уровне объекта \((не требуется Azure AD Premium)\).

Дополнительные сведения см. в статье [Мониторинг синхронизации Azure AD Connect с помощью Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

В следующем видео представлен обзор Azure AD Connect Health для синхронизации.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health для AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health для доменных служб Active Directory (AD DS) обеспечивает мониторинг контроллеров домена, установленных в Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016. Установка агента работоспособности позволит отслеживать локальную среду AD DS из облака. Azure AD Connect Health для AD DS предоставляет следующий набор основных возможностей.

* Отслеживание оповещений для обнаружения неработоспособных контроллеров домена, а также уведомления по электронной почте о критических оповещениях.
* Панель мониторинга контроллеров домена, на которой можно быстро просмотреть состояние работоспособности и рабочее состояние контроллеров домена.
* Панель мониторинга состояния репликации с последними сведениями о репликации, а также ссылки на руководства по устранению неполадок при обнаружении ошибок.
* Быстрый доступ из любого расположения к графикам данных производительности популярных счетчиков производительности, необходимых для наблюдения и устранения неполадок.

Дополнительные сведения см. в статье [Использование Azure AD Connect Health с AD DS](active-directory-aadconnect-health-adds.md).

В следующем видео представлен обзор Azure AD Connect Health для AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Приступая к работе с Azure AD Connect Health
Чтобы начать работу с Azure AD Connect Health, сделайте следующее:

1. [Приобретите Azure AD Premium](../active-directory-get-started-premium.md) или [получите пробную версию](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Скачайте и установите агенты Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) на серверах удостоверений.
3. Просмотрите панель мониторинга Azure AD Connect Health на странице [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Помните, чтобы на панели мониторинга Azure AD Connect Health отображались какие-либо данные, потребуется установить агенты Azure AD Connect Health на целевых серверах.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Скачивание и установка агента Azure AD Connect Health
* Обязательно [выполните требования](active-directory-aadconnect-health-agent-install.md#requirements) для Azure AD Connect Health.
* Приступая к работе с Azure AD Connect Health для AD FS
    * [Скачайте агент Azure AD Connect Health для AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Ознакомьтесь с инструкциями по установке.](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Приступая к работе с Azure AD Connect Health для синхронизации
    * [Скачайте и установите последнюю версию Azure AD Connect.](http://go.microsoft.com/fwlink/?linkid=615771) Агент Azure AD Connect Health для синхронизации будет установлен вместе с Azure AD Connect (версии 1.0.9125.0 или более поздней).
* Приступая к работе с Azure AD Connect Health для AD DS
    * [Скачайте агент Azure AD Connect Health для AD FS.](http://go.microsoft.com/fwlink/?LinkID=820540)
    * [Ознакомьтесь с инструкциями по установке.](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)

## <a name="azure-ad-connect-health-portal"></a>Портал Azure AD Connect Health
На портале Azure AD Connect Health можно просматривать оповещения, отслеживать производительность и аналитику использования. URL-адрес https://aka.ms/aadconnecthealth перенаправит вас в главную колонку Azure AD Connect Health. Ее можно считать окном. В главной колонке отображается элемент **быстрого запуска**, раздел со службами Azure AD Connect Health и дополнительные параметры конфигурации. Просмотрите снимок экрана колонки ниже и ознакомьтесь с кратким описанием содержащихся в ней элементов. После развертывания агентов служба работоспособности автоматически определит службы, отслеживаемые в Azure AD Connect Health.

> [!NOTE]
> Сведения о лицензировании см. в статье [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md) или [на странице цен на Azure AD](https://aka.ms/aadpricing).
    
![Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Быстрый запуск.** При выборе этого элемента откроется колонка **быстрого запуска**. Вы можете скачать агент Azure AD Connect Health, выбрав **Получить средства**. Кроме того, вы также можете получить доступ к документации и оставить отзыв.
* **Службы федерации Active Directory.** Этот раздел позволяет просмотреть все службы AD FS, которые в настоящее время отслеживает служба Azure AD Connect Health. Если выбрать экземпляр, откроется колонка со сведениями об этом экземпляре службы. Сюда входит обзор, свойства, оповещения, мониторинг и аналитика по использованию. Дополнительные сведения о возможностях см. в статье [Использование Azure AD Connect Health для синхронизации](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (Sync).** В этом разделе отображаются серверы Azure AD Connect, которые в настоящее время отслеживает служба Azure AD Connect Health. Когда вы выберете запись, откроется колонка с информацией о серверах Azure AD Connect. Дополнительные сведения о возможностях см. в статье [Использование Azure AD Connect Health для синхронизации](active-directory-aadconnect-health-sync.md).
* **Доменные службы Active Directory.** В этом разделе представлены все леса AD DS, которые в настоящее время отслеживает служба Azure AD Connect Health. Если выбрать лес, откроется колонка со сведениями об этом лесе. Эти сведения включают обзор основной информации, панель мониторинга контроллеров домена, панель мониторинга состояния репликации, оповещения и мониторинг. Дополнительные сведения о возможностях см. в статье [Использование Azure AD Connect Health с AD DS](active-directory-aadconnect-health-adds.md).
* **Настройка.** В этом разделе можно включить или отключить следующие функции.

  - Автоматическое обновление агента Azure AD Connect Health до последней версии. Агент Azure AD Connect Health будет обновлен до последней версии, когда она станет доступна. Эта функция включена по умолчанию.
  - Предоставление корпорации Майкрософт доступа к данным работоспособности каталога Azure AD только в целях устранения неполадок. Если эта функция включена, корпорации Майкрософт будут доступны те же данные, что видите вы. Эта информация может помочь при устранении неполадок и различных проблем. Эта функция отключена по умолчанию.

## <a name="related-links"></a>Связанные ссылки
* [Установка агента Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Операции Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Использование Azure AD Connect Health с AD FS](active-directory-aadconnect-health-adfs.md)
* [Использование Azure AD Connect Health для синхронизации](active-directory-aadconnect-health-sync.md)
* [Using Azure AD Connect Health with AD DS (Использование Azure AD Connect Health с AD DS)](active-directory-aadconnect-health-adds.md)
* [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: история выпусков версий](active-directory-aadconnect-health-version-history.md)

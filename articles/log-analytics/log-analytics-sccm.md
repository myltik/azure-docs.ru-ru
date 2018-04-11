---
title: Подключение Configuration Manager к Log Analytics | Документация Майкрософт
description: В этой статье описаны действия, с помощью которых можно подключить Configuration Manager к Log Analytics и начать анализ данных.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 5ff0687fe99f0853e29e5f0d814a8555c367027c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Подключение Configuration Manager к Log Analytics
Среду System Center Configuration Manager можно подключить к Azure Log Analytics для синхронизации данных коллекций устройств и указания этих коллекций в Log Analytics и службе автоматизации Azure.  

## <a name="prerequisites"></a>предварительным требованиям

Поддержка в Log Analytics текущей версии System Center Configuration Manager, версии 1606 или более поздних версий.  

## <a name="configuration-overview"></a>Общие сведения о настройке
Ниже приведены действия для настройки интеграции Configuration Manager с Log Analytics.  

1. На портале Azure зарегистрируйте Configuration Manager как веб-приложение и (или) приложение веб-API. Затем убедитесь, что у вас есть идентификатор клиента и секретный ключ клиента, полученные при регистрации в Azure Active Directory. Подробные сведения о выполнении этого шага см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md).
2. На портале Azure [предоставьте Configuration Manager (зарегистрированному веб-приложению) разрешение на доступ к Log Analytics](#grant-configuration-manager-with-permissions-to-log-analytics).
3. В Configuration Manager [добавьте подключение с помощью мастера добавления подключений к OMS](#add-an-oms-connection-to-configuration-manager).
4. В случае утери пароля или секретного ключа клиента либо истечения срока их действия вы можете [обновить свойства подключения](#update-oms-connection-properties) в Configuration Manager.
5. [Скачайте и установите Microsoft Monitoring Agent](#download-and-install-the-agent) на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager. Агент отправляет данные Configuration Manager в рабочую область Log Analytics.
6. В Log Analytics [импортируйте коллекции из Configuration Manager](#import-collections) как группы компьютеров.
7. В Log Analytics просмотрите данные из Configuration Manager как [группы компьютеров](log-analytics-computer-groups.md).

Дополнительные сведения о подключении Configuration Manager к OMS см. в статье [Sync data from Configuration Manager to the Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx) (Синхронизация данных из Configuration Manager в Microsoft Operations Management Suite).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Предоставление Configuration Manager разрешений для Log Analytics
В следующей процедуре вы предоставляете роль *Участник* в рабочей области Log Analytics для приложения AD и субъекта-службы, созданных ранее для Configuration Manager.  Если у вас еще нет рабочей области, [создайте ее в Log Analytics](log-analytics-quick-create-workspace.md), прежде чем продолжить.  Это позволит Configuration Manager проходить аутентификацию и подключаться к рабочей области Log Analytics.  

> [!NOTE]
> Необходимо указать для Configuration Manager разрешения на доступ к Log Analytics. В противном случае при использовании мастера конфигурации в Configuration Manager вы получите сообщение об ошибке.
>

1. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.<br><br> ![портал Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. В списке рабочих областей Log Analytics выберите рабочую область, которую требуется изменить.
3. В левой области выберите **Управление доступом (IAM)**.
4. На странице "Управление доступом" щелкните **Добавить**. Откроется область **Добавление разрешений**.
5. В области **Добавление разрешений** из раскрывающегося списка **Роли** выберите роль **Участник**.  
6. Из раскрывающегося списка **Назначение доступа к** выберите приложение Configuration Manager, ранее созданное в AD, и нажмите кнопку **ОК**.  

## <a name="download-and-install-the-agent"></a>Загрузка и установка агента
Ознакомьтесь со статьей [Создание рабочей области Log Analytics на портале Azure](log-analytics-agent-windows.md), чтобы понять, какие методы доступны для установки Microsoft Monitoring Agent на компьютере, на котором размещена роль системы сайта для точки подключения службы Configuration Manager.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Добавление подключения к OMS в Configuration Manager
Чтобы добавить подключение к OMS, необходимо настроить [точку подключения службы](https://technet.microsoft.com/library/mt627781.aspx) в среде Configuration Manager для работы в оперативном режиме.

1. В рабочей области **Администрирование** в Configuration Manager выберите **Соединитель OMS**. Откроется **мастер добавления подключений к OMS**. Щелкните **Далее**.
2. На экране **Общие** убедитесь, что вы выполнили следующие действия и указали сведения по каждому пункту, а затем щелкните **Далее**.

   1. На портале Azure вы зарегистрировали Configuration Manager как веб-приложение и (или) приложение веб-API, и у вас есть [идентификатор клиента, полученный при регистрации](../active-directory/active-directory-integrating-applications.md).
   2. На портале Azure вы создали секретный ключ приложения для зарегистрированного приложения в Azure Active Directory.  
   3. На портале Azure вы указали зарегистрированное веб-приложение с разрешением на доступ к OMS.  
      ![Страница "Общие" мастера подключений к OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. На экране **Azure Active Directory** настройте параметры подключения к Log Analytics, указав **клиент**, **идентификатор клиента** и **секретный ключ клиента**, а затем нажмите кнопку **Далее**.  
   ![Страница "Azure Active Directory" мастера подключений к OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Если вы успешно выполнили все остальные процедуры, сведения, указанные на экране **OMS Connection Configuration** (Конфигурация подключения к OMS), автоматически появятся на этой странице. Параметры подключения должны отобразиться в вашей **подписке Azure**, **группе ресурсов Azure** и **рабочей области Operations Management Suite**.  
   ![Страница "Подключение" мастера подключений к OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. Мастер подключится к службе Log Analytics, используя указанные вами сведения. Выберите коллекции устройств, которые требуется синхронизировать со службой, а затем нажмите кнопку **Добавить**.  
   ![Выбор коллекций](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Проверьте параметры подключения на экране **Сводка**, а затем щелкните **Далее**. На экране **Ход выполнения** отображается состояние подключения (должно отобразиться состояние **Выполнено**).

> [!NOTE]
> К Log Analytics необходимо подключить сайт верхнего уровня в иерархии. Если подключить к Log Analytics изолированный первичный сайт, а затем добавить в среду сайт центра администрирования, потребуется удалить и заново создать подключение в новой иерархии.
>
>

Установив связь между Configuration Manager и Log Analytics, можно добавлять или удалять коллекции и просматривать свойства подключения.

## <a name="update-log-analytics-connection-properties"></a>Обновление свойств подключения к Log Analytics
В случае утери пароля или секретного ключа клиента либо истечения срока их действия необходимо вручную обновить свойства подключения к Log Analytics.

1. В Configuration Manager перейдите к разделу **Облачные службы**, а затем выберите **Соединитель OMS**, чтобы открыть страницу **OMS Connection Properties** (Свойства подключения к OMS).
2. На этой странице щелкните **Azure Active Directory**, чтобы просмотреть сведения о **клиенте**, **идентификаторе клиента** и **сроке действия секретного ключа клиента**. **Проверьте** **секретный ключ клиента**, если срок его действия истек.

## <a name="import-collections"></a>Импорт коллекций
Добавив подключение к OMS в Configuration Manager и установив агент на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager, можно переходить к следующему шагу — импорту коллекций из Configuration Manager в Log Analytics как групп компьютеров.

После завершения начальной настройки для импорта коллекций устройств из иерархии сведения о членстве в коллекции извлекается каждые 3 часа для обеспечения актуальности сведений о членстве. Это можно отключить в любое время.

1. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
2. Из списка рабочих областей Log Analytics выберите ту, в которой зарегистрировано приложение Configuration Manager.  
3. Выберите **Дополнительные параметры**.<br><br> ![Дополнительные параметры Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Выберите **Группы компьютеров**, а затем щелкните **SCCM**.  
5. Выберите **Импортировать членства в коллекциях Configuration Manager** и нажмите кнопку **Сохранить**.  
   ![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Просмотр данных из Configuration Manager
Когда вы добавите подключение к OMS в Configuration Manager и установите агент на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager, данные будут отправляться из агента в Log Analytics. В Log Analytics коллекции из Configuration Manager отображаются как [группы компьютеров](log-analytics-computer-groups.md). Группы можно просматривать на странице **Configuration Manager**, выбрав **Параметры > Группы компьютеров**.

После импорта коллекций можно увидеть число обнаруженных компьютеров с членством в коллекциях. Также можно увидеть число импортированных коллекций.

![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/log-analytics-sccm/sccm-computer-groups02.png)

Если щелкнуть одну из них, откроется окно поиска, в котором отображаются либо все импортированные группы, либо все компьютеры, принадлежащие к каждой группе. С помощью [поиска по журналам](log-analytics-log-searches.md) можно выполнить подробный анализ данных Configuration Manager.

## <a name="next-steps"></a>Дополнительная информация
* Воспользуйтесь [поиском по журналам](log-analytics-log-searches.md) для просмотра подробных сведений о данных Configuration Manager.

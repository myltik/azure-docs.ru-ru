---
title: "Подключение Configuration Manager к Log Analytics | Документация Майкрософт"
description: "В этой статье описаны действия, с помощью которых можно подключить Configuration Manager к Log Analytics и начать анализ данных."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: 240db424645778c0fcd0f8abd17411e58bd95ab9


---
# <a name="connect-configuration-manager-to-log-analytics"></a>Подключение Configuration Manager к Log Analytics
System Center Configuration Manager можно подключить к Log Analytics в OMS, чтобы синхронизировать данные коллекции устройств. Так можно сделать данные из развертывания Configuration Manager доступными в OMS.

Для подключения Configuration Manager к OMS необходимо выполнить ряд шагов. Вот краткое описание всего процесса:

1. На портале управления Azure зарегистрируйте Configuration Manager как веб-приложение и (или) приложение веб-API. Затем убедитесь, что у вас есть идентификатор клиента и секретный ключ клиента, полученные при регистрации в Azure Active Directory. Подробные сведения о выполнении этого шага см. в статье [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../resource-group-create-service-principal-portal.md).
2. На портале управления Azure [укажите Configuration Manager (зарегистрированное веб-приложение) с разрешением на доступ к OMS](#provide-configuration-manager-with-permissions-to-oms).
3. В Configuration Manager [добавьте подключение с помощью мастера добавления подключений к OMS](#add-an-oms-connection-to-configuration-manager).
4. В случае утери пароля или секретного ключа клиента либо истечения срока их действия вы можете [обновить свойства подключения](#update-oms-connection-properties) в Configuration Manager.
5. Руководствуясь сведениями на портале OMS, [скачайте и установите Microsoft Monitoring Agent](#download-and-install-the-agent) на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager. Агент отправляет данные Configuration Manager в OMS.
6. В OMS [импортируйте коллекции из Configuration Manager](#import-collections) как группы компьютеров.
7. В OMS просмотрите данные из Configuration Manager [как группы компьютеров](log-analytics-computer-groups.md).

Дополнительные сведения о подключении Configuration Manager к OMS см. в статье [Sync data from Configuration Manager to the Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx) (Синхронизация данных из Configuration Manager в Microsoft Operations Management Suite).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Предоставление Configuration Manager разрешений в OMS
С помощью приведенной ниже процедуры можно предоставить порталу управления Azure разрешения для доступа к OMS. В рамках этой процедуры, в частности, необходимо назначить *роль участника* пользователям в группе ресурсов. В свою очередь, это позволит порталу управления Azure подключить Configuration Manager к OMS.

> [!NOTE]
> Необходимо указать разрешения на доступ к OMS для Configuration Manager. В противном случае при использовании мастера конфигурации в Configuration Manager вы получите сообщение об ошибке.
>
>

1. Откройте [портал Azure](https://portal.azure.com/) и щелкните **Обзор** > **Log Analytics (OMS)**, чтобы открыть колонку Log Analytics (OMS).  
2. В колонке **Log Analytics (OMS)** щелкните **Добавить**, чтобы открыть колонку **OMS Workspace** (Рабочая область OMS).  
   ![Колонка OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. В колонке **OMS Workspace** (Рабочая область OMS) укажите следующие сведения и нажмите кнопку **ОК**:

   * **рабочая область OMS**;
   * **Подписка**
   * **Группа ресурсов**
   * **Расположение**
   * **ценовая категория**.  
     ![Колонка OMS Workspace (Рабочая область OMS)](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > В примере выше создается новая группа ресурсов. В нашем примере она нужна только для того, чтобы предоставить Configuration Manager разрешения для рабочей области OMS.
     >
     >
4. Щелкните **Обзор** > **Группы ресурсов**, чтобы открыть колонку **Группы ресурсов**.
5. В колонке **Группы ресурсов** щелкните группу ресурсов, созданную выше, чтобы открыть колонку "&lt;Имя группы ресурсов&gt; — Параметры".  
   ![Колонка с параметрами группы ресурсов](./media/log-analytics-sccm/sccm-azure03.png)
6. В колонке "&lt;Имя группы ресурсов&gt; — Параметры" щелкните "Управление доступом (IAM)", чтобы открыть колонку "&lt;Имя группы ресурсов&gt; — Пользователи".  
   ![Колонка "Пользователи" для группы ресурсов](./media/log-analytics-sccm/sccm-azure04.png)  
7. В колонке "&lt;Имя группы ресурсов&gt; — Пользователи" щелкните **Добавить**, чтобы открыть колонку **Добавить доступ**.
8. В колонке **Добавить доступ** щелкните **Выбрать роль** и выберите роль **Участник**.  
   ![Выбор роли](./media/log-analytics-sccm/sccm-azure05.png)  
9. Щелкните **Добавить пользователей**, выберите пользователя Configuration Manager, щелкните **Выбрать** и нажмите кнопку **ОК**.  
   ![Добавление пользователей](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Добавление подключения к OMS в Configuration Manager
Чтобы добавить подключение к OMS, необходимо настроить [точку подключения службы](https://technet.microsoft.com/library/mt627781.aspx) в среде Configuration Manager для работы в оперативном режиме.

1. В рабочей области **Администрирование** в Configuration Manager выберите **Соединитель OMS**. Откроется **мастер добавления подключений к OMS**. Щелкните **Далее**.
2. На экране **Общие** убедитесь, что вы выполнили следующие действия и указали сведения по каждому пункту, а затем щелкните **Далее**.

   1. На портале управления Azure вы зарегистрировали Configuration Manager как веб-приложение и (или) приложение веб-API, и у вас есть [идентификатор клиента, полученный при регистрации](../active-directory/active-directory-integrating-applications.md).
   2. На портале управления Azure вы создали секретный ключ приложения для зарегистрированного приложения в Azure Active Directory.  
   3. На портале управления Azure вы указали зарегистрированное веб-приложение с разрешением на доступ к OMS.  
      ![Страница "Общие" мастера подключений к OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. На экране **Azure Active Directory** настройте параметры подключения к OMS, указав **клиент**, **идентификатор клиента** и **секретный ключ клиента**, а затем щелкните **Далее**.  
   ![Страница "Azure Active Directory" мастера подключений к OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Если вы успешно выполнили все остальные процедуры, сведения, указанные на экране **OMS Connection Configuration** (Конфигурация подключения к OMS), автоматически появятся на этой странице. Параметры подключения должны отобразиться в вашей **подписке Azure**, **группе ресурсов Azure** и **рабочей области Operations Management Suite**.  
   ![Страница "Подключение" мастера подключений к OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. Мастер подключится к службе OMS, используя указанные вами сведения. Выберите коллекции устройств, которые требуется синхронизировать с OMS, а затем нажмите кнопку **Добавить**.  
   ![Выбор коллекций](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Проверьте параметры подключения на экране **Сводка**, а затем щелкните **Далее**. На экране **Ход выполнения** отображается состояние подключения (должно отобразиться состояние **Выполнено**).

> [!NOTE]
> OMS необходимо подключить к сайту верхнего уровня в иерархии. Если подключить OMS к автономному первичному сайту, а затем добавить в среду сайт центра администрирования, вам придется удалить и заново создать подключение к OMS в новой иерархии.
>
>

Установив связь между Configuration Manager и OMS, можно добавить или удалить коллекции и просмотреть свойства подключения к OMS.

## <a name="update-oms-connection-properties"></a>Обновление свойств подключения к OMS
В случае утери пароля или секретного ключа клиента либо истечения срока их действия необходимо вручную обновить свойства подключения к OMS.

1. В Configuration Manager перейдите к разделу **Облачные службы**, а затем выберите **Соединитель OMS**, чтобы открыть страницу **OMS Connection Properties** (Свойства подключения к OMS).
2. На этой странице щелкните **Azure Active Directory**, чтобы просмотреть сведения о **клиенте**, **идентификаторе клиента** и **сроке действия секретного ключа клиента**. **Проверьте** **секретный ключ клиента**, если срок его действия истек.

## <a name="download-and-install-the-agent"></a>Загрузка и установка агента
1. На портале OMS [скачайте файл установки агента из OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Чтобы установить и настроить агент на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager, используйте один из следующих методов:
   * [установка агента с помощью программы установки](log-analytics-windows-agents.md#install-the-agent-using-setup);
   * [установка агента с помощью командной строки](log-analytics-windows-agents.md#install-the-agent-using-the-command-line);
   * [установка агента с помощью DSC в службе автоматизации Azure](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation).

## <a name="import-collections"></a>Импорт коллекций
Добавив подключение к OMS в Configuration Manager и установив агент на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager, можно переходить к следующему шагу — импорту коллекций из Configuration Manager в OMS как групп компьютеров.

После включения импорта данные о членстве в коллекциях извлекаются каждые 3 часа. Так обеспечивается актуальность сведений о членстве в коллекциях. Импорт можно отключить в любое время.

1. На портале OMS щелкните **Параметры**.
2. Перейдите на вкладку **Группы компьютеров**, а затем выберите вкладку **SCCM**.
3. Выберите **Импортировать членства в коллекциях Configuration Manager** и нажмите кнопку **Сохранить**.  
   ![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Просмотр данных из Configuration Manager
Когда вы добавите подключение к OMS в Configuration Manager и установите агент на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager, данные будут отправляться из агента в OMS. В OMS коллекции из Configuration Manager будут отображаться как [группы компьютеров](log-analytics-computer-groups.md). Группы можно просматривать на странице **Configuration Manager**, выбрав вкладку **Группы компьютеров** в разделе **Параметры**.

После импорта коллекций можно увидеть число обнаруженных компьютеров с членством в коллекциях. Также можно увидеть число импортированных коллекций.

![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/log-analytics-sccm/sccm-computer-groups02.png)

Если щелкнуть одну из них, откроется окно поиска, в котором отображаются либо все импортированные группы, либо все компьютеры, принадлежащие к каждой группе. С помощью [поиска по журналам](log-analytics-log-searches.md) можно выполнить подробный анализ данных Configuration Manager.

## <a name="next-steps"></a>Дальнейшие действия
* Воспользуйтесь [поиском по журналам](log-analytics-log-searches.md) для просмотра подробных сведений о данных Configuration Manager.



<!--HONumber=Nov16_HO3-->



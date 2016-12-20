---
title: "Заметки о выпуске каталога данных Azure | Документация Майкрософт"
description: "Заметки о выпуске каталога данных Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/21/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 60abdc490cefc7362ca6ed0dec7dbfd53984201f


---
# <a name="azure-data-catalog-release-notes"></a>Заметки о выпуске каталога данных Azure
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Заметки о выпуске каталога данных Azure от 20 ноября 2015 г.
### <a name="opening-data-sources-in-power-bi-desktop"></a>Открытие источников данных в Power BI Desktop
При использовании параметра «Открыть в Power BI Desktop» на портале **каталога данных Azure** у пользователей может возникнуть одна из двух проблем в приложении Power BI Desktop:

* Отображается диалоговое окно с заголовком «Не удается в открыть документ».
* Приложение Power BI Desktop открывается, но файл отображается пустым.

В каждом случае проблему можно устранить, скачав и установив последнюю версию Power BI Desktop с сайта [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Заметки о выпуске каталога данных Azure от 13 ноября 2015 г.
### <a name="registering-and-connecting-to-teradata"></a>Регистрация и подключение к Teradata
При подключении к источникам данных Teradata у пользователей должны быть установлен драйвер ODBC Teradata, соответствующий разрядности (32-разрядной или 64-разрядной версии) используемого программного обеспечения.

На дату выпуска этого соединителя Active Directory самый последний [драйвер ODBC Teradata для Windows (вер. 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) совместим с Office 2013, но не с Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Заметки о выпуске каталога данных Azure от 13 июля 2015 г.
### <a name="registering-and-connecting-to-oracle-database"></a>Регистрация и подключение к базе данных Oracle
При подключении к источникам данных базы данных Oracle у пользователей должны быть установлены правильные драйверы Oracle, которые соответствуют разрядности (32-разрядной или 64-разрядной версии) используемого программного обеспечения.

* При регистрации источников данных Oracle на компьютере под управлением 32-разрядной версии Windows будут использоваться 32-разрядные драйверы Oracle.
* При регистрации источников данных Oracle на компьютере под управлением 64-разрядной версии Windows будут использоваться 64-разрядные драйверы Oracle.
* При подключении к источникам данных Oracle с помощью Excel на компьютере с 32-разрядной версией Microsoft Office, в том числе под управлением 64-разрядной версии Windows, будут использоваться 32-разрядные драйверы Oracle.
* При подключении к источникам данных Oracle с помощью Excel на компьютере с 64-разрядной версией Microsoft Office будут использоваться 64-разрядные драйверы Oracle.

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Регистрация и подключение к SQL Server Reporting Services
Поддержка источников данных служб SQL Server Reporting Services (SSRS) в настоящее время ограничена только серверами в собственном режиме. Поддержка служб SSRS в режиме интеграции с SharePoint будет добавлена в более поздней версии.

### <a name="opening-data-assets-in-excel"></a>Открытие ресурсов данных в Excel
При открытии ресурсов данных в Microsoft Excel на портале **каталога данных Azure** пользователям может быть отображено диалоговое окно **Microsoft Excel Security Notice** (Уведомление безопасности Microsoft Excel). Это стандартное ожидаемое поведение, и пользователи могут выбрать **Включить** для продолжения.

Подробнее об этом см. в статье [Включение или отключение предупреждений системы безопасности о ссылках и файлах с подозрительных веб-сайтов](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Конфигурация прокси-сервера и политики, а также регистрация источника данных
Пользователи могут столкнуться с ситуацией, когда они могут войти на портал каталога данных Azure, но при попытке входа на средство регистрации источника данных отображается сообщение об ошибке, которая запрещает вход в систему.

Существуют две возможные причины такого проблемного поведения.

**Причина 1: конфигурация служб федерации Active Directory**. Инструмент регистрации источника данных использует аутентификацию с помощью форм для проверки входа пользователя, используя данные Active Directory. Для успешного входа в систему администратор Active Directory должен включить проверку подлинности форм в глобальную политику проверки подлинности.

В некоторых ситуациях эта ошибка может происходить только в том случае, если пользователь находится в локальной сети, или только в том случае, если пользователь подключается из-за пределов сети компании. Глобальная политика проверки подлинности позволяет включить методы проверки подлинности отдельно для интрасети и экстрасети. Ошибки входа в систему могут возникать, если не включена проверка подлинности форм для сети, из которой пользователь подключается.

Подробнее: [Настройка политик проверки подлинности](https://technet.microsoft.com/library/dn486781.aspx).

**Причина 2: конфигурация прокси-сервера сети**. Если в корпоративной сети используется прокси-сервер, инструмент регистрации не может подключиться к Azure Active Directory через прокси-сервер. Пользователи могут проверить средство регистрации, изменив файл конфигурации средства и добавив в файл этот раздел.

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Чтобы найти файл RegistrationTool.exe.config, запустите средство регистрации и откройте программу диспетчера задач Windows. На вкладке "Сведения" в диспетчере задач щелкните правой кнопкой мыши RegistrationTool.exe и выберите во всплывающем меню "Открыть расположение файла".




<!--HONumber=Nov16_HO3-->



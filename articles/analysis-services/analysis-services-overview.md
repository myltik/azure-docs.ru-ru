---
title: "Что такое службы Azure Analysis Services | Документация Майкрософт"
description: "Общие сведения о службах Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3539da8afe5781d74cbf723090050b767373f268
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-azure-analysis-services"></a>Службы Azure Analysis Services
![Службы Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Службы Azure Analysis Services, созданные на основе проверенного аналитического модуля в Microsoft SQL Server Analysis Services, позволяют моделировать данные корпоративного класса в облаке. 

Просмотрите это видео, чтобы узнать больше о том, как службы Azure Analysis Services дополняют общие возможности средств бизнес-аналитики корпорации Майкрософт и как воспользоваться преимуществами переноса семантических моделей в облако.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Сейчас доступна **предварительная версия** служб Azure Analysis Services. Некоторые возможности еще не реализованы. Обязательно ознакомьтесь с разделом [Ожидаемые возможности предварительной версии](#preview-expectations) в этой статье. Кроме того, следите за последними новостями в [блоге по Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>На основе SQL Server Analysis Services
Службы Azure Analysis Services совместимы с уже знакомыми вам службами SQL Server 2016 Analysis Services выпуска Enterprise Edition. Службы Azure Analysis Services поддерживают табличные модели на уровне совместимости 1200. Кроме того, также поддерживаются DirectQuery, секции, безопасность на уровне строк, двунаправленные связи и переводы.

## <a name="use-the-tools-you-already-know"></a>Использование знакомых инструментов
![Средства разработчика бизнес-аналитики](./media/analysis-services-overview/aas-overview-dev-tools.png)

При создании моделей данных для служб Analysis Services Azure можно использовать те же средства, что и для служб SQL Server Analysis Services. Для создания и развертывания табличной модели можно использовать последние версии [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) или [Azure Powershell](/powershell/azureps-cmdlets-docs) и шаблоны [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) в [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Подключение к источникам данных
Модели данных, развернутые на серверах в Azure, поддерживают подключение к локальным и облачным источникам данных. Вы также можете получить гибридное решение бизнес-аналитики, объединив данные из локальных и облачных источников.

![Источники данных](./media/analysis-services-overview/aas-overview-data-sources.png)

Так как сервер находится в облаке, подключение к облачным источникам данных не вызывает затруднений. При подключении к локальным источникам данных [локальный шлюз данных](analysis-services-gateway.md) обеспечивает быстрое и надежное соединение с сервером служб Analysis Services в облаке.  

 \* Некоторые источники данных пока не поддерживаются в предварительной версии. Дополнительные сведения см. в разделе [Ожидаемые возможности предварительной версии](#preview-expectations) этой статьи.

## <a name="explore-your-data-from-anywhere"></a>Анализ данных из любого расположения
Подключайтесь к серверам и [получайте данные](analysis-services-connect.md), используя любое средство или программу. Службы Azure Analysis Services поддерживают подключение из Power BI Desktop, Excel, пользовательских приложений и средств, использующих браузер.

![Визуализации данных](./media/analysis-services-overview/aas-overview-visualization.png)

 \* Power BI Embedded пока не поддерживается в предварительной версии.

## <a name="secure"></a>Безопасность
#### <a name="user-authentication"></a>Аутентификация пользователей
Проверка подлинности пользователей в Azure Analysis Services осуществляется через [Azure Active Directory (AD)](../active-directory/active-directory-whatis.md). При входе в базу данных служб Azure Analysis Services пользователи используют удостоверение рабочей учетной записи с соответствующими правами доступа. Эти учетные записи должны быть участниками каталога Azure Active Directory по умолчанию для подписки, где находится сервер служб Azure Analysis Services. [Интеграция каталогов](https://technet.microsoft.com/library/jj573653.aspx) между AAD и локальной службой Active Directory — отличный способ предоставить локальным пользователям доступ к базе данных служб Azure Analysis Services, но он не требуется для всех сценариев.

Во время входа пользователи вводят имя участника-пользователя учетной записи и пароль. При синхронизации с локальной службой Active Directory имя участника-пользователя часто соответствует адресу электронной почты организации.

Разрешения пользователей на управление ресурсами сервера Analysis Services Azure зависят от назначенной им роли в подписке Azure. По умолчанию администраторы подписки получают права владельца ресурсов сервера Azure. С помощью Azure Resource Manager можно добавить дополнительных пользователей.

#### <a name="data-security"></a>Защита данных
Для хранения данных и метаданных базы данных службы Azure Analysis Services используется хранилище BLOB-объектов Azure. Файлы данных в большом двоичном объекте шифруются с использованием шифрования на стороне сервера. При использовании режима прямого запроса сохраняются только метаданные. Доступ к фактическим данным осуществляется через источник данных во время выполнения запроса.

#### <a name="on-premises-data-sources"></a>Локальные источники данных
Чтобы обеспечить безопасный доступ к локальным данным в вашей организации, нужно установить и настроить [локальный шлюз данных](analysis-services-gateway.md). Шлюзы предоставляют доступ к данным через прямые запросы и в режиме "в памяти". Когда модель Azure Analysis Services подключается к локальному источнику данных, формируется запрос и создаются соответствующие зашифрованные учетные данные. Облачная служба шлюза анализирует запрос и отправляет его в служебную шину Azure. Локальный шлюз опрашивает служебную шину Azure, чтобы проверить наличие ожидающих запросов. Затем шлюз получает запрос, расшифровывает учетные данные и подключается к источнику данных для выполнения запроса. Результаты отправляются из источника данных обратно в шлюз, а затем — в базу данных Azure Analysis Services.

Использование служб Azure Analysis Services регулируется [условиями](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) и [заявлением о конфиденциальности служб Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Дополнительные сведения о центре безопасности Azure см. [здесь](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Получение справки
Службы Azure Analysis Services просты в настройке и управлении. Здесь представлены все сведения, необходимые для создания сервера и управления им. Процедуры создания модели данных для развертывания на удаленном и локальном сервере практические ничем не отличаются. В разделе [служб Analysis Services на сайте MSDN](https://msdn.microsoft.com/library/bb522607.aspx) есть обширная библиотека статей с общими сведениями и инструкциями, а также руководств и справочных статей.

[На сайте Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) содержится ряд полезных видео об Azure Analysis Services.

Обновления вносятся очень часто. Вы всегда можете узнать последние новости в [блоге по Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Сообщество
Уже есть активное сообщество пользователей служб Analysis Services. Присоединяйтесь к обсуждению на [форуме по Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Отзыв
У вас есть предложения или идеи касательно функций? Оставляйте свои комментарии на [этой странице](https://aka.ms/azureanalysisservicesfeedback).

У вас есть предложения относительно документации? Чтобы добавить комментарии, щелкните надпись Disqus в нижней части каждой статьи.

## <a name="preview-expectations"></a>Ожидаемые возможности предварительной версии
Сейчас доступна предварительная версия служб Azure Analysis Services. Существует несколько моментов, на которые следует обратить внимание.

##### <a name="server-modes"></a>Режимы сервера
Сейчас службы Azure Analysis Services поддерживают табличный режим для табличных моделей на уровне совместимости 1200. Многомерная модель, а также режим интеллектуального анализа данных и PowerPivot для SharePoint не поддерживаются.

##### <a name="data-sources"></a>Источники данных
В следующей таблице приведены источники данных, которые поддерживаются в табличных моделях 1200, развернутых на сервере служб Azure Analysis Services предварительной версии.

| **Облако** | **Локальный (требуется шлюз данных)** |
| --- | --- |
| База данных SQL |SQL Server |
| Хранилище данных SQL |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>Поставщики источников данных
Чтобы подключиться к источникам данных, для моделей данных в Azure Analysis Services могут требоваться не те поставщики данных, что для SQL Server Analysis Services. Требования к поставщику данных зависят от источника данных (облачного или локального) и типа модели данных ("в памяти" или режим прямых запросов). Дополнительные сведения см. в статье [Datasource connections](analysis-services-datasource.md) (Подключение к источникам данных).

### <a name="client-connections"></a>Клиентские подключения

Клиентским приложениям требуется последняя версия [поставщиков данных](analysis-services-data-providers.md) клиента для подключения к службам Azure Analysis Services.

Книги Excel с активными подключениями к серверу служб Azure Analysis Services, а также книги, сохраненные в OneDrive или SharePoint Online, не поддерживаются.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы достаточно знаете о службах Azure Analysis Services, пора приступать к работе. Узнайте, как [создать сервер](analysis-services-create-server.md) в Azure и [развернуть на нем табличную модель](analysis-services-deploy.md).



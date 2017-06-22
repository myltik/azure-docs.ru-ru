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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: cc85ae1c6642e763e57327c151ec68845f7673f7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-azure-analysis-services"></a>Службы Azure Analysis Services
![Службы Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Службы Azure Analysis Services, созданные на основе проверенного аналитического модуля в Microsoft SQL Server Analysis Services, позволяют моделировать данные корпоративного класса в облаке. 

Ознакомьтесь с этим видео, чтобы узнать больше о том, как службы Azure Analysis Services дополняют общие возможности средств бизнес-аналитики корпорации Майкрософт и как воспользоваться преимуществами переноса моделей данных в облако.


>
>[!Видео https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>На основе SQL Server Analysis Services
Службы Azure Analysis Services совместимы с уже знакомыми вам службами SQL Server Analysis Services выпуска Enterprise Edition. Службы Azure Analysis Services поддерживают табличные модели на уровне совместимости 1200 или более поздних. Кроме того, также поддерживаются DirectQuery, секции, безопасность на уровне строк, двунаправленные связи и переводы.

## <a name="use-the-tools-you-already-know"></a>Использование знакомых инструментов
![Средства разработчика бизнес-аналитики](./media/analysis-services-overview/aas-overview-dev-tools.png)

При создании моделей данных для служб Analysis Services Azure можно использовать те же средства, что и для служб SQL Server Analysis Services. Для создания и развертывания модели можно использовать [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) или [Azure Powershell](/powershell/azureps-cmdlets-docs) и шаблоны [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) в [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="supports-the-latest-features"></a>Поддержка новых возможностей
Службы Azure Analysis Services поддерживают табличные модели на уровне совместимости 1200 и 1400 (предварительная версия).

**Табличная модель 1200.** Впервые добавленная в SQL Server 2016 Analysis Services модель 1200 представила табличную модель объектов, описывающую такие объекты модели, как таблицы, столбцы и связи. Табличная модель объектов предоставляется в JSON с помощью [языка TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) и языка определения данных объектов AMO в пространстве имен [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

В **табличной модели 1400 (предварительная версия)** реализована поддержка строк детализации, безопасности на уровне объектов, неоднородной иерархии, современные возможности для подключения данных и множество других улучшений. Чтобы воспользоваться преимуществами новых функций, необходимо использовать последнюю версию [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx). Так как табличная модель 1400 все еще находится на стадии предварительной версии, все быстро меняется. Чтобы получить последнюю версию, ознакомьтесь с нашей [записью в блоге](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

## <a name="data-sources"></a>Источники данных
Модели данных, развернутые на серверах в Azure, поддерживают подключение к локальным и облачным источникам данных. Вы также можете получить гибридное решение бизнес-аналитики, объединив данные из локальных и облачных источников.

Так как сервер находится в облаке, подключение к облачным источникам данных не вызывает затруднений. При подключении к локальным источникам данных [локальный шлюз данных](analysis-services-gateway.md) обеспечивает быстрое и надежное соединение с сервером в облаке.

Дополнительные сведения о поддерживаемых локальных источниках данных см. в статье [Подключения к источникам данных](analysis-services-datasource.md).


## <a name="explore-your-data-from-anywhere"></a>Анализ данных из любого расположения
Подключайтесь к серверам и получайте данные, используя любое средство или программу. Службы Azure Analysis Services поддерживают подключение из Power BI Desktop, Excel, пользовательских приложений и средств, использующих браузер.

![Визуализации данных](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>Безопасность
#### <a name="user-authentication"></a>Аутентификация пользователей
Проверка подлинности пользователей в Azure Analysis Services осуществляется через [Azure Active Directory (AD)](../active-directory/active-directory-whatis.md). При входе в базу данных служб Azure Analysis Services пользователи используют удостоверение рабочей учетной записи с соответствующими правами доступа. Эти учетные записи должны быть участниками каталога Azure Active Directory по умолчанию для подписки, где находится сервер служб Azure Analysis Services. [Интеграция каталогов](https://technet.microsoft.com/library/jj573653.aspx) между AAD и локальной службой Active Directory — отличный способ предоставить пользователям доступ к базе данных служб Azure Analysis Services, но он не требуется для всех сценариев.

Во время входа пользователи вводят имя участника-пользователя учетной записи и пароль. При синхронизации с локальной службой Active Directory имя участника-пользователя часто соответствует адресу электронной почты организации.

Разрешения пользователей на управление ресурсами сервера Analysis Services Azure зависят от назначенной им роли в подписке Azure. По умолчанию администраторы подписки получают права владельца ресурсов сервера Azure. С помощью Azure Resource Manager можно добавить дополнительных пользователей.

#### <a name="data-security"></a>Защита данных
Для хранения данных и метаданных базы данных службы Azure Analysis Services используется хранилище BLOB-объектов Azure. Файлы данных в большом двоичном объекте шифруются с использованием шифрования на стороне сервера. При использовании режима прямого запроса сохраняются только метаданные. Доступ к фактическим данным осуществляется из источника данных во время запроса.

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

У вас есть предложения относительно документации? Чтобы добавить комментарии, щелкните надпись Livefyre в нижней части каждой статьи.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы достаточно знаете о службах Azure Analysis Services, пора приступать к работе. Узнайте, как [создать сервер](analysis-services-create-server.md) в Azure и [развернуть на нем табличную модель](analysis-services-deploy.md).



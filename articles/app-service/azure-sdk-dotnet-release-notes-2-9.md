---
title: Заметки о выпуске пакета SDK для Azure для .NET 2.9
description: Заметки о выпуске пакета SDK для Azure для .NET 2.9
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 30013c0bd8ded31ccaf3cdc142bbf8dc6b375031
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150963"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Заметки о выпуске пакета Azure SDK для .NET 2.9

В этой статье содержатся заметки о выпуске для версий 2.9 и 2.9.6 пакета Azure SDK для .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Сведения о выпуске Azure SDK для .NET 2.9.6

Дата выпуска: 16.11.2016
 
В этом выпуске нет никаких критических изменений пакета Azure SDK 2.9. Для использования этого пакета SDK с имеющимися проектами облачной службы также не требуется выполнять обновление.

### <a name="visual-studio-2017-release-candidate"></a>релиз-кандидат Visual Studio 2017

- В версии-кандидате Visual Studio 2017 этот выпуск пакета Azure SDK для .NET встроен в рабочую нагрузку Azure. Все средства, необходимые для разработки Azure, будут входить в эту версию. Для Visual Studio 2015 и Visual Studio 2013 пакет SDK по-прежнему будет доступен через WebPI. Когда Visual Studio 2017 будет выпущен в качестве конечного продукта, версии пакета Azure SDK для .NET больше не будут доступны для Visual Studio 2013. Чтобы скачать версию-кандидат Visual Studio 2017, перейдите по этой ссылке: https://www.visualstudio.com/vs/visual-studio-2017-rc/.

### <a name="azure-diagnostics"></a>Диагностика Azure

- Теперь сохраняется только частичная строка подключения к хранилищу для диагностики облачных служб с маркером вместо ключа. Фактический ключ к хранилищу данных теперь хранится в папке профиля пользователя, поэтому доступ к нему можно контролировать. Visual Studio будет считывать этот ключ для локальной отладки и процедуры публикации. 
- В ответ на описанные выше изменения группа разработчиков Visual Studio Online усовершенствовала шаблон задачи развертывания облачных служб Azure. Теперь пользователи могут указать ключ к хранилищу данных, чтобы настроить расширение системы диагностики во время публикации в Azure при непрерывной интеграции и развертывании.
- Мы сделали так, что стало возможно хранить безопасную строку подключения и разметку для системы диагностики Azure (WAD), чтобы устранить проблемы с конфигурацией в средах.
 
### <a name="windows-server-2016-virtual-machines"></a>Виртуальные машины Windows Server 2016

- Visual Studio теперь поддерживает развертывание облачных служб на виртуальных машинах с операционной системой из семейства версии 5 (Windows Server 2016). Для имеющихся облачных служб можно изменить параметры, чтобы использовать новую ОС из семейства версий. Если для создания облачных служб вы решили использовать .NET 4.6 или более поздней версии, по умолчанию для службы будет использоваться ОС из семейства версий 5.  Дополнительные сведения см. в статье [Таблица совместимости выпусков гостевых ОС Azure и пакетов SDK](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Известные проблемы

- В пакете SDK Azure для .NET 2.9.6 введено ограничение, который блокирует развертывание проектов, использующих неподдерживаемые платформы .NET Framework (например, .NET 4.6) для любого семейства ОС, предшествующего версии 5. Обходной путь описан [здесь](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Кэш роли Azure 

- Поддержка кэша роли Azure закончилась 30 ноября 2016 года. Дополнительные сведения см. [здесь](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Шаблоны Azure Resource Manager для Azure Stack

- Мы представляем Azure Stack как целевой объект развертывания для шаблонов Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Сводка о пакете Azure SDK для .NET 2.9

## <a name="overview"></a>Обзор
Этот документ содержит заметки о выпуске для пакета Azure SDK для .NET версии 2.9. 

Дополнительные сведения об обновлениях в этом выпуске см. в [объявлении о пакете SDK для Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 для Visual Studio 2015 с обновлением 2 и Visual Studio "15" (предварительная версия)
Это обновление содержит следующие исправления ошибок:

* Проблема, связанная с созданием клиента REST API, в котором строка "Unknown Type" (Неизвестный тип) будет использоваться в качестве имени папки, созданной кодом, и/или имени пространства имен в созданном коде.
* Проблема, связанная с запланированными веб-заданиями, в которых сведения проверки подлинности не удалось передать в процесс подготовки планировщика.

Это обновление содержит следующий новый компонент:

* Поддержка дополнительных служб приложений на вкладке "Службы" в диалоговом окне подготовки служб приложений. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Средства озера данных Azure для Visual Studio 2015 с обновлением 2
Эти обновления включают следующее:

* **Средства озера данных Azure** для Visual Studio теперь объединены в пакет SDK для Azure для версии .NET. Этот инструмент автоматически устанавливается при установке пакета SDK для Azure. 
  
    Этот инструмент постоянно обновляется. Получить обновления можно [здесь](http://aka.ms/datalaketool).
* **Обозреватель сервера** теперь позволяет просматривать все сущности метаданных U-SQL и обновлять некоторые из них. Дополнительную информацию см. в [этом](https://azure.microsoft.com/documentation/services/data-lake-analytics/) блоге.

## <a name="hdinsight-tools"></a>Средства HDInsight
**Средства HDInsight** для Visual Studio теперь поддерживают HDInsight версии 3.3, включая показ графики Tez и другие языковые исправления.

## <a name="azure-resource-manager"></a>Диспетчер ресурсов Azure
В этом выпуске добавлена поддержка [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) для шаблонов Resource Manager.

## <a name="see-also"></a>См. также
[Объявление о пакете SDK Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)


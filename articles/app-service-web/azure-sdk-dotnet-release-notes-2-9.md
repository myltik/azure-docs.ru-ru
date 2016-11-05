---
title: Заметки о выпуске пакета SDK для Azure для .NET 2.9
description: Заметки о выпуске пакета SDK для Azure для .NET 2.9
services: app-service\web
documentationcenter: .net
author: Juliako
manager: erikre
editor: ''

ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/17/2016
ms.author: juliako

---
# <a name="azure-sdk-for-.net-2.9-release-notes"></a>Заметки о выпуске пакета SDK для Azure для .NET 2.9
## <a name="overview"></a>Обзор
Этот документ содержит заметки о выпуске для пакета Azure SDK для .NET версии 2.9. 

Дополнительные сведения об обновлениях в этом выпуске см. в [объявлении о пакете SDK для Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-2.9-for-visual-studio-2015-update-2-and-visual-studio-"15"-preview"></a>Azure SDK 2.9 для Visual Studio 2015 с обновлением 2 и Visual Studio "15" (предварительная версия)
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
В этом выпуске добавлена поддержка [KeyVault](../resource-manager-keyvault-parameter.md) для шаблонов ARM.

## <a name="see-also"></a>Дополнительные материалы
[Объявление о пакете SDK Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!--HONumber=Oct16_HO2-->



---
title: Настройка инструментов Azure Stream Analytics для Visual Studio
description: В этой статье приведены требования для установки и настройки средств Azure Stream Analytics для Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Установка инструментов Azure Stream Analytics для Visual Studio
Инструменты Azure Stream Analytics теперь поддерживают Visual Studio 2017, Visual Studio 2015 и Visual Studio 2013. В этом документе показано, как устанавливать и удалять инструменты.

Дополнительные сведения об использовании инструментов см. в статье [Использование инструментов Azure Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Install
### <a name="visual-studio-2017"></a>Visual Studio 2017
* [Скачать Visual Studio 2017 (версии 15.3 или более поздней)](https://www.visualstudio.com/). Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. 
* Инструменты Stream Analytics являются частью рабочей нагрузки **разработки Azure** и **хранилища и обработки данных** в Visual Studio 2017. Включите одну из этих рабочих нагрузок как часть установки Visual Studio.

Включите рабочую нагрузку **Хранение и обработка данных**, как показано ниже.

![Рабочая нагрузка "Хранение и обработка данных"](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Включите рабочую нагрузку **Разработка для Azure**, как показано ниже.

![Рабочая нагрузка "Разработка для Azure".](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Установите Visual Studio 2015 или Visual Studio 2013 с обновлением 4. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. 
* Установите пакет SDK Microsoft Azure для .NET версии 2.7.1 или более поздней версии с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* Установите [инструменты Azure Stream Analytics для Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Блокировка изменений

### <a name="visual-studio-2017"></a>Visual Studio 2017
Напоминание о новой версии отображается в уведомлении Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Установленные инструменты Stream Analytics для Visual Studio автоматически проверяют наличие новых версий. Следуйте инструкциям во всплывающем окне, чтобы установить последнюю версию. 


## <a name="uninstall"></a>Удаление

### <a name="visual-studio-2017"></a>Visual Studio 2017
Дважды щелкните установщик Visual Studio и выберите **Изменить**. Снимите флажок **Средства Azure Data Lake и Stream Analytics** для рабочей нагрузки **Хранение и обработка данных** или **Разработка для Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Откройте панель управления и удалите **Средства Microsoft Azure Data Lake и Stream Analytics для Visual Studio**.






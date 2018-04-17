---
title: Инструкции по установке для инструментов Azure Stream Analytics для Visual Studio | Документация Майкрософт
description: Инструкции по установке для инструментов Azure Stream Analytics для Visual Studio.
keywords: Visual Studio
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: fe1f2158439b2c04e8232eb6fe3c9faf79641ffe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Инструкции по установке для инструментов Stream Analytics для Visual Studio
Инструменты Azure Stream Analytics теперь поддерживают Visual Studio 2017, Visual Studio 2015 и Visual Studio 2013. В этом документе показано, как устанавливать и удалять инструменты.

[Узнайте, как использовать инструменты Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

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






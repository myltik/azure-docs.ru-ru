---
title: Использование виртуальной машины для обработки и анализа геоданных средствами искусственного интеллекта в Azure | Документация Майкрософт
description: Использование виртуальной машины для обработки геоданных средствами ИИ в Azure.
keywords: deep learning, AI, data science tools, data science virtual machine, Geospatial analytics
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f346b086a0269f247d64edf9346b01849ba3d0ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408043"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Использование виртуальной машины для обработки и анализа геоданных средствами ИИ

Виртуальная машина для обработки и анализа геоданных средствами искусственного интеллекта позволяет осуществлять выборку данных для анализа, обрабатывать данные и создавать модели для приложений ИИ, использующих геопространственную информацию. Подготовив виртуальную машину для обработки и анализа геоданных средствами ИИ и войдя в ArcGIS Pro с учетной записью ArcGIS, можно начать взаимодействие с настольным приложением и онлайн-службой ArcGIS. Также доступ к ArcGIS можно получить с помощью интерфейсов Python и моста для языка R, предварительно настроенного на виртуальной машине для обработки и анализа геоданных. Создавать полнофункциональные приложения ИИ можно, объединяя их с платформами машинного обучения и глубокого обучения, а также доступным на виртуальной машине для обработки и анализа данных другим ПО соответствующего назначения.  


## <a name="configuration-details"></a>Сведения о конфигурации

Библиотека Python [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), которая используется в качестве интерфейса к ArcGIS, установлена в глобальной корневой среде conda виртуальной машины для обработки и анализа данных, находящейся в папке ```c:\anaconda```. 

- Если вы запускаете Python из командной строки, выполните команду ```activate```, чтобы активировать корневую среду conda для Python. 
- Если вы используете интегрированную среду разработки или записную книжку Jupyter, можно выбрать среду или ядро, чтобы убедиться, что вы находитесь в нужной среде conda. 

R-мост к ArcGIS устанавливается в качестве библиотеки R с именем [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) в основном автономном экземпляре Microsoft R Server, расположенном в папке ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio и Jupyter уже предварительно настроены для использования этой среды R и будут иметь доступ к библиотеке R ```arcgisbinding```. 


## <a name="geo-ai-data-science-vm-samples"></a>Примеры виртуальной машины для обработки и анализа геоданных средствами ИИ

Кроме примеров на основе платформ машинного обучения и глубокого обучения из базовой виртуальной машины для обработки и анализа данных, доступны также примеры геопространственных решений в составе виртуальной машины для обработки и анализа геоданных средствами ИИ. Эти примеры помогут вам начать разработку приложений ИИ с использованием геопространственных данных и программного обеспечения ArcGIS. 


1. [Getting started with Geospatial analytics with Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb) (Начало работы с геопространственной аналитикой с помощью Python): пример использования геопространственных данных с помощью интерфейса Python к ArcGIS, предоставленного библиотекой [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm). В примере также показано, как можно объединить традиционное машинное обучение с геопространственными данными и визуализировать результат на карте в ArcGIS. 

2. [Getting started with Geospatial analytics with R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb) (Начало работы с геопространственной аналитикой с помощью R): пример использования геопространственных данных с помощью R-интерфейса к ArcGIS, предоставленного библиотекой [arcgisbinding](https://github.com/R-ArcGIS/r-bridge). 

3. [Pixel-level land use classification](https://github.com/Azure/pixel_level_land_classification) (Классификация использования земли на уровне пикселей): учебное руководство по созданию модели глубокой нейронной сети, которая принимает аэрофотоснимок и возвращает метку покрытия земли. Примеры меток покрытия земли — "лес" или "вода". Модель возвращает такую метку для каждого пикселя в изображении. Модель создана на основе платформы глубокого обучения Microsoft [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) с открытым исходным кодом. В примере также показано, как масштабировать обучение с помощью [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) и использовать прогнозы модели в программном обеспечении ArcGIS Pro. 


## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры использования виртуальной машины для обработки и анализа данных см. здесь:

* [Примеры](dsvm-samples-and-walkthroughs.md)


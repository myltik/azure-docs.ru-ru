---
title: Примеры и пошаговые руководства для виртуальных машин для обработки и анализа данных в Azure | Документация Майкрософт
description: Примеры и пошаговые руководства для виртуальных машин для обработки и анализа данных
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 3e3ee232b6342601e44d728148d32e70e6f3f00b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419982"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Примеры для виртуальных машин для обработки и анализа данных

Виртуальные машины для обработки и анализа данных имеют полностью рабочие примеры в форме записных книжек Jupyter и некоторые, которые не основаны на Jupyter. Доступ к Jupyter можно получить, щелкнув значок `Jupyter` на рабочем столе или в меню приложения.  
> [!NOTE]
> Обратитесь к разделу [Доступ к Jupyter](#access-jupyter), чтобы включить записные книжки Jupyter на виртуальной машине для обработки и анализа данных.

## <a name="quick-reference-of-samples"></a>Краткий справочник примеров
| Категории примеров | ОПИСАНИЕ | Расположения |
| ------------- | ------------- | ------------- |
| Язык **R**  | Примеры на **R**, которые рассматривают такие сценарии, как подключение к облачным хранилищам данных Azure, сравнение моделей эксплуатации R и Microsoft R с открытым исходным кодом в Microsoft R Server и SQL Server. <br/> [Снимок экрана](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Язык **Python**  | Примеры на **Python**, которые рассматривают такие сценарии, как подключение к облачным хранилищам данных Azure и работу с **Машинным обучением Azure**.  <br/> [Снимок экрана](#python-language) | <br/>`~notebooks` <br/><br/>|
| Язык **Julia**  | Пример на **Julia**, описывающий построение графиков в Julia, глубокое обучение на Julia, вызов C и Python из Julia и т. д. <br/> [Снимок экрана](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Примеры глубокого обучения, опубликованные командой Cognitive Toolkit в Майкрософт.  <br/> [Снимок экрана](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| Записные книжки **MXnet**  | Примеры глубокого обучения, использующие нейронные сети на основе **MXnet**. Есть множество записных книжек: от базовых до расширенных сценариев.  <br/> [Снимок экрана](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Машинное обучение Azure** AzureML  | Взаимодействие со Студией **машинного обучения Azure** и создание конечных точек веб-службы из локально обученных моделей для рабочих процессов оценки в облаке. <br/> [Снимок экрана](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **Caffe2** | Примеры глубокого обучения, использующие нейронные сети на основе **Caffe2**. Есть несколько записных книжек, предназначенных для ознакомления пользователей с caffe2, и способов его эффективного использования, включая примеры, такие как предварительная обработка изображений, создание набора данных, регрессия и использование предварительно подготовленных моделей. <br/> [Снимок экрана](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Примеры на основе Python, использующие **H2O** для решения проблем во множестве реальных сценариев. <br/> [Снимок экрана](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Язык **SparkML**  | Пример, использующий функции и возможности набора средств **MLlib** через **pySpark 2.0** в **Apache Spark 2.0**.  <br/> [Снимок экрана](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| Язык **MMLSpark**  | Множество примеров использования **MMLSpark — машинное обучение Microsoft для Apache Spark**, платформы, предоставляющей ряд средств для углубленного обучения и обработки и анализа данных для **Apache Spark**. <br/> [Снимок экрана](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Несколько различных примеров нейронной сети и методов, реализованных с помощью платформы **TensorFlow**. <br/> [Снимок экрана](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Примеры стандартного машинного обучения в **XGBoost** для сценариев классификации, регрессии и т. д. <br/> [Снимок экрана](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Доступ к Jupyter 

Посетите домашнюю страницу Jupyter, перейдя к **`https://localhost:9999`** в Windows или **`https://localhost:8000`** в Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Включение доступа Jupyter из браузера

**Виртуальная машина для обработки и анализа данных под управлением Windows**

Запустите **`Jupyter SetPassword`** с помощью ярлыка на рабочем столе и следуйте инструкциям, чтобы установить или сбросить пароль для Jupyter и запустить процесс Jupyter. 
<br/>![Включение исключений Jupyter](./media/jupyter-setpassword.png)<br/>
Доступ к домашней странице Jupyter можно получить после полного запуска процесса Jupyter на виртуальной машине. Для этого откройте в браузере **`https://localhost:9999`**. Ознакомьтесь со снимком экрана для добавления исключения и получения доступа к Jupyter через браузер.
<br/>![Включение исключений Jupyter](./media/windows-jupyter-exception.png)<br/>
Войдите, используя только что заданный пароль.
<br/>
**Виртуальная машина для обработки и анализа данных под управлением Linux**

Доступ к домашней странице Jupyter на виртуальной машине можно получить, открыв в браузере **`https://localhost:8000`**. Ознакомьтесь со снимком экрана для добавления исключения и включения доступа Jupyter через браузер.
<br/>![Включение исключений Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Войдите, используя один и тот же пароль, что и для входа в DSVM.
<br/>

**Домашняя страница Jupyter**
<br/>![Домашняя страница Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Язык R 
<br/>![Примеры для R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Язык Python
<br/>![Примеры для Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Язык Julia 
<br/>![Примеры для Julia](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Примеры CNTK](./media/cntk-samples2.png)<br/>
<br/>![Примеры CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![Примеры MXNet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Примеры AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>Caffe2 
<br/>![Примеры Caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Примеры H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Примеры SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Примеры TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost; 
<br/>![Примеры XGBoost](./media/xgboost-samples.png)<br/>


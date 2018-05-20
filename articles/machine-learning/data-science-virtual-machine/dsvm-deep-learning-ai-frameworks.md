---
title: Платформы глубокого обучения и искусственного интеллекта в Azure | Документация Майкрософт
description: Платформы глубокого обучения и искусственного интеллекта
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
ms.openlocfilehash: cd8579fe29282f3875ecfddf67a34444e3de9ef5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Платформы глубокого обучения и искусственного интеллекта
[Виртуальная машина для обработки и анализа данных](http://aka.ms/dsvm) (DSVM) и [виртуальная машина для глубокого обучения](http://aka.ms/dsvm/deeplearning) поддерживают несколько платформ глубокого обучения для создания приложений искусственного интеллекта (AI) с прогнозной аналитикой и когнитивными возможностями, например понимание изображений и языка. 

Ниже приведены сведения о всех платформах глубокого обучения, доступных на DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | Microsoft Cognitive Toolkit (CNTK) устанавливается с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Ссылки на примеры      | Сюда входят примеры записных книжек Jupyter.     |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Keras      |
| Запуск и использование    | * В окне терминала активируйте нужную среду и запустите Python. <br/>
 * В Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов CNTK. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | TensorFlow устанавливается с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Ссылки на примеры      | Сюда входят примеры записных книжек Jupyter.     |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Keras      |
| Запуск и использование    | * В окне терминала активируйте нужную среду и запустите Python. <br/>
 * В Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов TensorFlow.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Что это такое?   | Распределенная платформа глубокого обучения для TensorFlow      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Horovod устанавливается с Python 3.5 на [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Ссылки на примеры      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | TensorFlow      |
| Запуск и использование    | В окне терминала активируйте соответствующую среду и запустите Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | TensorFlow устанавливается с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Ссылки на примеры      | https://github.com/fchollet/keras/tree/master/examples      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Запуск и использование    | * В окне терминала активируйте нужную среду и запустите Python. <br/>
 * В Jupyter скачайте образцы из расположения GitHub, подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем откройте каталог образцов. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Caffe устанавливается в `/opt/caffe`.    |
| Как перейти на использование Python 2.7 | Запустите `source activate root` |
| Ссылки на примеры      | Примеры находятся в `/opt/caffe/examples`.      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Caffe2      |
### <a name="how-to-use--run-it"></a>Запуск и использование  

Используйте X2Go для входа в виртуальную машину, а затем запустите новый терминал и введите следующее:

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Откроется новое окно браузера с примерами записных книжек.

Двоичные файлы устанавливаются в каталог /opt/caffe/build/install/bin.

Для установленной версии Caffe требуется Python 2.7, и она не будет работать с компонентом Python 3.5, активируемым по умолчанию. Выполните команду `source activate root`, чтобы переключиться на среду Anaconda. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Caffe2 устанавливается в среду [conda с Python 2.7 (в корень)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Источник данных находится в `/opt/caffe2`. |
| Ссылки на примеры      | Примеры записных книжек поставляются с JupyterHub. |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Caffe      |
| Запуск и использование    | * В окне терминала активируйте [корневую среду Python](dsvm-languages.md#python-linux-and-windows-server-2012-edition), запустите Python и импортируйте Caffe2. <br/> * В JupyterHub подключитесь к [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), а затем перейдите в каталог Caffe2 к примерам записных книжек. Для некоторых записных книжек требуется корневой каталог Caffe2, чтобы их можно было задать в коде Python. Введите /opt/caffe2. |
| Создание записных книжек | Caffe2 создается на основе источника данных в Linux и содержит CUDA, cuDNN и Intel MKL. Текущая контрольная сумма — 0d9c0d48c6f20143d6404b99cc568efd29d5a4be — выбрана для обеспечения стабильности всех графических процессоров и протестирована на образцах. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | Chainer устанавливается с [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL и ChainerCV также устанавливаются.   |
| Ссылки на примеры      | Примеры записных книжек поставляются с JupyterHub. |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Caffe      |
| Запуск и использование  | * В окне терминала активируйте среду [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), запустите _python_, а затем импортируйте Chainer. <br/>
* В JupyterHub [подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем перейдите в каталог Chainer к примерам записных книжек.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения для H2O      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Deep Water устанавливается с [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) и доступна также в `/dsvm/tools/deep_water`.   |
| Ссылки на примеры      | Примеры записных книжек поставляются с JupyterHub.      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Запуск и использование  

Для работы Deep Water требуется CUDA 8 с cuDNN 5.1. Этот вариант не совпадает с путем к библиотеке, заданным по умолчанию, так как другие платформы глубокого обучения используют CUDA 9 и cuDNN 7. Чтобы использовать CUDA 8 + cuDNN 5.1 для Deep Water, необходимо следующее:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Для использования Deep Water необходимо следующее:
* В окне терминала активируйте среду [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а затем запустите _python_. <br/>
* В JupyterHub [подключитесь к JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем перейдите в каталог deep_water к примерам записных книжек.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | MXNet устанавливается в `C:\dsvm\tools\mxnet` в Windows и `/dsvm/tools/mxnet` в Linux. Привязки Python устанавливаются с Python 3.5 на [Linux и Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), а с Python 3.6 на [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Привязки R также устанавливаются в Ubuntu.   |
| Ссылки на примеры      | Сюда входят примеры записных книжек Jupyter.    |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Keras      |
| Запуск и использование    | * В окне терминала активируйте нужную среду и запустите Python. <br/>
 * В Jupyter подключитесь к [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) или [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов mxnet.  |
 | Создание записных книжек | MXNet создается из источников данных в Linux. В эту сборку входят CUDA, cuDNN, NCCL и MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Что это такое?   | Система глубокого обучения от NVIDIA для быстрого обучения моделей глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | DIGITS устанавливается в `/dsvm/tools/DIGITS` и доступна в виде службы, которая также называется _digits_.   |
### <a name="how-to-use--run-it"></a>Запуск и использование  

Войдите на виртуальную машину с помощью X2Go. В терминале запустите службу:

    sudo systemctl start digits

Запуск службы займет около одной минуты. Запустите браузер и перейдите по адресу `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Что это такое?   | Средство NVIDIA для выполнения запросов активности GPU      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows, Linux     |
| Настройка и установка на DSVM  | _nvidia-smi_ можно найти в системном пути.   |
| Запуск и использование | Откройте командную строку (в Windows) или терминал (в Linux), а затем запустите _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Theano устанавливается в среде Python 2.7 (_корневая среда_), а также в Python 3.5 (_py35_).   |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Keras      |
| Запуск и использование    | * В терминале активируйте нужную версию Python (корневую или py35), запустите python, а затем импортируйте theano. <br/> 
* В Jupyter выберите ядро Python 2.7 или 3.5, а затем импортируйте theano.  
<br/>
Чтобы обойти недавно выявленную ошибку MKL, необходимо сначала установить потоковый уровень MKL.<br/><br/>
_export MKL_THREADING_LAYER=GNU_
|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | Torch устанавливается в `/dsvm/tools/torch`. PyTorch устанавливается в среде Python 2.7 (_корневая среда_), а также в Python 3.5 (_py35_).   |
| Ссылки на примеры      | Примеры Torch находятся в `/dsvm/samples/torch`. Примеры PyTorch находятся в `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Что это такое?   | Платформа глубокого обучения      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Linux     |
| Настройка и установка на DSVM  | PyTorch устанавливается на [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Ссылки на примеры      | Сюда входят примеры записных книжек Jupyter, которые можно найти в папке /dsvm/samples/pytorch.      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | Torch      |
| Запуск и использование | 
* В окне терминала активируйте соответствующую среду и запустите Python. <br/>
 * * В Jupyter подключитесь к [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), затем откройте каталог образцов PyTorch.  |

## <a name="mxnet-model-server"></a>Сервер модели MXNet

|    |           |
| ------------- | ------------- |
| Что это такое?   | Сервер для создания конечных точек HTTP для моделей MXNet и ONNX      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Linux     |
| Настройка и установка на DSVM  | _mxnet-model-server_ доступен в терминале.   |
| Ссылки на примеры      | Последние примеры можно найти на [странице сервера модели MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | MXNet      |

## <a name="tensorflow-serving"></a>Сервер модели TensorFlow

|    |           |
| ------------- | ------------- |
| Что это такое?   | Сервер для формирования выводов в модели TensorFlow      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Linux     |
| Настройка и установка на DSVM  | _tensorflow_model_server_ доступен в терминале.   |
| Ссылки на примеры      | Примеры можно найти в [Интернете](https://www.tensorflow.org/serving/).      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Что это такое?   | Сервер вывода для глубокого обучения NVIDIA. |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Ubuntu     |
| Настройка и установка на DSVM  | TensorRT устанавливается как пакет _apt_.   |
| Ссылки на примеры      | Примеры можно найти в [Интернете](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Дополнительные средства на виртуальной машине для обработки и анализа данных      | TensorFlow Serving, MXNet Model Server  |




---
title: Как использовать GPU для службы "Машинное обучение Azure" | Документация Майкрософт
description: В этой статье описывается обучение глубоких нейронных сетей в Azure Machine Learning Workbench с использованием блока графической обработки (GPU).
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: db0cfa1d91681d48a21ac160706dc4e8c72f81c2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830917"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Как использовать GPU в службе "Машинное обучение Azure"
Графический процессор (GPU) широко используется для обработки ресурсоемких задач, которые обычно выполняются при обучении определенных моделей глубокой нейронной сети. С помощью GPU можно значительно сократить время обучения моделей. Из этого документа вы узнаете, как настроить в Azure ML Workbench использование [DSVM (виртуальная машина для обработки и анализа данных)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview), оснащенную GPU, в качестве целевой среды выполнения. 

## <a name="prerequisites"></a>предварительным требованиям
- Чтобы поработать с этим пошаговым руководством, сначала необходимо установить [Azure ML Workbench](../service/quickstart-installation.md).
- Необходимо иметь доступ к компьютерам, оснащенным графическими процессорами NVidia.
    - Скрипты можно выполнять напрямую на локальных компьютерах (Windows или macOS) с графическими процессорами,
    - а также в контейнере Docker на компьютере с графическими процессорами.

## <a name="execute-in-local-environment-with-gpus"></a>Выполнение в _локальной_ среде с графическими процессорами
Azure ML Workbench можно установить на локальном компьютере, оснащенном GPU, и работать с решением в _локальной_ среде. Это может быть:
- Физический компьютер Windows или macOS.
- Виртуальная машина Windows, например виртуальная машина для обработки и анализа данных с Windows, подготовленная с помощью шаблона виртуальных машин Azure серии NC.

В этом случае Azure ML Workbench не нужно настраивать определенным образом. Просто убедитесь, что в локальной среде установлены все необходимые драйверы, наборы средств и библиотеки машинного обучения с поддержкой GPU. Выполняйте скрипты в _локальной_ среде, где среда выполнения Python может напрямую получить доступ к локальному оборудованию GPU.

1. Откройте AML Workbench. Выберите **Файл** и **Открыть командную строку**. 
2. Из командной строки установите платформу глубокого обучения с поддержкой GPU, например Microsoft Cognitive Toolkit, TensorFlow и т. д. Например: 

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Запишите код Python, использующий библиотеки глубокого обучения.
4. Выберите _локальную_ среду вычислений и выполните код Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Выполнение в среде _Docker_ на виртуальной машине Linux с графическими процессорами
Azure ML Workbench также поддерживает выполнение в Docker на виртуальной машине Azure под управлением Linux. Это отличный вариант, позволяющий выполнять ресурсоемкие задания на мощном виртуальном оборудовании, выключать его по завершении и платить только за использованные ресурсы. Хотя в принципе графические процессоры можно использовать на любой виртуальной машине Linux, на виртуальной машине для обработки и анализа данных на базе Ubuntu установлены обязательные драйверы и библиотеки CUDA, значительно упрощающие настройку. Сделайте следующее:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Создание виртуальной машины для обработки и анализа данных на базе Ubuntu под управлением Linux в Azure
1. В браузере откройте [портал Azure](https://portal.azure.com).

2. В левой части портала выберите **+ Создать**.

3. В Marketplace найдите "Виртуальная машина Linux (Ubuntu) для обработки и анализа данных".

4. Нажмите кнопку **Создать** для создания виртуальной машины Ubuntu для обработки и анализа данных.

5. Укажите необходимые сведения в форме **Основы**.
При выборе расположения виртуальной машины обратите внимание, что виртуальные машины GPU доступны только в определенных регионах Azure, например в **юго-центральном регионе США**. Дополнительные сведения см. в статье [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).
Нажмите кнопку "ОК", чтобы сохранить сведения в форме **Основы**.

6. Выберите размер виртуальной машины. Выберите один из размеров для виртуальных машин с префиксом NC, которые оснащены микросхемами графического процессора NVidia.  Щелкните **Просмотреть все**, чтобы при необходимости просмотреть полный список. Дополнительные сведения о виртуальных машинах Azure, оснащенных GPU, см. в статье [Графический процессор](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Настройте остальные параметры и просмотрите сведения о приобретении. Щелкните "Приобрести" для создания виртуальной машины. Запишите IP-адрес, выделенный для виртуальной машины. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Создание проекта в Azure ML Workbench 
Воспользуйтесь примером _классификации MNIST с помощью TensorFlow_ или _классификации набора данных MNIST с помощью CNTK_.

### <a name="create-a-new-compute-target"></a>Создание целевой среды вычислений
Запустите командную строку из Azure ML Workbench. Введите следующую команду. Замените текст заполнителя в приведенном ниже примере собственными значениями для имени, IP-адреса, имени пользователя и пароля. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Настройка Azure ML Workbench для доступа к GPU
Вернитесь в проект и откройте **представление файлов**, а затем нажмите кнопку **Обновить**. Теперь можно увидеть два новых файла конфигурации: `my_dsvm.compute` и `my_dsvm.runconfig`.
 
Откройте `my_dsvm.compute`. Измените `baseDockerImage` на `microsoft/mmlspark:plus-gpu-0.7.9` и добавьте новую строку `nvidiaDocker: true`. Таким образом, файл должен содержать следующие две строки:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Теперь откройте `my_dsvm.runconfig` и измените значение `Framework` с `PySpark` на `Python`. Если строка не отображается, добавьте ее, так как значение по умолчанию будет `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Создание ссылки на платформу глубокого обучения 
Откройте файл `conda_dependencies.yml` и убедитесь, что вы используете версию GPU для пакетов Python платформы глубокого обучения. В примерах проектов указаны версии ЦП, поэтому необходимо внести эти изменения.

Пример для TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Пример для Microsoft Cognitive Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

Вы также можете использовать 1-разрядную SGD-версию набора средств Microsoft Cognitive Toolkit, обеспечивающую повышение производительности на виртуальных машинах с несколькими GPU. Обратите внимание на [требования к лицензии для 1-разрядной SGD-версии](https://docs.microsoft.com/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Выполнить
Теперь вы готовы к выполнению скриптов Python. Вы можете выполнять их в Azure ML Workbench с использованием контекста `my_dsvm` или запускать из командной строки:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Чтобы проверить использование графического процессора, изучите выходные данные. Они должны выглядеть примерно так:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Поздравляем! Вы использовали возможности GPU в контейнере Docker в скрипте.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с примером использования GPU для ускорения обучения глубокой нейронной сети в коллекции Azure ML.

---
title: Использование профилей версий API и Python в Azure Stack | Документы Майкрософт
description: Сведения об использовании профилей версий API и Python в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310300"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Использование профилей версий API и Python в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="python-samples-for-azure-stack"></a>Примеры Python для Azure Stack 

Приведенные ниже примеры кода можно использовать для выполнения типичных задач управления для виртуальных машин в Azure Stack.

Примеры кода показывают следующее:

- Создание виртуальных машин:
    - Создание виртуальной машины Linux
    - Создание виртуальной машины Windows
- Обновление виртуальной машины:
    - Расширение диска
    - Добавление тега для виртуальной машины
    - Присоединение дисков данных
    - Отключение дисков данных
- Эксплуатация виртуальной машины:
    - запуск виртуальной машины
    - Остановка виртуальной машины
    - Перезапуск виртуальной машины
- Отображение списка виртуальных машин
- удаление виртуальной машины

Чтобы просмотреть код для выполнения этих операций, изучите функцию **run_example()** в сценарии Python **Hybrid/unmanaged-disks/example.py** из репозитория GitHub [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Каждая операция однозначно помечается комментарием и функцией печати.
Примеры необязательно следуют в порядке, показанном в приведенном выше списке.


## <a name="run-the-python-sample"></a>Запуск примера Python

1.  Если у вас не установлен язык Python, [установите его](https://www.python.org/downloads/).

    Этот пример (и пакет SDK) совместим с Python 2.7, 3.4, 3.5 и 3.6.

2.  Общей рекомендацией для разработки на Python является использование виртуальной среды. 
    Дополнительные сведения см. в разделе https://docs.python.org/3/tutorial/venv.html.
    
    Установите и инициализируйте виртуальную среду с помощью модуля "venv" в Python 3 (нужно установить [virtualenv](https://pypi.python.org/pypi/virtualenv) для Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Клонируйте репозиторий.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Установите зависимости с помощью PIP.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Создайте [субъект-службу](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) для работы с Azure Stack. Убедитесь, что субъект-служба имеет [роль участника или владельца](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) в вашей подписке.

6.  Задайте указанные ниже переменные среды и экспортируйте их в вашу текущую оболочку. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Обратите внимание, что для запуска этого примера в Azure Stack Marketplace должны присутствовать образы Ubuntu 16.04-LTS и WindowsServer 2012-R2-Datacenter. Их можно [скачать из Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) или [добавить в репозиторий образов платформы](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Запустите пример.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Заметки

Вам может быть предложено попытаться извлечь диск ОС виртуальной машины с помощью `virtual_machine.storage_profile.os_disk`.
В некоторых случаях это может дать нужный результат, но не забывайте, что в этом случае вы получаете объект `OSDisk`.
Чтобы изменить размер диска ОС, как это делает `example.py`, вам нужен объект `Disk`, а не `OSDisk`.
`example.py` возвращает объект `Disk` со следующим содержимым:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Дополнительная информация

- [Центр разработки на Python Azure](https://azure.microsoft.com/develop/python/)
- [Документация по виртуальным машинам Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Схема обучения для виртуальных машин](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Если у вас нет подписки Microsoft Azure, [здесь](http://go.microsoft.com/fwlink/?LinkId=330212) можно получить бесплатную пробную учетную запись.

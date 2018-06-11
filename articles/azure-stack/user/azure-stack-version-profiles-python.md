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
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806842"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Использование профилей версий API и Python в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="python-and-api-version-profiles"></a>Использование профилей версии API с помощью Python

Пакет SDK для Python поддерживает профили версии API для разных облачных платформ, например Azure Stack и глобальной среды Azure. Вы можете использовать профили API при создании решений для гибридного облака. Пакет SDK для Python поддерживает такие профили API:

1. **Актуальная**  
    Профиль предназначен для последних версий API всех поставщиков служб на платформе Azure.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Профиль предназначен для версий API поставщиков ресурсов, поддерживаемых Azure Stack.

    Дополнительные сведения о профилях API и Azure Stack см. в статье [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Установка пакета Azure SDK для Python

1.  Установите Git с [официального сайта](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Инструкции по установке пакета SDK для Python см. в руководстве по [Azure для разработчиков Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  При необходимости создайте подписку и сохраните ее идентификатор для дальнейшего использования. Инструкции по созданию подписки см. в статье [Создание подписок для предложений в Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Создайте субъект-службу и сохраните его идентификатор и секрет. Инструкции по созданию субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](../azure-stack-create-service-principals.md). 
5.  Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Предоставление приложениям доступа к Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать пакет SDK Azure для Python и Azure Stack, укажите следующие значения и задайте значения для переменных среды. См. инструкции по указанию переменных среды для вашей операционной системы после таблицы. 

| Значение | Переменные среды | ОПИСАНИЕ |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID | AZURE_TENANT_ID | Значение [идентификатора клиента](../azure-stack-identity-overview.md) Azure Stack. |
| Идентификатор клиента | AZURE_CLIENT_ID | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше). |
| Идентификатор подписки | AZURE_SUBSCRIPTION_ID | [Идентификатор подписки](../azure-stack-plan-offer-quota-overview.md#subscriptions) для доступа к предложениям в Azure Stack. |
| Секрет клиента | AZURE_CLIENT_SECRET | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше). |
| Конечная точка Resource Manager | ARM_ENDPOINT | См. дополнительные сведения о [конечной точке Resource Manager для Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


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

5.  Создайте [субъект-службу](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) для работы с Azure Stack. Убедитесь, что субъект-служба имеет [роль участника или владельца](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) в вашей подписке.

6.  Задайте указанные ниже переменные среды и экспортируйте их в вашу текущую оболочку. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).
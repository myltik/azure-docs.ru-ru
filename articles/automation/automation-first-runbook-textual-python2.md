---
title: "Мой первый модуль Runbook Python в службе автоматизации Azure | Документация Майкрософт"
description: "Руководство, в котором описывается создание, тестирование и публикация простого модуля Runbook Python."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 0cf0b1829c7e1f9b0b8be90983a705d82784c062
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="my-first-python-runbook"></a>Мой первый модуль Runbook Python

> [!div class="op_single_selector"]
> * [Графический](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

В этом руководстве описана процедура создания [модуля Runbook Python](automation-runbook-types.md#python-runbooks) в службе автоматизации Azure. Начать с простого модуля runbook, тестирование и публикация. Затем мы изменим модуль runbook, настроив его для фактического управления ресурсами Azure (в нашем примере это запуск виртуальной машины Azure). Наконец вносятся runbook более надежными, добавив параметры модуля runbook.

## <a name="prerequisites"></a>Технические условия

Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как в процессе изучения этого руководства ее нужно будет остановить и запустить заново.

## <a name="create-a-new-runbook"></a>Создание модуля Runbook

Начните с создания простого модуля runbook, который выводит текст *Hello World*.

1. На портале Azure выберите свою учетную запись службы автоматизации.

    Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись службы автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).<br>

1. В разделе **Управление процессами** выберите **Модули Runbook**, чтобы открыть список модулей.
2. Выберите **+ добавить runbook** для создания модуля runbook.
3. Присвойте модулю Runbook имя *MyFirstRunbook-Python*.
4. В этом случае вы собираетесь создать [Python runbook](automation-runbook-types.md#python-runbooks) поэтому выберите **Python 2** для **тип Runbook**.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="add-code-to-the-runbook"></a>Добавление кода в модуль Runbook

Теперь можно добавить простой команды Печать текста «Hello World»:

```python
print("Hello World!")
```

Нажмите кнопку **Сохранить**, чтобы сохранить модуль runbook.

## <a name="test-the-runbook"></a>Тестирование модуля Runbook

Прежде чем опубликовать модуль runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, правильно ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его **черновую** версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.
   ![Test Pane](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
1. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.
   Сначала задание получает состояние *В очереди*, которое означает, что задание ожидает доступа к рабочей роли runbook в облаке. Оно перемещается в *запуск* когда исполнитель задания, а затем *под управлением* при runbook фактически начинает выполнение.
1. Когда задание модуля Runbook будет выполнено, на экране появится результат. В этом случае вы увидите *Hello World*.
1. Закройте область тестирования, чтобы вернуться на холст.

## <a name="publish-and-start-the-runbook"></a>Публикация и запуск модуля Runbook

Модуль runbook, который вы создали — по-прежнему в режиме черновика. Необходимо опубликовать его перед его запуском в рабочей среде.
При публикации модуля Runbook существующая опубликованная версия перезаписывается черновой версией.
В этом случае вы опубликованной версии еще нет, так как вы только что создали runbook.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
   ![Кнопка "Опубликовать"](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Если прокрутка влево, чтобы просмотреть runbook в **Runbooks** панели, он отображается **состояние создания** из **опубликовано**.
1. Прокрутите экран вправо до области **MyFirstRunbook-Python**.
   Параметры в верхней части экрана позволяют запускать и просматривать модуль runbook, назначать его запуск в определенное время в будущем, а также создавать вызовы [webhook](automation-webhooks.md), чтобы запускать модуль с помощью HTTP-вызова.
1. Вы хотите запустить модуль runbook, нажмите **запустить** и нажмите кнопку **ОК** при открытии колонки запустить Runbook.
1. Для задания runbook, которое вы создали открывается область задание. Вы можете закрыть эту панель, но в этом случае можно оставить его открытым, можно отслеживать ход выполнения задания.
1. Состояние задания отображается в **Сводка заданий** и сопоставляет состояния, вы видели, когда тестирование модуля runbook.
1. Как только состояние модуля Runbook изменится на *Выполнено*, щелкните **Выходные данные**. Открыть область вывода, и вы увидите вашей *Hello World*.
1. Закройте область выходных данных.
1. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. В потоке вывода должен отображаться только текст *Hello World*, но могут присутствовать и другие потоки заданий runbook, например "Подробные сведения" и "Ошибка", если модуль записывает в них какие-то данные.
1. Закройте область потоков и область заданий, чтобы вернуться в область MyFirstRunbook-Python.
1. Щелкните **Задания** , чтобы открыть область "Задания" для этого Runbook. Откроется список всех заданий, созданных этим модулем Runbook. В нем должно быть только одно задание, так как вы запустили задание только один раз.
1. Если щелкнуть это задание, откроется та же область заданий, что и при запуске модуля runbook. С ее помощью можно вернуться назад и просмотреть сведения о любом задании, созданном для конкретного модуля Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Добавление аутентификации для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure.
Для управления ресурсами Azure сценарий должен выполнить аутентификацию с помощью учетных данных из [учетной записи службы автоматизации](automation-offering-get-started.md).

> [!NOTE]
> Учетную запись службы автоматизации необходимо создать с помощью функции субъекта-служба, чтобы в ней был сертификат запуска от имени.
> Если учетная запись службы автоматизации не была создана с помощью субъекта-службы, то для аутентификации можно использовать метод, описанный в статье [Проверка подлинности с помощью библиотек управления Azure для Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Откройте текстовый редактор, щелкнув **Изменить** в области MyFirstRunbook-Python.
1. Добавьте следующий код для аутентификации в Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Добавление кода для создания вычислительного клиента Python и запуска виртуальной машины

Для работы с виртуальными машинами Azure создайте экземпляр [вычислительного клиента Azure для Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Используйте вычислительный клиент для запуска виртуальной машины. Добавьте в модуль Runbook следующий код:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

В этом фрагменте _MyResourceGroup_ — это имя группы ресурсов, в которую входит виртуальная машина, а _TestVM_ — это имя виртуальной машины, которую требуется запустить. 

Протестируйте и выполните модуль Runbook еще раз, чтобы убедиться, что он запускает виртуальную машину.

## <a name="use-input-parameters"></a>Использование входных параметров

В настоящее время в качестве имен группы ресурсов и виртуальной машины модуль Runbook использует жестко заданные значения.
Теперь давайте добавим код, который извлекает эти значения из входных параметров.

Вы используете `sys.argv` переменную, чтобы получить значения параметров.
Добавьте следующий код в модуль Runbook сразу после других инструкций `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Он импортирует модуль `sys` и создает две переменные для хранения имен группы ресурсов и виртуальной машины.
Обратите внимание, что элемент списка аргументов (`sys.argv[0]`) является именем сценария, а не вводится пользователем.

Теперь можно изменить две последние строки runbook, чтобы использовать значения входных параметров вместо использования жестко запрограммированных значений:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

При запуске модуля Runbook Python (из колонки **Тест** или как опубликованного модуля) можно ввести значения параметров в колонке **Запуск Runbook** в разделе **Параметры**.

![Поле значений параметров](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Когда вы начнете вводить значение в первое поле, отобразится второе поле и так далее. Таким образом можно ввести любое количество значений параметров.

Все данные доступны для скрипта как `sys.argv` массива, как показано в коде, который был только что добавлен.

В качестве значения первого параметра введите имя своей группы ресурсов, а в качестве значения второго параметра — имя виртуальной машины, которую необходимо запустить.

![Введение значений параметров](media/automation-first-runbook-textual-python/runbook-python-params.png)

Нажмите кнопку **ОК**, чтобы запустить модуль Runbook. Будет выполнен модуль Runbook, который запустит указанную виртуальную машину.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как начать работу с модулями Runbook, см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Чтобы получить дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях, обратитесь к статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md)
* Дополнительные сведения о разработке для Azure с использованием Python см. в статье [Azure для разработчиков Python](https://docs.microsoft.com/python/azure/?view=azure-python).
* Примеры модулей Runbook Python 2 см. на странице [Azure для разработчиков Python](https://docs.microsoft.com/python/azure/?view=azure-python).

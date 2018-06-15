---
title: Кластеры MATLAB на виртуальных машинах | Документация Майкрософт
description: Использование виртуальных машин Microsoft Azure для создания кластеров MATLAB Distributed Computing Server для запуска параллельных рабочих нагрузок MATLAB с ресурсоемкими вычислениями
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 695833fb12c0c7a130e98fe9b3bdfa502672ab29
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914924"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Создание кластеров MATLAB Distributed Computing Server на виртуальных машинах Azure
Используя виртуальные машины Microsoft Azure, вы можете создать один или несколько кластеров MATLAB Distributed Computing Server, в которых можно запускать параллельные рабочие нагрузки MATLAB для ресурсоемких вычислений. Установите программное обеспечение MATLAB Distributed Computing Server на виртуальную машину для использования в качестве базового образа. Затем примените шаблон быстрого запуска Azure или сценарий Azure PowerShell (доступный на [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) для развертывания кластера и управления им. После развертывания подключитесь к кластеру, чтобы запустить рабочие нагрузки.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>О MATLAB и MATLAB Distributed Computing Server
Платформа [MATLAB](http://www.mathworks.com/products/matlab/) оптимизирована для решения инженерных и научных задач. Пользователи MATLAB, работающие с задачами крупномасштабной симуляции и обработки данных, могут задействовать продукты MathWorks для параллельных вычислений, чтобы ускорить рабочие нагрузки с ресурсоемкими вычислениями, используя преимущества вычислительных кластеров и служб Grid. [Parallel Computing Toolbox](http://www.mathworks.com/products/parallel-computing/) позволяет пользователям MATLAB параллелизировать приложения и задействовать преимущества многоядерных процессоров, графических процессоров и вычислительных кластеров. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) позволяет пользователям MATLAB задействовать множество компьютеров в вычислительном кластере.

Используя виртуальные машины Azure, можно создавать кластеры MATLAB Distributed Computing Server, в которых доступны те же механизмы для отправки параллельных рабочих нагрузок, что и в локальных кластерах. Такими рабочими нагрузками могут быть интерактивные задания, пакетные задания, независимые задачи и взаимодействующие задачи. Использование Azure в сочетании с платформой MATLAB имеет множество преимуществ по сравнению с подготовкой и использованием традиционного локального оборудования. Среди этих преимуществ: поддержка широкого диапазона размеров виртуальных машин, возможность создавать кластеры по требованию для оплаты только тех вычислительных ресурсов, которые используются, а также возможность тестировать модели в масштабе.  

## <a name="prerequisites"></a>предварительным требованиям
* **Клиентский компьютер** — необходим клиентский компьютер на базе Windows для обмена данными с Azure и кластером MATLAB Distributed Computing Server после развертывания.
* **Azure PowerShell** — ознакомьтесь с разделом [Установка и настройка Azure PowerShell](/powershell/azure/overview) , чтобы установить этот компонент на клиентском компьютере.
* **Подписка Azure** — если ее нет, можно за пару минут создать [бесплатную учетную запись](https://azure.microsoft.com/free/) . Для больших кластеров можно использовать подписку с оплатой по мере использования или другие варианты приобретения.
* **Квота на виртуальные ЦП** — чтобы развернуть большой кластер или несколько кластеров MATLAB Distributed Computing Server, вам, возможно, потребуется увеличить квоту на виртуальные ЦП. Чтобы увеличить квоту, бесплатно [отправьте запрос в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **Лицензии MATLAB, Parallel Computing Toolbox и MATLAB Distributed Computing Server** — скрипты предполагают использование [MathWorks Hosted License Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) для всех лицензий.  
* **Программное обеспечение MATLAB Distributed Computing Server** — будет установлено на виртуальную машину, используемую в качестве базового образа виртуальной машины для виртуальных машин кластера.

## <a name="high-level-steps"></a>Шаги высокого уровня
Чтобы использовать виртуальные машины Azure для кластеров MATLAB Distributed Computing Server, требуется выполнить следующие действия. Подробные инструкции см. в сопроводительной документации по шаблону быстрого запуска и сценариям на сайте [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Создание базового образа виртуальной машины**  

   * Загрузите и установите программное обеспечение MATLAB Distributed Computing Server на эту виртуальную машину.

     > [!NOTE]
     > Этот процесс может занять несколько часов, но его требуется выполнить только один раз для каждой используемой версии MATLAB.   
     >
     >
2. **Создание одного или нескольких кластеров**  

   * Используйте предоставленный скрипт PowerShell или шаблон быстрого запуска для создания кластера из базового образа виртуальной машины.   
   * Управление кластерами осуществляется с помощью предоставленного скрипта PowerShell, который позволяет перечислять, приостанавливать, возобновлять и удалять кластеры.

## <a name="cluster-configurations"></a>Конфигурации кластеров
В настоящее время шаблон и скрипт для создания кластеров позволяют создать единую топологию MATLAB Distributed Computing Server. При необходимости можно создать один или несколько дополнительных кластеров; при этом в каждом кластере может быть разное число рабочих виртуальных машин, различные размеры виртуальных машин и т. д.

### <a name="matlab-client-and-cluster-in-azure"></a>Клиент и кластер MATLAB в Azure
Узел клиента MATLAB, узел планировщика заданий MATLAB и узлы рабочих ролей MATLAB Distributed Computing Server настраиваются как виртуальные машины Azure в виртуальной сети, как показано на следующем рисунке.


* Чтобы использовать кластер, подключитесь к узлу клиента с помощью удаленного рабочего стола. На узле клиента выполняется клиент MATLAB.
* Узел клиента содержит общую папку, доступную для всех рабочих узлов.
* Для проверки лицензий на все программное обеспечение MATLAB используется диспетчер лицензий MathWorks Hosted License Manager.
* По умолчанию на виртуальных машинах рабочей роли создается по одному рабочему узлу MATLAB Distributed Computing Server на виртуальный ЦП, но это количество можно изменить.

## <a name="use-an-azure-based-cluster"></a>Использование кластера на базе Azure
Как и в других типах кластеров MATLAB Distributed Computing Server, в клиенте MATLAB (на клиентской виртуальной машине) необходимо использовать диспетчер профилей кластеров (Cluster Profile Manager), чтобы создать профиль кластера планировщика заданий MATLAB.

![Диспетчер профилей кластеров](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Дополнительная информация
* Подробные инструкции по развертыванию и управлению кластерами MATLAB Distributed Computing Server в Azure см. в репозитории [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster), содержащем шаблоны и сценарии.
* Посетите [сайт MathWorks](http://www.mathworks.com/) с подробной документацией по MATLAB и MATLAB Distributed Computing Server.

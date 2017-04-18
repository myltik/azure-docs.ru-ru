---
title: "Развертывание кластера контейнера Docker в Azure | Документация Майкрософт"
description: "Развертывание решений Kubernetes, DC/OS или Docker Swarm в службе контейнеров Azure с помощью портала Azure или шаблона Resource Manager."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure, dcos, swarm, kubernetes, служба контейнеров azure, acs"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 71903120a77aa4d8fd58ea600488d3464570b0cb
ms.lasthandoff: 04/06/2017

---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-portal"></a>Развертывание решения для размещения контейнера Docker с помощью портала Azure



Служба контейнеров Azure предусматривает быстрое развертывание популярных решений с открытым кодом для кластеризации и оркестрации контейнеров. В этом документе описано развертывание кластера Службы контейнеров Azure с использованием портала Azure или шаблона быстрого запуска Azure Resource Manager. 

Вы также можете развернуть кластер Службы контейнеров Azure с помощью [Azure CLI 2.0](container-service-create-acs-cluster-cli.md) или с помощью интерфейсов API Службы контейнеров Azure.

Чтобы изучить общие положения, ознакомьтесь с разделом [Общие сведения о службе контейнеров Azure](container-service-intro.md).


## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure.** Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Для более крупных кластеров можно использовать подписку с оплатой по мере использования или другие варианты приобретения.

    > [!NOTE]
    > Объем использования подписки Azure и [квоты на ресурсы](../azure-subscription-service-limits.md), включая квоты на ядра, могут ограничивать размер развертываемого кластера. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](../azure-supportability/how-to-create-azure-support-request.md). Это бесплатная услуга.
    >

* **Открытый ключ RSA (SSH).** При развертывании на портале или с помощью одного из шаблонов быстрого запуска Azure необходимо предоставить открытый ключ, используемый для аутентификации в виртуальных машинах Службы контейнеров Azure. Инструкции по созданию ключей RSA (SSH) см. в соответствующих статьях для [OS X, Linux](../virtual-machines/linux/mac-create-ssh-keys.md) или [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Секрет и идентификатор клиента субъекта-службы (только для Kubernetes).** Дополнительные сведения и рекомендации по созданию субъекта-службы Azure Active Directory см. в разделе [Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Создание кластера с помощью портала Azure
1. Войдите на портал Azure, выберите **Создать** и найдите **службу контейнеров Azure** в Azure Marketplace.

    ![Служба контейнеров Azure в Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Выберите **Служба контейнеров Azure** и щелкните **Создать**.

3. В колонке **Основные сведения** задайте следующие параметры.

    * **Orchestrator** — выберите один из оркестраторов контейнера для развертывания в кластере.
        * **DC/OS** — развертывает кластер DC/OS.
        * **Swarm** — развертывает кластер Docker Swarm.
        * **Kubernetes** — развертывает кластер Kubernetes.
    * **Подписка**— выберите подписку Azure.
    * **Группа ресурсов** — введите имя новой группы ресурсов для развертывания.
    * **Расположение**— выберите регион Azure для развертывания службы контейнеров Azure. Проверьте [доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).
    
    ![Основные параметры](media/container-service-deployment/acs-portal3.png)  <br />
    
    Когда будете готовы продолжить, нажмите кнопку **ОК** .

4. В колонке **Master configuration** (Конфигурация главных узлов) введите указанные ниже параметры для главных узлов Linux в кластере (некоторые параметры относятся к каждому оркестратору).

    * **Master DNS name** (DNS-имя главного узла) — префикс, используемый для создания уникального полного доменного имени главного узла. Полное доменное имя главного узла имеет вид *префикс*mgmt.*расположение*.cloudapp.azure.com.
    * **Имя пользователя** — имя пользователя для учетной записи на каждой виртуальной машине Linux в кластере.
    * **Открытый ключ RSA (SSH)** — добавьте открытый ключ, который будет использоваться для аутентификации в виртуальных машинах Linux. Важно, чтобы ключ не был разбит разрывами строки, но содержал префикс `ssh-rsa`. Постфикс `username@domain` добавлять необязательно. Ключ должен выглядеть так: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 
    * **Субъект-служба** — если вы выбрали оркестратор Kubernetes, введите **идентификатор клиента субъекта-службы** (также называемый appId) Azure Active Directory и **секрет клиента субъекта-службы** (пароль). Дополнительные сведения см. в статье [о субъекте-службе для кластера Kubernetes](container-service-kubernetes-service-principal.md).
    * **Количество главных серверов** — количество главных серверов в кластере.
    * **VM diagnostics** (Диагностика виртуальных машин) — для некоторых оркестраторов на главных узлах можно включить диагностику виртуальных машин.

    ![Конфигурация главных узлов](media/container-service-deployment/acs-portal4.png)  <br />

    Когда будете готовы продолжить, нажмите кнопку **ОК** .

5. В колонке **Конфигурация агента** введите следующие сведения.

    * **Agent count** (Количество агентов) — начальное количество агентов в масштабируемом наборе агента для Docker Swarm и Kubernetes. Для DC/OS этот параметр указывает на начальное количество агентов в частном масштабируемом наборе. Кроме того, для DC/OS создается общедоступный масштабируемый набор с заранее определенным количеством агентов. Количество агентов в этом общедоступном масштабируемом наборе определяется с учетом количества главных узлов в кластере: один общедоступный агент для одного главного узла и два общедоступных агента для трех или пяти главных узлов.
    * **Размер виртуальной машины агента**— размер виртуальных машин агентов.
    * **Операционная система** — в настоящее время этот параметр доступен только в том случае, если вы выбрали оркестратор Kubernetes. Выберите дистрибутив Linux или операционную систему Windows Server для агентов. Этот параметр определяет, может ли кластер запускать приложения-контейнеры Windows или Linux. 

        > [!NOTE]
        > Поддержка контейнера Windows для кластеров Kubernetes находится на этапе предварительной версии. Для кластеров DC/OS и Swarm в настоящее время поддерживаются только агенты Linux в Службе контейнеров Azure.

    * **Учетные данные агента** — если вы выбрали операционную систему Windows, введите **имя пользователя** и **пароль** администратора для виртуальных машин агента. 

    ![Конфигурация агента](media/container-service-deployment/acs-portal5.png)  <br />

    Когда будете готовы продолжить, нажмите кнопку **ОК** .

6. Когда проверка службы завершится, нажмите кнопку **ОК**.

    ![Проверка](media/container-service-deployment/acs-portal6.png)  <br />

7. Просмотрите условия. Нажмите кнопку **Создать**, чтобы начать развертывание.

    Если вы решили выполнить развертывание на портале Azure, вы сможете просматривать состояние развертывания.

    ![Состояния развертывания](media/container-service-deployment/acs-portal8.png)  <br />

Развертывание занимает несколько минут. Затем кластер Службы контейнеров Azure можно использовать.


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Создание кластера с помощью шаблона быстрого запуска
Для развертывания кластера в Службе контейнеров Azure можно использовать шаблоны быстрого запуска Azure. Предоставляемые шаблоны быстрого запуска можно изменять, включив дополнительную или расширенную конфигурацию Azure. Чтобы создать кластер Службы контейнеров Azure с помощью шаблона быстрого запуска Azure, вам потребуется подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Чтобы развернуть кластер с помощью шаблона или Azure CLI 2.0, выполните приведенные ниже действия (см. [инструкции по установке и настройке](/cli/azure/install-az-cli2)).

> [!NOTE] 
> В системе Windows эти же действия можно выполнить с помощью Azure PowerShell. Инструкции см. далее в этом разделе. Кроме того, шаблон можно развернуть на [портале](../azure-resource-manager/resource-group-template-deploy-portal.md) или с помощью других методов.

1. Чтобы развернуть кластер DC/OS, Docker Swarm или Kubernetes, выберите на GitHub один из следующих шаблонов быстрого запуска. Ниже приведен неполный список шаблонов. Шаблоны DC/OS и Swarm отличаются только выбором оркестратора по умолчанию.

    * [Шаблон DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).
    * [шаблон Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm);
    * [Шаблон Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

2. Войдите в учетную запись Azure (`az login`) и подключите Azure CLI к подписке Azure. Чтобы просмотреть подписку Azure по умолчанию, выполните следующую команду.

    ```azurecli
    az account show
    ```
    
    Если у вас есть несколько подписок и вы хотите выбрать другую подписку по умолчанию, выполните командлет `az account set --subscription`, указав идентификатор или имя этой подписки.

3. Мы советуем использовать для развертывания новую группу ресурсов. Чтобы создать группу ресурсов, выполните команду `az group create`, указав ее имя и расположение. 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Создайте файл JSON, содержащий необходимые параметры шаблона. Скачайте файл параметров `azuredeploy.parameters.json`, доступный с шаблоном `azuredeploy.json` Службы контейнеров Azure в GitHub. Введите необходимые значения параметров кластера. 

    Например, чтобы использовать [шаблон DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), укажите значения параметров `dnsNamePrefix` и `sshRSAPublicKey`. Описания и значения других параметров см. в файле `azuredeploy.json`.  
 

5. Создайте кластер Службы контейнеров, передав файл параметров развертывания с помощью следующей команды, где:

    * **RESOURCE_GROUP** — имя группы ресурсов, созданной на предыдущем этапе.
    * **DEPLOYMENT_NAME** (необязательный) — имя развертывания.
    * **TEMPLATE_URI** — расположение файла развертывания `azuredeploy.json`. Этот URI должен быть необработанным файлом (RAW), а не указателем на пользовательский интерфейс GitHub. Чтобы найти этот URI, выберите в GitHub файл `azuredeploy.json` и нажмите кнопку **RAW**.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Параметры также можно указать как строку в формате JSON в командной строке. Используйте команду, аналогичную приведенной ниже.

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > Развертывание занимает несколько минут.
    > 

### <a name="equivalent-powershell-commands"></a>Аналогичные команды PowerShell
Шаблон кластера Службы контейнеров Azure также можно развернуть с помощью PowerShell. В этом документе используется [модуль Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/)версии 1.0.

1. Чтобы развернуть кластер DC/OS, Docker Swarm или Kubernetes, выберите на GitHub один из следующих шаблонов быстрого запуска. Ниже приведен неполный список шаблонов. Обратите внимание, что шаблоны DC/OS и Swarm отличаются только выбором оркестратора по умолчанию.

    * [Шаблон DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).
    * [шаблон Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm);
    * [Шаблон Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

2. Прежде чем создать кластер в подписке Azure, убедитесь, что для сеанса PowerShell выполнен вход в Azure. Это можно сделать с помощью команды `Get-AzureRMSubscription` .

    ```powershell
    Get-AzureRmSubscription
    ```

3. Войти в Azure можно с помощью команды `Login-AzureRMAccount` .

    ```powershell
    Login-AzureRmAccount
    ```

4. Мы советуем использовать для развертывания новую группу ресурсов. Чтобы создать группу ресурсов, используйте команду `New-AzureRmResourceGroup`, указав имя группы ресурсов и регион назначения.

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. После создания группы ресурсов можно создать кластер, используя следующую команду: Универсальный код ресурса (URI) нужного шаблона указывается в параметре `-TemplateUri`. При выполнении этой команды в PowerShell отобразится запрос на ввод значений параметров развертывания.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Выбор параметров шаблона
Если вы знакомы с PowerShell, вы знаете, что можете проходить по кругу все доступные параметры для командлета. Для этого введите знак минус (-) и нажмите клавишу TAB. Точно так же это работает с параметрами, которые вы определили в шаблоне. Сразу после ввода имени шаблона командлет выбирает шаблон, анализирует его параметры и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона. Если вы забыли значение обязательного параметра, PowerShell подскажет его вам.

Вот полная версия команды, в которой указаны параметры. Укажите собственные значения для имен ресурсов.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас есть работающий кластер, выберите ссылки ниже, чтобы узнать о возможностях подключения и управления.

* [Подключение к кластеру службы контейнеров Azure](container-service-connect.md)
* [Работа со службой контейнеров Azure и DC/OS](container-service-mesos-marathon-rest.md)
* [Работа со службой контейнеров Azure и Docker Swarm](container-service-docker-swarm.md)
* [Работа со службой контейнеров Azure и Kubernetes](container-service-kubernetes-walkthrough.md)


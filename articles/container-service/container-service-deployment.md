---
title: "Развертывание кластера контейнера Docker в Azure | Документация Майкрософт"
description: "Развертывание кластера Службы контейнеров Azure с помощью портала Azure или шаблона Azure Resource Manager."
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
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Развертывание кластера службы контейнеров Azure



Служба контейнеров Azure предусматривает быстрое развертывание популярных решений с открытым кодом для кластеризации и оркестрации контейнеров. В этом документе описано развертывание кластера Службы контейнеров Azure с использованием портала Azure или шаблона быстрого запуска Azure Resource Manager. 

> [!NOTE]
> Поддержка Kubernetes в службе контейнеров Azure сейчас доступна в предварительной версии.

Кроме того, его также можно развернуть с помощью [Azure CLI 2.0 (предварительная версия)](container-service-create-acs-cluster-cli.md) или API-интерфейсов Службы контейнеров Azure.



## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure.** Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Открытый ключ SSH.** При развертывании на портале или с помощью одного из шаблонов быстрого запуска Azure необходимо предоставить открытый ключ, используемый для проверки подлинности при входе в виртуальные машины Службы контейнеров Azure. Инструкции по созданию ключей SSH см. в соответствующих статьях для [OS X, Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) или [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **Секрет и идентификатор клиента субъекта-службы (только для Kubernetes).** Дополнительные сведения и рекомендации по созданию субъекта-службы см. в [этой статье](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Создание кластера с помощью портала Azure
1. Войдите на портал Azure, выберите **Создать** и найдите **службу контейнеров Azure** в Azure Marketplace.

    ![Служба контейнеров Azure в Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Выберите **службу контейнеров Azure** и щелкните **Создать**.

    ![Создание службы контейнеров](media/container-service-deployment/acs-portal2.png)  <br />

3. Введите следующие сведения:

    * **Имя пользователя** — имя пользователя, которое будет использоваться для учетной записи на каждой виртуальной машине, а также в масштабируемых наборах виртуальных машин в кластере Службы контейнеров Azure.
    * **Подписка**— выберите подписку Azure.
    * **Группа ресурсов**— создайте группу ресурсов или выберите имеющуюся.
    * **Расположение**— выберите регион Azure для развертывания службы контейнеров Azure.
    * **Открытый ключ SSH** — добавьте открытый ключ, который будет использоваться для проверки подлинности при входе в виртуальные машины Службы контейнеров Azure. Важно, чтобы ключ не был разбит разрывами строки, но содержал префикс `ssh-rsa`. Постфикс `username@domain` добавлять необязательно. Ключ должен выглядеть так: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Когда будете готовы продолжить, нажмите кнопку **ОК** .

    ![Основные параметры](media/container-service-deployment/acs-portal3.png)  <br />

5. Выберите тип оркестрации. Доступны следующие параметры.

  * **DC/OS** — развертывает кластер DC/OS.
  * **Swarm** — развертывает кластер Docker Swarm.
  * **Kubernetes** — развертывает кластер Kubernetes.


6. Когда будете готовы продолжить, нажмите кнопку **ОК** .

    ![Выбор оркестратора](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Если в раскрывающемся списке выбран вариант **Kubernetes**, вам нужно ввести идентификатор клиента субъекта-службы и секрет клиента субъекта-службы. Дополнительные сведения см. в статье [о субъекте-службе для кластера Kubernetes](container-service-kubernetes-service-principal.md).

    ![Выбор субъекта-служба для Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. В колонке параметров **службы контейнеров Azure** введите следующие сведения.

    * **Количество главных серверов** — количество главных серверов в кластере. Если выбран вариант Kubernetes, количество главных серверов по умолчанию равно 1.
    * **Agent count** (Количество агентов) — начальное количество агентов в масштабируемом наборе агента для Docker Swarm и Kubernetes. Для DC/OS этот параметр указывает на начальное количество агентов в частном масштабируемом наборе. Кроме того, для DC/OS создается общедоступный масштабируемый набор с заранее определенным количеством агентов. Количество агентов в этом наборе определяется с учетом количества главных серверов в кластере:&1; общедоступный агент для&1; главного сервера,&2; общедоступных агента для&3; или&5; главных серверов.
    * **Размер виртуальной машины агента**— размер виртуальных машин агентов.
    * **Префикс DNS** — уникальное имя, используемое в качестве префикса для полных доменных имен службы.
    * **VM diagnostics** (Диагностика виртуальных машин) — для некоторых оркестраторов можно включить диагностику виртуальных машин.

8. Когда будете готовы продолжить, нажмите кнопку **ОК** .

    ![Параметры Службы контейнеров](media/container-service-deployment/acs-portal5.png)  <br />

9. Когда завершится проверка службы, нажмите кнопку **ОК** .

    ![Проверка](media/container-service-deployment/acs-portal6.png)  <br />

10. Просмотрите условия. Нажмите кнопку **Приобрести**, чтобы начать развертывание.

    ![Purchase](media/container-service-deployment/acs-portal7.png)  <br />

    Если вы решили выполнить развертывание на портале Azure, вы сможете просматривать состояние развертывания.

    ![Состояния развертывания](media/container-service-deployment/acs-portal8.png)  <br />

Развертывание занимает несколько минут. Затем кластер Службы контейнеров Azure можно использовать.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Создание кластера с помощью шаблона быстрого запуска
Для развертывания кластера в Службе контейнеров Azure можно использовать шаблоны быстрого запуска Azure. Предоставляемые шаблоны быстрого запуска можно изменять, включив дополнительную или расширенную конфигурацию Azure. Чтобы создать кластер Службы контейнеров Azure с помощью шаблона быстрого запуска Azure, вам потребуется подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Чтобы развернуть кластер с помощью шаблона или Azure CLI 2.0 (предварительная версия), выполните приведенные ниже действия (см. [инструкции по установке и настройке](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> В системе Windows эти же действия можно выполнить с помощью Azure PowerShell. Инструкции см. далее в этом разделе. Кроме того, шаблон можно развернуть на [портале](../azure-resource-manager/resource-group-template-deploy-portal.md) или с помощью других методов.

1. Чтобы развернуть кластер DC/OS, Docker Swarm или Kubernetes, выберите на GitHub один из следующих шаблонов. Обратите внимание, что шаблоны DC/OS и Swarm отличаются только выбором оркестратора по умолчанию.

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

1. Чтобы развернуть кластер DC/OS, Docker Swarm или Kubernetes, выберите один из следующих шаблонов. Обратите внимание, что шаблоны DC/OS и Swarm отличаются только выбором оркестратора по умолчанию.

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

Ниже показана полная версия команды, в которой указаны параметры. Вы можете указать собственные значения для имен ресурсов.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас есть работающий кластер, выберите ссылки ниже, чтобы узнать о возможностях подключения и управления.

* [Подключение к кластеру службы контейнеров Azure](container-service-connect.md)
* [Работа со службой контейнеров Azure и DC/OS](container-service-mesos-marathon-rest.md)
* [Работа со службой контейнеров Azure и Docker Swarm](container-service-docker-swarm.md)
* [Работа со службой контейнеров Azure и Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->



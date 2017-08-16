---
title: "Решение \"Контейнеры\" в Azure Log Analytics | Документация Майкрософт"
description: "Решение \"Контейнеры\" в Log Analytics позволяет централизованно просматривать узлы контейнеров Docker и Windows, а также управлять ими."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte;banders
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 5fe0c4c5642fcaa83bcfc830e64600986b8fbf7f
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---
# <a name="containers-preview-solution-in-log-analytics"></a>Решение "Контейнеры" (предварительная версия) в Log Analytics

![Символ решения "Контейнеры"](./media/log-analytics-containers/containers-symbol.png)

В этой статье описано, как настроить и использовать решение "Контейнеры" в Log Analytics для централизованного просмотра узлов контейнеров Docker и Windows, а также управление ими. Docker — это система виртуализации программного обеспечения, с помощью которой можно создавать контейнеры, автоматизирующие развертывание программного обеспечения в соответствующей ИТ-инфраструктуре.

Благодаря этому решению можно видеть, какие контейнеры работают на узлах контейнеров и какие образы выполняются в контейнерах. Также можно просматривать подробные сведения аудита, в том числе команды, используемые в контейнерах. Кроме того, вы можете устранять неполадки контейнеров, просматривая централизованные журналы и выполняя в них поиск, без необходимости удаленного просмотра узлов Docker или Windows. Решение позволяет находить контейнеры, содержащие ошибки и использующие слишком много ресурсов на узле. Также у вас есть возможность централизованно просматривать сведения об использовании и производительности ЦП, памяти, хранилища и сети по контейнерам. На компьютерах под управлением Windows можно централизовать и сравнивать журналы из Windows Server, Hyper-V и контейнеров Docker.

На схеме ниже показаны связи между разными узлами контейнера и агентами с OMS.

![Схема контейнеров](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements"></a>Требования к системе
Сначала необходимо выполнить следующие требования.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Поддержка решений для мониторинга контейнеров: оркестратор Docker и платформа ОС 
Ниже представлена таблица поддержки мониторинга операционных систем и оркестрации Docker с помощью Log Analytics. В частности, в таблице описана поддержка мониторинга списка контейнеров, производительности и журналов.   

| | ACS | Linux | Windows | Контейнер<br>Список | Образ —<br>Список | Узел<br>Список | Контейнер<br>Производительность | Контейнер<br>Событие | Событие<br>Журнал | Контейнер<br>Журнал | 
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| kubernetes | Да | Да | | Да | Да | Да | Да | Да | Да | Да | 
| Mesosphere<br>DC/OS | Да | Да | | Да | Да | Да | Да| Да | Да | Да | 
| Docker<br>Swarm | Да | Да | Да | Да | Да | Да | Да | Да | | Да |
| Service<br>Fabric | | | Да | Да | Да | Да | Да | Да | Да | Да | 
| Red Hat Open<br>Shift | | Да | | Да | Да| Да | Да | Да | | Да | 
| Windows Server<br>(изолированный) | | | Да | Да | Да | Да | Да | Да | | Да |
| Linux Server<br>(изолированный) | | Да | | Да | Да | Да | Да | Да | | Да |


### <a name="docker-versions-supported-on-linux"></a>Версии Docker, поддерживаемые в Linux

- Docker 1.11–1.13
- Docker CE и EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Дистрибутивы Linux x64, поддерживаемые в качестве узлов контейнера

- Ubuntu 14.04 LTS и 16.04 LTS
- CoreOS (стабильный выпуск);
- Amazon Linux 2016.09.0;
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 и 7.3
- SLES 12
- RHEL 7.2 и 7.3
- Платформа контейнеров Red Hat OpenShift (OCP) 3.4 и 3.5
- ACS Mesosphere DC/OS 1.7.3–1.8.8
- ACS Kubernetes 1.4.5–1.6
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Поддерживаемая операционная система Windows

- Windows Server 2016
- Юбилейный выпуск Windows 10 (профессиональный или корпоративный)

### <a name="docker-versions-supported-on-windows"></a>Версии Docker, поддерживаемые в Windows

- Docker 1.12 и 1.13
- Docker 17.03.0 

## <a name="installing-and-configuring-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

1. Решение для контейнеров необходимо добавить в рабочую область OMS из [Azure Мarketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) или в соответствии с инструкциями по [добавлению решений Log Analytics из коллекции решений](log-analytics-add-solutions.md).

2. Установите и используйте Docker с OMS.  В зависимости от операционной системы можно выбрать один из следующих методов:

  * В поддерживаемых операционных системах Linux установите и запустите Docker, а затем установите и настройте агент OMS для Linux.  
  * В CoreOS невозможно запустить агент OMS для Linux. Вместо этого можно запустить контейнерную версию агента OMS для Linux. Если вы работаете с контейнерами в облаке "Azure для государственных организаций", то см. раздел [Для всех узлов контейнера Linux, включая CoreOS](#for-all-linux-container-hosts-including-coreos) или [Для всех узлов контейнера Linux Azure для государственных организаций, включая CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos).
  * В Windows Server 2016 и Windows 10 установите модуль и клиент Docker, после чего подключите агент, чтобы собрать сведения и отправить их в Log Analytics.  

### <a name="container-services"></a>Служба контейнеров

- Если вы используете среду Red Hat OpenShift, то см. раздел [Настройка агента OMS для Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
- При наличии кластера Kubernetes, использующего службу контейнеров Azure, см. статью [Мониторинг кластера Службы контейнеров Azure с помощью Microsoft Operations Management Suite (OMS)](../container-service/kubernetes/container-service-kubernetes-oms.md).
- При наличии кластера DC/OS в службе контейнеров Azure дополнительные сведения см. в статье [Мониторинг кластера DC/OS в службе контейнеров Azure с помощью Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
- При наличии среды режима Docker Swarm ознакомьтесь с разделом [Настройка агента OMS для Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
- При использовании контейнеров с Service Fabric см. статью [Общие сведения о Service Fabric](../service-fabric/service-fabric-overview.md).
- Дополнительные сведения о том, как установить и настроить модули Docker на компьютерах под управлением Windows, см. в [этой статье](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

> [!IMPORTANT]
> Docker необходимо запустить **перед** установкой [агента OMS для Linux](log-analytics-agent-linux.md) на узлах контейнера. Если вы уже установили агент перед установкой Docker, то необходимо переустановить агент OMS для Linux. Дополнительные сведения о Docker см. на [веб-сайте Docker](https://www.docker.com).
>
>

Для мониторинга контейнеров необходимо настроить указанные ниже параметры на узлах контейнера.

## <a name="linux-container-hosts"></a>Узлы контейнера Linux

После установки Docker используйте приведенные ниже параметры узла контейнера, чтобы настроить агент для использования с Docker. Сначала необходимо получить идентификатор и ключ рабочей области OMS, которые можно найти, переключившись на [классический портал OMS](https://mms.microsoft.com).  На странице **Обзор** в верхнем меню выберите **Параметры** и перейдите в раздел **Подключенные источники\Серверы с Windows**.  Вы увидите необходимые значения справа от полей **Идентификатор рабочей области** и **Первичный ключ**.  Скопируйте их и вставьте в любой удобный для вас редактор.    

### <a name="for-all-linux-container-hosts-except-coreos"></a>Для всех узлов контейнера Linux, за исключением CoreOS

- Дополнительные сведения и инструкции по установке агента OMS для Linux см. в статье [Подключение компьютеров Linux к Operations Management Suite (OMS)](log-analytics-agent-linux.md).

### <a name="for-all-linux-container-hosts-including-coreos"></a>Для всех узлов контейнера Linux, включая CoreOS

Запустите контейнер OMS, который вы хотите отслеживать. Используйте следующий пример, внеся в него необходимые изменения:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="for-all-azure-government-linux-container-hosts-including-coreos"></a>Для всех узлов контейнера Linux Azure для государственных организаций, включая CoreOS

Запустите контейнер OMS, который вы хотите отслеживать. Используйте следующий пример, внеся в него необходимые изменения:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>Переход от использования установленного агента Linux к использованию агента в контейнере
Если ранее вы использовали установленный напрямую агент и теперь вместо него хотите использовать агент, работающий в контейнере, сначала необходимо удалить агент OMS для Linux. Сведения об удалении агента см. в разделе [Удаление агента OMS для Linux](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux).  

### <a name="configure-an-oms-agent-for-docker-swarm"></a>Настройка агента OMS для Docker Swarm

Агент OMS можно запускать в качестве глобальной службы в Docker Swarm. Используйте приведенные ниже сведения для создания службы агента OMS. Необходимо вставить свой идентификатор рабочей области OMS и первичный ключ.

- Выполните следующую команду на главном узле.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Настройка агента OMS для Red Hat OpenShift
Существует три способа добавления агента OMS в Red Hat OpenShift, чтобы начать сбор данных мониторинга контейнера: 
 
* [установить агент OMS для Linux](log-analytics-agent-linux.md) непосредственно на каждом узле OpenShift;  
* [включить расширение виртуальной машины Log Analytics](log-analytics-azure-vm-extension.md) на каждом узле OpenShift, размещенном в Azure;  
* установить агент OMS как набор daemon-set для OpenShift.  

В этом разделе описаны действия, которые необходимо выполнить для установки агента OMS как набора daemon-set для OpenShift.  

1. Войдите на главный узел OpenShift и скопируйте YAML-файл [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) с портала GitHub на свой главный узел. При этом замените значения идентификатора рабочей области OMS и первичного ключа своими значениями.
2. Выполните следующие команды, чтобы создать проект для OMS и настроить учетную запись пользователя.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Чтобы развернуть набор daemon-set, выполните следующую команду: 
    
    `oc create -f ocp-omsagent.yaml`

5. Чтобы проверить правильность его настроек и работоспособность, введите следующую команду: 

    `oc describe daemonset omsagent`  
    
    Выходные данные должны выглядеть примерно так:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Чтобы применить секреты для защиты идентификатора рабочей области OMS и первичного ключа, когда используется YAML-файл набора daemon-set агента OMS, выполните следующие действия.

1. Войдите на главный узел OpenShift и скопируйте YAML-файл [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) и сценарий создания секретов [ocp secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) с портала GitHub.  Этот сценарий создаст YAML-файл секретов для идентификатора рабочей области OMS и первичного ключа, чтобы защитить ваши секретные сведения.  
2. Выполните следующие команды, чтобы создать проект для OMS и настроить учетную запись пользователя. Сценарий создания секретов запросит ввести идентификатор рабочей области OMS <WSID> и первичный ключ <KEY>, после чего создаст файл ocp-secret.yaml.  
    
    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Разверните файл секретов, выполнив следующую команду:

    `oc create -f ocp-secret.yaml`

5. Проверьте развертывание, выполнив следующую команду: 

    `oc describe secret omsagent-secret`  

    Выходные данные должны выглядеть примерно так:  
    
    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Разверните YAML-файл набора daemon-set агента OMS, выполнив следующую команду: 

    `oc create -f ocp-ds-omsagent.yaml`  
  
7. Проверьте развертывание, выполнив следующую команду: 

    `oc describe ds oms`
  
    Выходные данные должны выглядеть примерно так:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  
    
    Type:   Opaque  
    
     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

### <a name="secure-your-secret-information-for-docker-swarm-and-kubernetes"></a>Защита секретных сведений для Docker Swarm и Kubernetes 

Вы можете защитить секретный идентификатор рабочей области OMS и первичные ключи для служб контейнеров Docker Swarm и Kubernetes.

#### <a name="secure-secrets-for-docker-swarm"></a>Защита секретов для Docker Swarm

Для Docker Swarm после создания секрета для идентификатора рабочей области и первичного ключа вы можете выполнять процесс создания службы Docker для агента OMS. Используйте приведенные ниже сведения для создания секретных данных.

1. Выполните следующую команду на главном узле.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Убедитесь, что секретные данные созданы надлежащим образом.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Запустите следующую команду, чтобы подключить секреты к контейнерному агенту OMS.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="secure-secrets-for-kubernetes-with-yaml-files"></a>Защита секретов для Kubernetes с помощью файлов YAML

Для Kubernetes используйте сценарий, чтобы создать YAML-файл секретов для идентификатора рабочей области и первичного ключа. На странице [OMS Docker Kubernetes в GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) есть файлы, которые можно использовать с секретными данными или без них:

- стандартный файл DaemonSet агента OMS не поддерживает секретные сведения (omsagent.yaml);
- YAML-файл DaemonSet агента OMS использует секретные сведения (omsagent-ds-secrets.yaml) со сценариями формирования секретов, чтобы создать YAML-файл секретов (omsagentsecret.yaml).

Вы можете создать DaemonSet агента OMS с секретными данными или без них.

##### <a name="default-omsagent-daemonset-yaml-file-without-secrets"></a>Стандартный файл YAML DaemonSet агента OMS без секретов

- Для стандартного файла YAML DaemonSet агента OMS замените `<WSID>` и `<KEY>` на ваши WSID и ключ. Скопируйте файл на главный узел и выполните следующую команду:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

##### <a name="default-omsagent-daemonset-yaml-file-with-secrets"></a>Стандартный файл YAML DaemonSet агента OMS с секретами

1. Чтобы использовать DaemonSet агента OMS с секретными сведениями, сначала создайте секреты.
    1. Скопируйте сценарий и файл шаблона секретов в один и тот же каталог.
        - Сценарий создания секретов — secret-gen.sh
        - Шаблон секретов — secret-template.yaml
    2. Выполните сценарий, как показано в следующем примере. Сценарий запрашивает идентификатор рабочей области OMS и первичный ключ, а после их ввода создает YAML-файл секретов, который можно запустить.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Создайте pod секретов, выполнив следующую команду:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Чтобы проверить, выполните следующую команду:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Выходные данные должны выглядеть примерно так:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Выходные данные должны выглядеть примерно так:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Создание свой набор daemon-set omsagent, выполнив команду ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Убедитесь, что DaemonSet агента OMS запущен, что будет выглядеть примерно так:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Для Kubernetes используйте сценарий для создания файла YAML секретов для идентификатора рабочей области и первичного ключа. Используйте сведения из следующего примера с [файлом YAML omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml), чтобы защитить свои секретные сведения.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

## <a name="windows-container-hosts"></a>Узлы контейнеров Windows

### <a name="preparation-before-installing-windows-agents"></a>Подготовка к установке агентов Windows

Настройте службу Docker, прежде чем устанавливать агенты на компьютерах под управлением Windows. Конфигурация позволяет агенту Windows или расширению виртуальной машины Log Analytics использовать TCP-сокет Docker, чтобы агенты могли получить удаленный доступ к управляющей программе Docker и возможность получать данные мониторинга.

#### <a name="to-start-docker-and-verify-its-configuration"></a>Запуск Docker и проверка его конфигурации

Для настройки именованного канала TCP для Windows Server выполните приведенные ниже действия.

1. Включите канал TCP и именованный канал в Windows PowerShell.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Настройте Docker с помощью файла конфигурации для канала TCP и именованного канала. Файл конфигурации расположен в папке C:\ProgramData\docker\config\daemon.json.

    В файле daemon.json потребуется следующее:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Дополнительные сведения о настройке управляющей программы Docker, используемой в контейнерах Windows, см. в статье [Подсистема Docker в Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


### <a name="install-windows-agents"></a>Установка агентов Windows

Чтобы включить мониторинг контейнеров Windows и Hyper-V, установите Microsoft Monitoring Agent (MMA) на компьютерах Windows, которые являются узлами контейнера. Сведения для компьютеров под управлением Windows в локальной среде см. в статье [Подключение компьютеров Windows к Log Analytics](log-analytics-windows-agents.md). Виртуальные машины, запущенные в Azure, следует подключить к Log Analytics с помощью [расширения виртуальной машины](log-analytics-azure-vm-extension.md).

Вы можете отслеживать контейнеры Windows, запущенные в Service Fabric. Однако сейчас для Service Fabric поддерживаются только [виртуальные машины, работающие в Azure](log-analytics-azure-vm-extension.md), и [компьютеры под управлением Windows в локальной среде](log-analytics-windows-agents.md).

Чтобы убедиться, что решение "Контейнеры" настроено правильно, сделайте следующее:

- Проверьте, был ли пакет управления скачан должным образом, и найдите файл *ContainerManagement.xxx*.
    - Файлы должны находиться в папке, расположенной по адресу C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.
- Проверьте правильность идентификатора рабочей области OMS, выбрав **Панель управления** > **Система и безопасность**.
    - Откройте **Microsoft Monitoring Agent** и убедитесь, что сведения о рабочей области правильны.


## <a name="containers-data-collection-details"></a>Сведения о сборе данных из контейнеров
Решение "Контейнеры" собирает различные метрики производительности и данные журналов из узлов контейнеров и контейнеров, в которых включен агент.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для решения "Контейнеры".

| платформа | [Агент OMS для Linux](log-analytics-linux-agents.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

| платформа | [Агент Windows](log-analytics-windows-agents.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

| платформа | [Расширение виртуальной машины Log Analytics](log-analytics-azure-vm-extension.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Таблицы Azure |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

В таблице ниже приведены примеры типов данных, собранных решением "Контейнеры", и типов данных, которые используются при поиске по журналам и в результатах.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Type=Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `Type=ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Список образов контейнеров | `Type=ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Журнал контейнеров | `Type=ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Журнал службы контейнеров | `Type=ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Мониторинг контейнеров
Включив решение на портале OMS, вы увидите плитку **Контейнеры**, на которой отображаются сводные сведения об узлах контейнеров и контейнерах, запущенных на узлах.

![Плитка "Контейнеры"](./media/log-analytics-containers/containers-title.png)

Плитка показывает число контейнеров в среде, число запущенных и остановленных контейнеров, а также число контейнеров, работа которых завершилась сбоем.

### <a name="using-the-containers-dashboard"></a>Использование панели мониторинга "Контейнеры"
Щелкните плитку **Контейнеры**. Отсюда можно открыть представления, упорядоченные по:

* событиям контейнера;
* Ошибки
* состоянию контейнера;
* списку образов контейнеров;
* производительности ЦП и памяти.

Каждая область на панели мониторинга — это визуальное представление поиска по собранным данным.

![Панель мониторинга "Контейнеры"](./media/log-analytics-containers/containers-dash01.png)

![Панель мониторинга "Контейнеры"](./media/log-analytics-containers/containers-dash02.png)

Перейдите в колонку **состояния контейнера** и щелкните верхнюю область, как показано ниже.

![Состояние контейнера](./media/log-analytics-containers/containers-status.png)

Откроется колонка "Поиск по журналам" со сведениями об узлах и работающих в них контейнерах.

![Колонка "Поиск по журналам" для контейнеров](./media/log-analytics-containers/containers-log-search.png)

Здесь можно изменить поисковый запрос таким образом, чтобы найти нужную вам информацию. Дополнительные сведения об использовании поиска по журналам см. в статье [Поиск по журналам в Log Analytics](log-analytics-log-searches.md).

Например, поисковый запрос можно изменить так, чтобы отобразить все остановленные контейнеры вместо работающих. Для этого в поисковом запросе следует изменить условие **Выполняется** на **Остановлено**.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Устранение неполадок с помощью поиска контейнера со сбоем
OMS добавляет к контейнеру пометку **Сбой**, если такой контейнер завершил работу с ненулевым кодом выхода. Обзор ошибок и сбоев в среде можно просмотреть в колонке **Контейнеры со сбоями**.

### <a name="to-find-failed-containers"></a>Поиск контейнеров со сбоями
1. Щелкните колонку **Container Events** (События контейнеров).  
   Колонка ![Container Events](./media/log-analytics-containers/containers-events.png) (События контейнеров)
2. Откроется колонка "Поиск по журналам", в которой отобразится состояние контейнеров, как показано ниже.  
   ![Состояние контейнеров](./media/log-analytics-containers/containers-container-state.png)
3. Затем щелкните число контейнеров со сбоем, чтобы просмотреть дополнительные сведения, включая размер образа и число образов после остановки и сбоя. Разверните пункт **Показать больше**, чтобы просмотреть идентификатор образа.  
   ![Контейнеры со сбоями](./media/log-analytics-containers/containers-state-failed.png)
4. Найдите контейнер, в котором выполняется этот образ. Введите в поисковый запрос код
   `Type=ContainerInventory <ImageID>` для отображения журналов. Можно прокрутить страницу, чтобы просмотреть контейнер со сбоем.  
   ![Контейнеры со сбоями](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Поиск данных контейнера по журналам
При устранении определенной ошибки эта функция помогает узнать, где именно в среде возникает эта ошибка. С помощью журналов следующих типов можно создавать запросы для получения нужных вам сведений.

* **ContainerInventory** — этот тип используется для получения сведений о расположении контейнеров, их именах и образах, которые в них выполняются.
* **ContainerImageInventory** — этот тип используется для поиска сведений, упорядоченных по образам, и для просмотра информации об образах, включая идентификаторы или размеры образов.
* **ContainerLog** — этот тип используется, если требуется найти определенные сведения или записи в журнале ошибок.
* **ContainerServiceLog** — этот тип используется для поиска в журнале аудита данных об управляющей программе Docker, включая команды для запуска, остановки и извлечения.

### <a name="to-search-logs-for-container-data"></a>Поиск данных контейнера по журналам
* Выберите образ, в котором недавно произошел сбой, и найдите журнал ошибок для этого образа. Сначала найдите имя контейнера, в котором выполняется этот образ, выполнив поиск в журнале **ContainerInventory**. Например, выполните поиск по запросу `Type=ContainerInventory ubuntu Failed`  
    ![Поиск контейнеров Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Запишите имя контейнера в строке **Имя** и выполните поиск по журналам, указав это имя. В нашем примере поисковый запрос будет выглядеть так: `Type=ContainerLog adoring_meitner`.

**Просмотр сведений о производительности**

Начиная создавать запрос, прежде всего следует узнать, какие данные можно найти с его помощью. Например, чтобы просмотреть все данные о производительности, попробуйте использовать общий запрос следующего вида:

```
Type=Perf
```

![Производительность контейнеров](./media/log-analytics-containers/containers-perf01.png)

Более наглядно результаты можно отобразить, щелкнув слово **Метрики**.

![Производительность контейнеров](./media/log-analytics-containers/containers-perf02.png)

Чтобы отобразить данные производительности только для конкретного контейнера, введите его имя в правой части запроса.

```
Type=Perf <containerName>
```

Вы увидите список метрик производительности, собранных для отдельного контейнера.

![Производительность контейнеров](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам
При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии с конкретной средой. Сначала можно поэкспериментировать с колонкой **Заметные запросы**, чтобы научиться создавать более сложные запросы.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Запросы по контейнерам](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Сохранение запросов для поиска по журналам
Сохранение запросов — это стандартная функция в Log Analytics. Она позволяет сохранять полезные запросы для использования в будущем.

Создав запрос, который вы считаете полезным, сохраните его, щелкнув **Избранное** в верхней части страницы поиска по журналам. Позднее вы сможете легко открыть его на странице **Моя панель мониторинга**.

## <a name="next-steps"></a>Дальнейшие действия
* [Выполните поиск по журналам](log-analytics-log-searches.md), чтобы просмотреть подробные записи с данными контейнеров.


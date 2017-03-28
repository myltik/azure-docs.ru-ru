---
title: "Подключение к кластеру службы контейнеров Azure | Документация Майкрософт"
description: "Подключение к кластеру Kubernetes, DC/OS или Docker Swarm в службе контейнеров Azure с удаленного компьютера"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 31897e11abfe70ed08381f0d13c6bdabe56c28ed
ms.openlocfilehash: 05ff751255000220be3b59d013b6106473e4732b
ms.lasthandoff: 03/02/2017


---
# <a name="make-a-remote-connection-to-a-kuburnetes-dcos-or-docker-swarm-cluster"></a>Создание удаленного подключения к кластеру Kuburnetes, DC/OS или Docker Swarm
После создания кластера службы контейнеров Azure необходимо подключиться к кластеру, чтобы развертывать рабочие нагрузки и управлять ими. В этой статье описывается подключение к главной виртуальной машине кластера с удаленного компьютера. 

Кластеры Kubernetes, DC/OS и Docker Swarm предоставляют конечные точки HTTP локально. При использовании Kubernetes к этой конечной точке предоставляется безопасный доступ через Интернет, и к ней можно обратиться, выполнив команду `kubectl` в программе командной строки на любом компьютере, подключенном к Интернету. 

В случае с DC/OS и Docker Swarm необходимо создать туннель Secure Shell (SSH) к внутренней системе. После установления туннеля можно выполнять команды, которые используют конечные точки HTTP, и просматривать веб-интерфейс кластера из локальной системы. 


## <a name="prerequisites"></a>Предварительные требования

* Кластер Kubernetes, DC/OS или Swarm, [развернутый в службе контейнеров Azure](container-service-deployment.md).
* Файл закрытого ключа RSA (SSH), соответствующий открытому ключу, добавленному в кластер во время развертывания. В этих командах предполагается, что закрытый ключ SSH хранится на вашем компьютере в папке `$HOME/.ssh/id_rsa`. Дополнительные сведения см. в статьях [Создание пары из открытого и закрытого ключей SSH для виртуальных машин Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) и [Использование SSH с Windows в Azure](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). Если SSH-подключение не работает, может потребоваться [сбросить ключи SSH](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Выполните следующие действия, чтобы установить и настроить `kubectl` на компьютере.

> [!NOTE] 
> В Linux или OS X может потребоваться выполнить команды из этого раздела с помощью `sudo`.
> 

### <a name="install-kubectl"></a>Установка kubectl
Один из способов установки этого инструмента — с помощью команды `az acs kubernetes install-cli` в Azure CLI 2.0. Чтобы выполнить эту команду, обязательно [установите](/cli/azure/install-az-cli2) последнюю версию Azure CLI 2.0 и войдите в учетную запись Azure с помощью команды `az login`.

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Последнюю версию клиента можно также скачать со страницы [списка выпусков Kubernetes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Дополнительные сведения см. в разделе [Installing and Setting up kubectl](https://kubernetes.io/docs/user-guide/prereqs/) (Установка и настройка kubectl).

### <a name="download-cluster-credentials"></a>Скачивание учетных данных кластера
Когда средство `kubectl` будет установлено, скопируйте на локальный компьютер учетные данные кластера. Чтобы получить учетные данные, выполните команду `az acs kubernetes get-credentials`. Передайте имя группы ресурсов и имя ресурса службы контейнеров:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Эта команда загрузит учетные данные кластера в папку `$HOME/.kube/config`, где средство `kubectl` будет их искать.

Вы также можете с помощью `scp` безопасно скопировать файл на локальный компьютер из папки `$HOME/.kube/config` на главной виртуальной машине. Например:

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Если вы работаете в Windows, необходимо использовать Bash на Ubuntu в Windows, клиент безопасного копирования файлов PuTTy или аналогичное средство.



### <a name="use-kubectl"></a>Использование kubectl

После того как вы настроите `kubectl`, вы можете проверить подключение, просмотрев список узлов в кластере:

```console
kubectl get nodes
```

Можно использовать другие команды `kubectl`. Например, вы можете просмотреть панель мониторинга Kubernetes. Сначала запустите прокси для сервера Kubernetes API:

```console
kubectl proxy
```

Теперь пользовательский интерфейс Kubernetes доступен по адресу `http://localhost:8001/ui`.

Дополнительные сведения см. в статье о [быстром запуске Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Подключение к кластеру DC/OS или Swarm

Чтобы использовать кластеры DC/OS и Docker Swarm, развернутые в службе контейнеров Azure, создайте туннель SSH из локальной системы Linux, OS X или Windows. 

> [!NOTE]
> В статье рассматриваются инструкции по туннелированию TCP-трафика по протоколу SSH. Кроме того, можно запустить интерактивный сеанс SSH с одной из внутренних систем управления кластером, но мы не рекомендуем это делать. Работа непосредственно во внутренней системе, вы рискуете случайно изменить конфигурацию.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Создание туннеля SSH в Linux или OS X
Во-первых, чтобы создать туннель SSH в любой из этих операционных систем, вам нужно узнать общедоступное DNS-имя главного сервера с балансировкой нагрузки. Выполните следующие действия.


1. На [портале Azure](https://portal.azure.com) перейдите в группу ресурсов, содержащую кластер службы контейнеров. Разверните группу ресурсов, чтобы отображались все ресурсы. 

2. Щелкните ресурс службы контейнеров и нажмите кнопку **Обзор**. В разделе **Основное** отобразится **полное доменное имя главного кластера**. Сохраните это имя для последующего использования. 

    ![Общедоступное DNS-имя](media/pubdns.png)

    Кроме того, в службе контейнеров можно выполнить команду `az acs show` и в выходных данных найти свойство **Master Profile:fqdn**.

3. Теперь откройте оболочку и выполните команду `ssh`, указав следующие значения: 

    **LOCAL_PORT** — TCP-порт на стороне службы, к которому подключается туннель. Для Swarm задайте порт 2375, а для DC/OS — 80.  
    **REMOTE_PORT** — порт конечной точки, к которой требуется предоставить доступ. Для Swarm используется порт 2375. Для DC/OS используется порт 80.  
    **USERNAME** — имя пользователя, указанное при развертывании кластера.  
    **DNSPREFIX** — префикс DNS, указанный при развертывании кластера.  
    **REGION** — регион, в котором расположена ваша группа ресурсов.  
    **PATH_TO_PRIVATE_KEY** (необязательно) — это путь к закрытому ключу, который соответствует открытому ключу, указанному во время создания кластера. Используйте этот параметр с флагом `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > Для SSH-подключения используется порт 2200, а не стандартный 22. В кластере с несколькими главными виртуальными машинами этот порт используется для подключения к первой главной виртуальной машине.
    > 



Примеры для кластеров DC/OS и Swarm приведены в следующих разделах.    

### <a name="dcos-tunnel"></a>Туннель DC/OS
Чтобы открыть туннель для конечных точек DC/ОС, выполните следующую команду:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> В качестве локального порта вместо 80 можно указать, например, 8888, но в этом случае некоторые ссылки в пользовательском веб-интерфейсе могут не работать.

Теперь вы можете подключаться к конечным точкам DC/OS из локальной системы, используя следующие URL-адреса (при условии, что в качестве локального порта используется 80):

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

Аналогичным образом через этот туннель можно подключаться к интерфейсам REST API каждого приложения.

### <a name="swarm-tunnel"></a>Туннель Swarm
Чтобы открыть туннель для конечной точки Swarm, выполните следующую команду:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

Теперь можно задать переменную среды DOCKER_HOST, как указано далее. Вы можете продолжить использовать интерфейс командной строки Docker в обычном режиме.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Создание туннеля SSH в Windows
Создавать туннели SSH в Windows можно несколькими способами. В этом разделе описывается, как создать туннель с помощью PuTTY.

1. [Скачайте PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) в операционную систему Windows.

2. Запустите приложение.

3. Введите имя узла, которое состоит из имени администратора кластера и общедоступного DNS-имени первого главного сервера в кластере. **Имя узла** будет указано в этом формате: `adminuser@PublicDNSName`. В поле **Порт**введите значение 2200.

    ![Конфигурация PuTTY 1](media/putty1.png)

4. Выберите **SSH > Проверка подлинности**. Добавьте путь к файлу закрытого ключа (в формате PPK) для проверки подлинности. Чтобы создать этот файл из SSH-ключа, используемого для создания кластера, можно использовать средство [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    ![Конфигурация PuTTY 2](media/putty2.png)

5. Выберите **SSH > Туннели** и настройте следующие порты для перенаправления:

    * **исходный порт** — используйте порт 80 для DC/OS или 2375 для Swarm;
    * **конечный порт** — используйте localhost:80 для DC/OS или localhost:2375 для Swarm.

    В следующем примере выполняется настройка для DC/OS. Для Docker Swarm эта процедура аналогична.

    > [!NOTE]
    > При создании этого туннеля нужно, чтобы порт 80 больше нигде не использовался.
    > 

    ![Конфигурация PuTTY 3](media/putty3.png)

6. По завершении выберите **Сеанс > Сохранить**, чтобы сохранить конфигурацию подключения.

7. Чтобы подключиться к сеансу PuTTY, нажмите кнопку **Открыть**. После подключения конфигурацию порта можно просмотреть в журнале событий PuTTY.

    ![Журнал событий PuTTY](media/putty4.png)

После настройки туннеля для DC/OS связанные конечные точки будут доступны по такому адресу:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

После настройки туннеля для Docker Swarm откройте параметры Windows, чтобы указать значение `:2375` для системной переменной среды `DOCKER_HOST`. Теперь вы можете обращаться к кластеру Swarm из интерфейса командной строки Docker.

## <a name="next-steps"></a>Дальнейшие действия
Развертывание контейнеров в кластере и управление ими.

* [Работа со службой контейнеров Azure и Kubernetes](container-service-kubernetes-ui.md)
* [Работа со службой контейнеров Azure и DC/OS](container-service-mesos-marathon-rest.md)
* [Работа со службой контейнеров Azure и Docker Swarm](container-service-docker-swarm.md)



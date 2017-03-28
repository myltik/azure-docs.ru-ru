---
title: "Кластер Azure Kubernetes для Windows | Документация Майкрософт"
description: "Развертывание кластера Kubernetes для контейнеров Windows в Службе контейнеров Azure и начало работы с ним."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c43648dae95d90d0ee9f3d6b5bedfad7ab4889ca
ms.lasthandoff: 03/22/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Приступая к работе с кластером Kubernetes и контейнерами Windows в службе контейнеров Azure


В этой статье показано, как в Службе контейнеров Azure создать кластер Kubernetes, содержащий узлы Windows для запуска контейнеров Windows. 

> [!NOTE]
> Поддержка контейнеров Windows для Kubernetes в Службе контейнеров Azure находится в на этапе предварительной версии. С помощью портала Azure или шаблона Resource Manager создайте кластер Kubernetes с узлами Windows. В настоящее время эта функция недоступна для Azure CLI 2.0.
>



На следующем рисунке показана архитектура кластера Kubernetes в Службе контейнеров Azure с одним главным узлом Linux и двумя узлами агента Windows. 

* Главный узел Linux обслуживает REST API Kubernetes. К нему можно получить доступ по протоколу SSH через порт 22 или с помощью `kubectl` через порт 443. 
* Узлы агента Windows группируются в группы доступности Azure. На них запускаются контейнеры. Доступ к узлам Windows можно получить с помощью туннеля SSH (RDP) через главный узел. Правила Azure Load Balancer добавляются в кластер динамически, в зависимости от предоставляемых служб.


![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Все виртуальные машины размещены в одной частной виртуальной сети и полностью доступны друг для друга. На всех виртуальных машинах выполняются kubelet, Docker и прокси-сервер.

## <a name="prerequisites"></a>Предварительные требования


* **Открытый ключ RSA (SSH).** При развертывании на портале или с помощью одного из шаблонов быстрого запуска Azure необходимо предоставить открытый ключ RSA (SSH), используемый для аутентификации в виртуальных машинах Службы контейнеров Azure. Инструкции по созданию ключей RSA (SSH) см. в соответствующих статьях для [OS X, Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) или [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **Секрет и идентификатор клиента субъекта-службы.** Дополнительные сведения и рекомендации в разделе [Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Создание кластера

Для [создания кластера Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) с узлами агента Windows можно использовать портал Azure. При создании кластера обратите внимание на следующие параметры.

* В колонке **Основные** из списка **Orchestrator** выберите **Kubernetes**. 

  ![Выбор оркестратора Kubernetes](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* В колонке **Master configuration** (Конфигурация главных узлов) введите учетные данные пользователя и субъекта-службы для главных узлов Linux. Выберите 1, 3 или 5 главных узлов.

* В колонке **Конфигурация агента** из списка **Операционная система** выберите **Windows (preview)** (Windows (предварительная версия)). Введите учетные данные администратора для узлов агента Windows.

  ![Выбор агентов Windows](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

Дополнительные сведения см. в статье [Развертывание кластера службы контейнеров Azure](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Используйте программу командной строки `kubectl`, чтобы с локального компьютера подключиться к главному узлу кластера Kubernetes. Инструкции по установке и настройке `kubectl` см. в статье [Подключение к кластеру Службы контейнеров Azure](container-service-connect.md#connect-to-a-kubernetes-cluster). С помощью команд `kubectl` можно получить доступ к пользовательскому веб-интерфейсу Kubernetes для создания рабочих нагрузок контейнера Windows и управления ими.

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

После создания кластера и подключения к нему с помощью `kubectl` можно попытаться запустить базовое веб-приложение Windows и предоставить через Интернет. В этом примере ресурсы контейнера указываются с помощью файла в формате YAML, а затем создаются с помощью команды `kubctl apply`.

1. Чтобы просмотреть список узлов, введите команду `kubectl get nodes`. Чтобы получить полные сведения об узлах, введите приведенную ниже команду.  

    ```
    kubectl get nodes -o yaml
    ```

2. Создайте файл `simpleweb.yaml` и скопируйте в него следующее содержимое. Этот файл настраивает веб-приложение с помощью базового образа ОС Windows Server 2016 Server Core из [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> Конфигурация содержит параметр `type: LoadBalancer`. Этот параметр позволяет предоставить службу в Интернете через Azure Load Balancer. Дополнительные сведения см. в разделе [Балансировка нагрузки контейнеров в кластере Kubernetes в Службе контейнеров Azure](container-service-kubernetes-load-balancing.md).
>

## <a name="start-the-application"></a>Запуск приложения

1. Чтобы запустить приложение, ведите приведенную ниже команду.  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. Чтобы проверить развертывание службы (которое занимает около 30 секунд), введите приведенную ниже команду.  

    ```
    kubectl get pods
    ```

3. Чтобы после запуска службы просмотреть ее внутренний и внешний IP-адреса, введите приведенную команду.

    ```
    kubectl get svc
    ``` 
  
    ![IP-адреса службы Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    Добавление внешнего IP-адреса занимает несколько минут. Пока подсистема балансировки нагрузки не настроит этот внешний адрес, он отображается как `<pending>`.

4. После того, как внешний IP-адрес станет доступен, вы сможете получить доступ к службе через веб-браузер.

    ![Серверное приложение Windows в браузере](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Доступ к узлам Windows
Доступ к узлам Windows может осуществляться с локального компьютера Windows через подключение к удаленному рабочему столу. Мы рекомендуем использовать туннель SSH (RDP), проходящий через главный узел. 

Создавать туннели SSH в Windows можно несколькими способами. В этом разделе описывается, как создать туннель с помощью PuTTY.

1. [Скачайте PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) в операционную систему Windows.

2. Запустите приложение.

3. Введите имя узла, которое состоит из имени администратора кластера и общедоступного DNS-имени первого главного узла в кластере. **Имя узла** будет указано в этом формате: `adminuser@PublicDNSName`. В поле **Порт** введите значение 22.

  ![Конфигурация PuTTY 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Выберите **SSH > Проверка подлинности**. Добавьте путь к файлу закрытого ключа (в формате PPK) для проверки подлинности. Чтобы создать этот файл из SSH-ключа, используемого для создания кластера, можно использовать средство [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

  ![Конфигурация PuTTY 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Выберите **SSH > Туннели** и настройте порты для перенаправления. Так как локальный компьютер Windows уже использует порт 3389, рекомендуется использовать приведенные ниже параметры для связи с узлом Windows 0 и узлом Windows 1. (Придерживайтесь этой же схемы для дополнительных узлов Windows.)

    **Узел Windows 0**

    * **Исходный порт:** 3390
    * **Место назначения:** 10.240.245.5:3389

    **Узел Windows 1**

    * **Исходный порт:** 3391
    * **Место назначения:** 10.240.245.6:3389

    ![Изображение туннелей RDP Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. По завершении выберите **Сеанс > Сохранить**, чтобы сохранить конфигурацию подключения.

7. Чтобы подключиться к сеансу PuTTY, нажмите кнопку **Открыть**. Завершите подключение к главному узлу.

8. Откройте подключение к удаленному рабочему столу. Для подключения к первому узлу Windows в поле **Компьютер** укажите `localhost:3390` и нажмите кнопку **Подключить**. (Для подключения ко второму узлу укажите `localhost:3390` и т. д.) Для завершения подключения укажите пароль локального администратора Windows, заданный во время развертывания.


## <a name="next-steps"></a>Дальнейшие действия

Мы рекомендуем использовать следующие ссылки на ресурсы с материалами по работе с Kubernetes:

* В [учебном курсе по Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) описано, как выполнять развертывание, масштабирование, обновление и отладку контейнерных приложений.
* В [руководстве по Kubernetes](http://kubernetes.io/docs/user-guide/) показано, как выполнять команды в существующем кластере Kubernetes.
* На странице с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) представлены примеры запуска реальных приложений с использованием Kubernetes.

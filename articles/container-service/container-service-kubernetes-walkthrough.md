---
title: "Развертывание кластера службы контейнеров Azure с помощью Kubernetes | Документация Майкрософт"
description: "Развертывание кластера службы контейнеров Azure с помощью Kubernetes"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Обработчик службы контейнеров Microsoft Azure. Пошаговое руководство по использованию Kubernetes

## <a name="deployment"></a>Развертывание

Ниже описаны шаги по развертыванию простого кластера Kubernetes.

1. [Создание ключа SSH](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation).
3. [Настройка субъекта-службы](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md).
4. Нажатие кнопки [Развертывание в Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) в файле сведений и заполнение полей.

## <a name="walkthrough"></a>Пошаговое руководство

После создания кластера Kubernetes у вас будет группа ресурсов со следующими компонентами.

1. 1 главный узел, доступный по протоколу SSH через порт 22, или средство kubectl, доступное через порт 443.

2. Набор узлов в группе доступности.  Доступ к этим узлам может осуществляться через главный узел.  Пример того, как это сделать, см. в статье о [перенаправлении агента](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant).

На следующем рисунке показана архитектура кластера службы контейнеров с 1 главным узлом и 2 агентами.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-walkthrough/kubernetes.png)

На рисунке выше представлены следующие компоненты.

1. **Основные компоненты.** На главном узле выполняются планировщик Kubernetes, сервер API и диспетчер контроллеров.  Порт 443 предоставляется для удаленного управления с помощью интерфейса командной строки kubectl.
2. **Узлы.** Узлы Kubernetes, запущенные в группе доступности.  Балансировщики Azure Load Balancer динамически добавляются в кластер в зависимости от предоставляемых служб. 
3. **Общие компоненты.** На всех виртуальных машинах выполняются агент kubelet, Docker и прокси-сервер.
4. **Сеть.** Всем виртуальным машинам назначается IP-адрес в сети 10.240.0.0/16.  Каждой виртуальной машине назначается подсеть /24 для CIDR модулей с указанием IP-адреса для каждого модуля.  Прокси-сервер, запущенный на каждой виртуальной машине, реализует сеть службы 10.0.0.0/16.

Все виртуальные машины подключены к одной частной виртуальной сети и полностью доступны друг для друга.

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

Выполнив инструкции из этого руководства, вы научитесь:
 * получать доступ к кластеру Kubernetes через SSH;
 * развертывать простое приложение Docker и предоставлять к нему доступ;
 * определять расположение файла конфигурации Kube и получать удаленный доступ к кластеру Kubernetes;
 * использовать `kubectl exec` для выполнения команд в контейнере; 
 * получать доступ к панели мониторинга Kubernetes.

1. Развернув шаблон, запишите полные доменные имена главного узла.
   1. Если используется PowerShell или интерфейс командной строки, выходной параметр находится в разделе OutputsString с именем masterFQDN.
   2. Если используется портал, откройте колонку обзора для ресурса ContainerService, чтобы скопировать полное доменное имя:
     
   ![Изображение масштабирования Docker](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. Используйте SSH, чтобы получить доступ к полному доменному имени главного узла, полученному на шаге 1.

3. Просмотрите узлы и запущенные модули:
  1. Чтобы просмотреть список узлов типа `kubectl get nodes`.  Чтобы получить полные сведения об узлах, добавьте `-o yaml`, чтобы получилось `kubectl get nodes -o yaml`.
  2. Чтобы просмотреть список запущенных модулей типа `kubectl get pods --all-namespaces`.

4. Запустите первый образ Docker: введите `kubectl run nginx --image nginx`.  Будет запущен контейнер Docker nginx в модуле на одном из узлов.

5. Введите `kubectl get pods -o yaml`, чтобы просмотреть подробные сведения о развертывании nginx. Вы увидите IP-адреса узла и модуля.  IP-адрес модуля назначается из CIDR модуля на узле.  Выполните curl с IP-адресом модуля, чтобы увидеть выходные данные nginx, например: `curl 10.244.1.4`

  ![Изображение выполнения curl с IP-адресом модуля](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. Следующий шаг — предоставление развертывания nginx как службы Kubernetes в частной сети 10.0.0.0/16:
  1. Предоставьте службу с помощью команды `kubectl expose deployment nginx --port=80`.
  2. Получите IP-адрес службы `kubectl get service`.
  3. Выполните curl с IP-адресом модуля, например: `curl 10.0.105.199`

  ![Изображение выполнения curl с IP-адресом службы](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. Последний шаг — предоставление открытого доступа к службе.  Это можно сделать, изменив тип службы с `ClusterIP` на `LoadBalancer`:
  1. Измените службу: `kubectl edit svc/nginx`
  2. Измените `type` с `ClusterIP` на `LoadBalancer` и сохраните изменения.  Таким образом Kubernetes создаст балансировщик Azure Load Balancer с общедоступным IP-адресом.
  3. Выполнение всех действий займет 2–3 минуты.  Чтобы просмотреть изменение состояния службы с ожидания на использование внешнего IP-адреса, введите `watch 'kubectl get svc'`.

  ![Изображение выходных данных с переходом от ожидания к использованию внешнего IP-адреса](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. Полученный внешний IP-адрес можно ввести в браузере:

  ![Изображение перехода на страницу nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. Следующий шаг — удаленное управление кластером Kubernetes.  Сначала скачайте и установите Kubectl на свой компьютер:
  * [Kubectl для Windows](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe);
  * [Kubectl для OS X](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl);
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. На главном узле Kubernetes файл конфигурации kube для удаленного доступа расположен в корневом каталоге ~/.kube/config.  Скачайте этот файл на компьютер, присвойте переменную среды KUBECONFIG и запустите kubectl для проверки подключения к кластеру:
  * В Windows воспользуйтесь средством pscp из [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Проверьте, предоставлен ли сертификат, с помощью [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * В OS X или Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. Следующий шаг — удаленное выполнение команд в удаленном контейнере Docker:
  1. Запустите `kubectl get pods`, чтобы отобразить имя модуля nginx.
  2. С помощью этого имени в модуле можно запустить удаленную команду.  Например: `kubectl exec nginx-701339712-retbj date`
  3. Попробуйте запустить удаленный сеанс bash. Например: `kubectl exec nginx-701339712-retbj -it bash`.  На следующем снимке экрана показано выполнение этих команд.

  ![Изображение выполнения curl с IP-адресом модуля](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. Последний шаг — отображение панелей мониторинга:
  1. Запустите `kubectl proxy` для прямого подключения к прокси-серверу.
  2. В браузере перейдите к [панели мониторинга](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all).
  3. Просмотрите и изучите свои модули и службы.
  ![Изображение панели мониторинга Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>Дополнительные сведения

Мы рекомендуем использовать следующие ссылки на ресурсы с материалами по работе с Kubernetes:

1. [Документация по Azure Kubernetes](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Документация сообщества Kubernetes

1. В [учебном курсе по Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) описано, как выполнять развертывание, масштабирование, обновление и отладку контейнерных приложений.
2. В [руководстве по Kubernetes](http://kubernetes.io/docs/user-guide/) показано, как выполнять команды в существующем кластере Kubernetes.
3. На странице с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) представлены примеры запуска реальных приложений с использованием Kubernetes.



<!--HONumber=Nov16_HO3-->



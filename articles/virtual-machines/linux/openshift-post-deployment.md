---
title: "OpenShift в задачах Azure, выполняемых после развертывания | Документация Майкрософт"
description: "Задачи, выполняемые после развертывания OpenShift"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>Задачи, выполняемые после развертывания

Есть дополнительные элементы, которые можно настроить после развертывания кластера OpenShift. В этой статье рассматривается следующее:

- настройка единого входа с помощью Azure Active Directory (AAD);
- настройка OMS для мониторинга OpenShift;
- настройка метрик и ведения журнала;

## <a name="single-sign-on-using-aad"></a>единый вход с помощью AAD.

Чтобы использовать AAD для аутентификации, сначала необходимо создать регистрацию Azure AD App. Этот процесс будет состоять из двух шагов — создания регистрации приложения и настройки разрешений.

### <a name="create-app-registration"></a>Создание регистрации приложения

Для создания регистрации приложения будет использоваться Azure CLI, а для настройки разрешений — графический пользовательский интерфейс (портал). Чтобы создать регистрацию приложений, потребуются сведения для пяти параметров.

- Отображаемое имя. Имя регистрации приложения (например: OCPAzureAD).
- Домашняя страница. URL-адрес консоли OpenShift (например: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console).
- Идентификатор URI. URL-адрес консоли OpenShift (например: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console).
- URL-адрес ответа. Главный общедоступный URL-адрес и имя регистрации приложения (например: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD).
- Пароль. Защищенный пароль (используйте надежный пароль).

В следующем примере создается запись о регистрации приложения с использованием сведений, приведенных выше.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Если команда выполняется успешно, будут получены подобные выходные данные JSON:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Запишите свойство appId, возвращенное из команды. Оно понадобится в дальнейшем.

На **портале Azure**:

1.  Выберите **Azure Active Directory** --> **App Registration** (Регистрация приложений).
2.  Найдите регистрацию своего приложения (например: OCPAzureAD).
3.  В результатах щелкните App Registration (Регистрация приложений).
4.  В колонке параметров щелкните **Требуемые разрешения**.
5.  В колонке "Требуемые разрешения" щелкните **Добавить**.

  ![Регистрация приложения](media/openshift-post-deployment/app-registration.png)

6.  Щелкните шаг 1 "Выбор API" и выберите **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**, а затем нажмите кнопку **Выбор** внизу.

  ![Регистрация приложения. Выбор API](media/openshift-post-deployment/app-registration-select-api.png)

7.  На шаге 2 "Выбор разрешений" в разделе **Делегированные разрешения** выберите **Вход и чтение профиля пользователя** и щелкните **Выбор**.

  ![Доступ к регистрации приложения](media/openshift-post-deployment/app-registration-access.png)

8.  Нажмите кнопку **Done** (Готово).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Настройка OpenShift для аутентификации Azure AD

Чтобы настроить OpenShift для использования в качестве поставщика аутентификации Azure AD необходимо изменить файл **/etc/origin/master/master-config.yaml** на всех главных узлах.

Идентификатор клиента можно найти, выполнив следующую команду интерфейса командной строки:

```azurecli
az account show
```

В файле yaml найдите следующие строки:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Вставьте следующие строки сразу после строк выше:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Идентификатор клиента можно найти, выполнив следующую команду интерфейса командной строки: ```az account show```

Перезапустите основные службы OpenShift на всех главных узлах.

**Источник OpenShift**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**Платформа контейнеров OpenShift с несколькими главными узлами**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Платформа контейнеров OpenShift с одним главным узлом**

```bash
sudo systemctl restart atomic-openshift-master
```

В консоли OpenShift будет показано два варианта аутентификации — htpasswd_auth и **[Регистрация приложения]**.

## <a name="monitor-openshift-with-oms"></a>Мониторинг OpenShift с OMS

Мониторинг OpenShift с OMS можно выполнять с помощью одного из двух вариантов — установки агента OMS на узле виртуальной машины или в контейнере OMS. В этой статье приведены инструкции по развертыванию контейнера OMS.

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>Создание проекта OpenShift для OMS и настройка доступа пользователя

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>Создание файла yaml набора управляющей программы

Создание файла с именем ocp omsagent.yml.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-secret-yaml-file"></a>Создание файла yaml секрета

Чтобы создать файл yaml секрета, требуются два блока данных — идентификатор и общий ключ рабочей области OMS. 

Пример файла ocp-secret.yml 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Замените wsid_data идентификатором рабочей области OMS с кодировкой Base64, а key_data — общим ключом рабочей области OMS с кодировкой Base64.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>Создание секрета и набора управляющей программы

Развертывание файла секрета

```bash
oc create -f ocp-secret.yml
```

Развертывание набора управляющей программы агента OMS

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Настройка метрик и ведения журнала

Шаблон Resource Manager платформы контейнеров OpenShift (OCP) предоставляет входные параметры для включения метрик и ведения журнала,  а предложение платформы контейнеров OpenShift из Marketplace и шаблон Resource Manager источника OpenShift — нет.

Если использовался шаблон Resource Manager OCP, а метрики и ведение журнала не были включены во время установки или было использовано предложение OCP из Marketplace, их можно легко включить позже. При использовании шаблона Resource Manager источника OpenShift требуется предварительная подготовка.

### <a name="openshift-origin-template-pre-work"></a>Предварительная подготовка шаблона источника OpenShift

Подключитесь по SSH к первому главному узлу, используя порт 2200.

Пример

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

Измените файл **/etc/ansible/hosts** и добавьте следующие строки в разделе поставщика удостоверений (# Enable HTPasswdPasswordIdentityProvider).

```yaml
# Setup metrics
openshift_hosted_metrics_deploy=false
openshift_metrics_cassandra_storage_type=dynamic
openshift_metrics_start_cluster=true
openshift_metrics_hawkular_nodeselector={"type":"infra"}
openshift_metrics_cassandra_nodeselector={"type":"infra"}
openshift_metrics_heapster_nodeselector={"type":"infra"}
openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

# Setup logging
openshift_hosted_logging_deploy=false
openshift_hosted_logging_storage_kind=dynamic
openshift_logging_fluentd_nodeselector={"logging":"true"}
openshift_logging_es_nodeselector={"type":"infra"}
openshift_logging_kibana_nodeselector={"type":"infra"}
openshift_logging_curator_nodeselector={"type":"infra"}
openshift_master_logging_public_url=https://kibana.$ROUTING
```

Замените $ROUTING строкой, используемой для свойства **openshift_master_default_subdomain** в том же файле **/etc/ansible/hosts**.

### <a name="azure-cloud-provider-in-use"></a>Использование поставщика облачных служб Azure

На первом главном узле (источник) или узле-бастионе (OCP) подключитесь по протоколу SSH c использованием учетных данных, указанных во время развертывания. Введите следующую команду:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Поставщик облачных служб Azure не используется

На первом главном узле (источник) или узле-бастионе (OCP) подключитесь по протоколу SSH c использованием учетных данных, указанных во время развертывания. Введите следующую команду:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Дальнейшие действия

- [Overview](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Обзор)
- [Приступая к работе с OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
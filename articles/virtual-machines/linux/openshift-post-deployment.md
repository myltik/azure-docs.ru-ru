---
title: Задачи, выполняемые после развертывания OpenShift в Azure | Документация Майкрософт
description: Дополнительные задачи, которые необходимо выполнить после развертывания кластера OpenShift.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: bdfd075b9438ee12e940f3ec4fddebf467c93ca8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796165"
---
# <a name="post-deployment-tasks"></a>Задачи, выполняемые после развертывания

После развертывания кластера OpenShift можно настроить дополнительные элементы. В этой статье рассматриваются следующие задачи:

- Настройка единого входа с использованием Azure Active Directory (Azure AD).
- Как настроить мониторинг OpenShift с помощью Log Analytics
- Настройка метрик и ведения журнала.

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Настройка единого входа с использованием Azure Active Directory

Чтобы использовать Azure Active Directory для аутентификации, прежде всего нужно зарегистрировать приложение в Azure AD. Этот процесс состоит из двух этапов: регистрация приложения и настройка разрешений.

### <a name="create-an-app-registration"></a>Регистрация приложения

В этом примере для регистрации приложения используется Azure CLI, а для настройки разрешений — графический пользовательский интерфейс (портал). Чтобы зарегистрировать приложение, нужно знать следующие сведения для пяти параметров:

- Отображаемое имя. Имя регистрируемого приложения (например, OCPAzureAD).
- Домашняя страница. URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI. URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URL-адрес ответа. Главный общедоступный URL-адрес и имя регистрации приложения (например, https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Пароль. Защищенный пароль (используйте надежный пароль).

В следующем примере регистрируется приложение на основе приведенных выше сведений.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Если команда будет выполнена успешно, вы получите такие выходные данные в формате JSON:

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

На портале Azure выполните следующие действия.

1.  Выберите **Azure Active Directory** > **App Registration** (Регистрация приложений).
2.  Найдите зарегистрированное приложение (например, OCPAzureAD).
3.  В результатах поиска щелкните нужное зарегистрированное приложение.
4.  В области **Параметры** щелкните **Требуемые разрешения**.
5.  В области **Необходимые разрешения** щелкните **Добавить**.

  ![Регистрация приложения](media/openshift-post-deployment/app-registration.png)

6.  Щелкните "Шаг 1. Выбрать API" и выберите **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Щелкните **Выбрать** внизу.

  ![Регистрация приложения. Выбор API](media/openshift-post-deployment/app-registration-select-api.png)

7.  В области "Шаг 2. Выбор разрешений" выберите **Вход и чтение профиля пользователя** в разделе **Делегированные разрешения**, затем щелкните **Выбрать**.

  ![Доступ к регистрации приложения](media/openshift-post-deployment/app-registration-access.png)

8.  Нажмите кнопку **Готово**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Настройка OpenShift для аутентификации Azure AD

Чтобы OpenShift использовал Azure AD в качестве поставщика проверки подлинности, внесите изменения в файл /etc/origin/master/master-config.yaml на всех главных узлах.

Определите идентификатор клиента, выполнив следующую команду интерфейса командной строки:

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

Сразу после указанных выше строк вставьте следующие строки:

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

Определите идентификатор клиента, выполнив следующую команду интерфейса командной строки: ```az account show```

Перезапустите основные службы OpenShift на всех главных узлах.

**Источник OpenShift**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**Платформа контейнеров OpenShift (OCP) с несколькими главными узлами**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Платформа контейнеров OpenShift с одним главным узлом**

```bash
sudo systemctl restart atomic-openshift-master
```

В консоли OpenShift предложены два варианта аутентификации: htpasswd_auth и [Регистрация приложения].

## <a name="monitor-openshift-with-log-analytics"></a>Мониторинг OpenShift с помощью Log Analytics

Есть два способа настроить мониторинг OpenShift с помощью Log Analytics: установить агент OMS на узле виртуальной машины или использовать контейнер OMS. В этой статье приведены инструкции по развертыванию контейнера OMS.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Создание проекта OpenShift для Log Analytics и настройка доступа пользователя

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Создание YAML-файла для набора управляющей программы

Создайте файл с именем ocp-omsagent.yml.

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

## <a name="create-a-secret-yaml-file"></a>Создание YAML-файла для секрета

Чтобы создать YAML-файл секрета, нужно знать два типа сведений — идентификатор и общий ключ рабочей области Log Analytics. 

Ниже представлен пример файла ocp-secret.yml: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Замените здесь строку wsid_data идентификатором рабочей области Log Analytics в кодировке Base64. Затем замените строку key_data общим ключом рабочей области Log Analytics в кодировке Base64.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Создание секрета и набора управляющей программы

Разверните файл секрета:

```bash
oc create -f ocp-secret.yml
```

Разверните набор управляющей программы агента OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Настройка метрик и ведения журнала

Шаблон Azure Resource Manager для платформы контейнеров OpenShift предоставляет входные параметры для включения метрик и ведения журнала, которых нет в предложении платформы контейнеров OpenShift из Marketplace и в шаблоне Resource Manager для OpenShift Origin.

Если вы использовали шаблон Resource Manager OCP и не включили во время установки метрики и ведение журнала, или использовали предложение OCP из Marketplace, вы можете легко включить эти настройки позднее. При работе с шаблоном Resource Manager для OpenShift Origin требуется предварительная подготовка.

### <a name="openshift-origin-template-pre-work"></a>Предварительная подготовка шаблона источника OpenShift

1. Подключитесь по SSH к порту 2200 первого главного узла.

   Пример:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Откройте для редактирования файл /etc/ansible/hosts и добавьте в него следующие строки после раздела поставщика удостоверений (# Enable HTPasswdPasswordIdentityProvider).

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

3. Замените $ROUTING строкой, которая указана в качестве значения для openshift_master_default_subdomain в том же файле /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Использование поставщика облачных служб Azure

Создайте SSH-подключение с первого главного узла (источник) или узла-бастиона (OCP) c использованием учетных данных, указанных во время развертывания. Введите следующую команду:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Поставщик облачных служб Azure не используется

Создайте SSH-подключение с первого главного узла (источник) или узла-бастиона (OCP) c использованием учетных данных, указанных во время развертывания. Введите следующую команду:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Дополнительная информация

- [Overview](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Обзор)
- [Приступая к работе с OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)

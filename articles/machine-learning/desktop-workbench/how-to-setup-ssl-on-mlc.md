---
title: Включение SSL в кластере Azure Machine Learning Compute (MLC) | Документация Майкрософт
description: Инструкции по настройке SSL для вызовов оценки в кластере Azure Machine Learning Compute (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 14f8dd29b7d4185d01529631333de045ad23cdd0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831468"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Включение SSL в кластере Azure Machine Learning Compute (MLC) 

Эти инструкции позволяют настроить SSL для вызовов оценки в кластере Machine Learning Compute (MLC). 

## <a name="prerequisites"></a>предварительным требованиям 

1. Выберите имя CNAME (DNS-имя), которое будет использоваться при выполнении вызовов оценки в режиме реального времени.

2. Создайте сертификат *без пароля* с этим именем CNAME.

3. Если сертификат сохранен в формате, отличном от PEM, преобразуйте его в формат PEM с помощью таких средств, как *openssl*.

После выполнения предварительных требований у вас будут два файла:

* Файл для сертификата, например `cert.pem`. Файл должен содержать полную цепочку сертификатов.
* файл для ключа, например `key.pem`.



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Настройка SSL-сертификата в новом кластере ACS

С помощью интерфейса командной строки Azure Machine Learning выполните следующую команду с параметром `-c`, чтобы создать кластер ACS с присоединенным SSL-сертификатом.

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Настройка SSL-сертификата в существующем кластере ACS

Если вы планируете использовать кластер, созданный без SSL, можете добавить сертификат с помощью командлетов Azure PowerShell.

Необходимо указать ключ и сертификат в необработанном формате PEM. Их можно прочитать из переменных PowerShell.

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Добавьте сертификат в кластер. 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Сопоставление CNAME и IP-адреса

Создайте сопоставление между именем CNAME, выбранном в разделе предварительных требований, и IP-адресом внешнего интерфейса в режиме реального времени (FE). Чтобы узнать IP-адрес FE, запустите приведенную ниже команду. В выходных данных появится поле с именем publicIpAddress, которое содержит IP-адрес внешнего интерфейса кластера в режиме реального времени. Чтобы настроить FQDN, используемое в CNAME для общедоступного IP-адреса, см. инструкции поставщика DNS.



## <a name="auto-detection-of-a-certificate"></a>Автообнаружение сертификата 

Когда вы создаете службу в режиме реального времени с помощью команды `az ml service create realtime`, служба "Машинное обучение Azure" автоматически обнаруживает SSL-сертификат, настроенный в кластере, и автоматически настраивает URL-адрес для оценки с назначенным сертификатом. 

Чтобы просмотреть состояние сертификата, выполните приведенную ниже команду. Обратите внимание на префикс https в URI для оценки и CNAME в имени узла. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```

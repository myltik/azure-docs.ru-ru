---
title: "Защита веб-сервера с помощью SSL-сертификатов в Azure | Документация Майкрософт"
description: "Узнайте, как защитить веб-сервер NGINX с помощью SSL-сертификатов на виртуальной машине Linux в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6b333b75f571e367470037ab9ce8b273fcae5498
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Защита веб-сервера с помощью SSL-сертификатов на виртуальной машине Linux в Azure
Чтобы защитить веб-серверы, можно использовать сертификат Secure Sockets Later (SSL) для шифрования веб-трафика. SSL-сертификаты могут храниться в Azure Key Vault и разрешать безопасное развертывание сертификатов на виртуальных машинах Linux в Azure. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание Azure Key Vault
> * создать или передать сертификат в Key Vault;
> * создание виртуальной машины и установка веб-сервера NGINX;
> * внедрение сертификата в виртуальную машину и настройка NGINX с помощью SSL-привязки.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

При выборе для установки и использования CLI локально упражнений этого учебника требуется, что вы используете Azure CLI версии 2.0.22 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Обзор
Azure Key Vault защищает криптографические ключи и секреты, в том числе сертификаты или пароли. Key Vault помогает оптимизировать управление сертификатами и позволяет контролировать ключи, которые предоставляют доступ к этим сертификатам. Можно создать самозаверяющий сертификат в Key Vault или передать существующий доверенный сертификат.

Вместо того чтобы использовать образ виртуальной машины, включающий встроенные сертификаты, можно внедрить сертификаты в работающую виртуальную машину. Этот процесс гарантирует установку на веб-сервер самых последних сертификатов во время развертывания. Если вы обновляете или заменяете сертификат, вам не нужно создавать новый пользовательский образ виртуальной машины. Последние сертификаты внедряются автоматически при создании дополнительных виртуальных машин. При этом сертификат никогда не покидает платформу Azure и не отображается в скрипте, журнале командной строки или шаблоне.


## <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault
Прежде чем создать Key Vault и сертификаты, выполните командлет [az group create](/cli/azure/group#create), чтобы создать группу ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroupSecureWeb* в расположении *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Затем создайте Key Vault с помощью [az keyvault create](/cli/azure/keyvault#create) и включите его использование при развертывании виртуальной машины. У каждого Key Vault должно быть уникальное имя в нижнем регистре. Замените *<mykeyvault>* в следующем примере собственным уникальным именем Key Vault.

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Создание сертификата и его сохранение в Key Vault
Для использования в рабочей среде следует импортировать действительный сертификат, подписанный доверенным поставщиком, выполнив команду [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). В следующем примере в этом руководстве показано, как можно командой [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) создать самозаверяющий сертификат, использующий политику сертификата по умолчанию.

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Подготовка сертификата для использования с виртуальной машиной
Чтобы использовать сертификата во время создания виртуальной машины, получите идентификатор сертификата, выполнив команду [az keyvault secret list-versions](/cli/azure/keyvault/secret#list-versions). Преобразуйте сертификат, выполнив команду [az vm format-secret](/cli/azure/vm#format-secret). Следующий пример присваивает переменным результаты этих команд, чтобы их было удобно использовать в дальнейшем.

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Создание конфигурации cloud-init для защиты сервера NGINX
[Пакет cloud-init](https://cloudinit.readthedocs.io) — широко используемое средство, используемое для настройки виртуальной машины Linux при ее первой загрузке. Вы можете использовать cloud-init для установки пакетов, записи файлов или настройки пользователей и параметров безопасности. Так как cloud-init выполняется при начальной загрузке, для применения вашей конфигурации не требуются какие-либо дополнительные действия или обязательные агенты.

При создании виртуальной машины сертификаты и ключи хранятся в защищенном каталоге */var/lib/waagent/*. Используйте cloud-init, чтобы автоматизировать добавление сертификата виртуальной машины и настройку веб-сервера. В этом примере нужно установить и настроить веб-сервер NGINX. Этот же процесс можно использовать для установки и настройки Apache. 

Создайте файл *cloud-init-web-server.txt* и вставьте в него приведенную ниже конфигурацию:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Создание защищенной виртуальной машины
Теперь создайте виртуальную машину командой [az vm create](/cli/azure/vm#create). Данные сертификата внедряются из Key Vault с помощью параметра `--secrets`. Передайте конфигурацию cloud-init с параметром `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

На создание виртуальной машины, установку пакетов и запуск приложения может потребоваться несколько минут. Когда виртуальная машина будет создана, запишите значение `publicIpAddress`, отображаемое Azure CLI. Этот адрес используется для доступа к сайту в веб-браузере.

Чтобы разрешить безопасную передачу веб-трафика для виртуальной машины, откройте порт 443 для Интернета командой [az vm open-port](/cli/azure/vm#open-port).

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Тестирование защищенного веб-приложения
Теперь можно открыть веб-браузер и ввести в адресной строке *https://<publicIpAddress>*. Укажите собственный общедоступный IP-адрес, настроенный при создании виртуальной машины. Если используется самозаверяющий сертификат, ответьте согласием на предупреждение системы безопасности.

![Принятие предупреждения о безопасности веб-браузера](./media/tutorial-secure-web-server/browser-warning.png)

На экране отобразится защищенный веб-сайт NGINX, как в показано следующем примере:

![Просмотр работающего защищенного сайта NGINX](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Дальнейшие действия

С помощью этого руководства вы защитили веб-сервер NGINX SSL-сертификатом, который хранится в Azure Key Vault. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание Azure Key Vault
> * создать или передать сертификат в Key Vault;
> * создание виртуальной машины и установка веб-сервера NGINX;
> * внедрение сертификата в виртуальную машину и настройка NGINX с помощью SSL-привязки.

Чтобы увидеть предварительно созданные примеры скриптов виртуальной машины, перейдите по ссылке ниже.

> [!div class="nextstepaction"]
> [Примеры скриптов для виртуальной машины Windows](./cli-samples.md)
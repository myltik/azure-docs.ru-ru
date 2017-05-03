---
title: "Настройка виртуальной машины Linux при первой загрузке в Azure |Документация Майкрософт"
description: "Узнайте, как использовать пакет cloud-init и Key Vault для настройки виртуальных машин Linux при первой загрузке в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 8f86f812cd708d8122ecc507d02fb2ec2c73689f
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Как настроить виртуальную машину Linux при первой загрузке
Для быстрого и согласованного создания виртуальных машин, как правило, применяются средства автоматизации. Наиболее распространенный подход к настройке виртуальной машины при первой загрузке — использование [cloud-init](https://cloudinit.readthedocs.io). В этом руководстве описывается, как использовать пакет cloud-init для автоматической установки пакетов, настройки веб-сервера NGINX и развертывания приложения Node.js.

Для работы с этим руководством можно использовать последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).


## <a name="cloud-init-overview"></a>Обзор cloud-Init
[Пакет cloud-init](https://cloudinit.readthedocs.io) — широко используемое средство, используемое для настройки виртуальной машины Linux при ее первой загрузке. Вы можете использовать cloud-init для установки пакетов, записи файлов или настройки пользователей и параметров безопасности. Так как cloud-init выполняется при начальной загрузке, для применения вашей конфигурации не требуются какие-либо дополнительные действия или обязательные агенты.

Кроме того, cloud-init работает с разными дистрибутивами. Например, для установки пакета не нужно использовать `apt-get install` или `yum install`. Вместо этого можно определить список устанавливаемых пакетов, и cloud-init автоматически использует собственный инструмент управления пакетами из выбранного дистрибутива.

Мы и наши партнеры работаем над тем, чтобы сценарии cloud-init были добавлены в образы, предоставляемые для Azure. В следующей таблице приведены сведения о текущей доступности cloud-init в образах платформы Azure.

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |последних |
| CoreOS |CoreOS |CoreOS |Stable |последних |


## <a name="create-config-file"></a>Создание файла конфигурации
Чтобы посмотреть, как работает cloud-init, создайте виртуальную машину для установки сервера NGINX и запуска простого приложения Node.js Hello World. Приведенная ниже конфигурация cloud-init устанавливает требуемые пакеты, создает приложение Node.js, а затем инициализирует и запускает это приложение.

Создайте файл с именем `cloud-init.txt` и вставьте следующую конфигурацию:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Чтобы узнать больше о параметрах конфигурации cloud-init, ознакомьтесь с [примерами конфигурации cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Прежде чем создать виртуальную машину, выполните команду [az group create](/cli/azure/group#create), чтобы создать группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroupAutomate` в расположении `westus`:

```azurecli
az group create --name myResourceGroupAutomate --location westus
```

Теперь создайте виртуальную машину командой [az vm create](/cli/azure/vm#create). Используйте параметр `--custom-data`, чтобы передать файл конфигурации cloud-init. Укажите полный путь к конфигурации `cloud-init.txt`, если файл сохранен вне текущего рабочего каталога. В следующем примере создается виртуальная машина с именем `myAutomatedVM`.

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

На создание виртуальной машины, установку пакетов и запуск приложения может потребоваться несколько минут. Когда виртуальная машина будет создана, запишите значение `publicIpAddress`, отображаемое Azure CLI. Это адрес для доступа к приложению Node.js через веб-браузер.

Чтобы разрешить передачу веб-трафика для виртуальной машины, откройте порт 80 для Интернета командой [az vm open-port](/cli/azure/vm#open-port).

```azurecli
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Тестирование веб-приложения
Теперь можно открыть веб-браузер и ввести в адресной строке `http://<publicIpAddress>`. Укажите собственный общедоступный IP-адрес, настроенный при создании виртуальной машины. Отобразится ваше приложение Node.js, как показано ниже.

![Просмотр работающего сайта NGINX](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Внедрение сертификатов из Key Vault
В этом дополнительном разделе описывается, как безопасно хранить сертификаты в Azure Key Vault и внедрять их во время развертывания виртуальных машин. Вместо того, чтобы использовать пользовательский образ со встроенными сертификатами, можно использовать этот процесс, чтобы обеспечить внедрение наиболее актуальных сертификатов в виртуальную машину при ее первом запуске. При этом сертификат никогда не покидает платформу Azure и не предоставляется в сценарии, журнале командной строки или шаблоне.

Azure Key Vault защищает криптографические ключи и секреты, в том числе сертификаты или пароли. Key Vault помогает оптимизировать управление ключами и позволяет поддерживать контроль над ключами, которые предоставляют доступ к вашим данным и шифруют их. В этом сценарии вводятся некоторые понятия Key Vault, относящиеся к созданию и использованию сертификата. Впрочем, это не все общие сведения о том, как использовать Key Vault.

Далее описывается, как:

- создать Azure Key Vault;
- создать или передать сертификат в Key Vault;
- создать секрет из сертификата для внедрения в виртуальную машину;
- создать виртуальную машину и внедрить сертификат.

### <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault
Сначала создайте Key Vault командой [az keyvault create](/cli/azure/keyvault#create) и включите его использование при развертывании виртуальной машины. У каждого Key Vault должно быть уникальное имя в нижнем регистре. Замените `<mykeyvault>` в следующем примере собственным уникальным именем Key Vault.

```azurecli
keyvault_name=<mykeyvault>
az keyvault create --resource-group myResourceGroupAutomate --name $keyvault_name --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Создание сертификата и его сохранение в Key Vault
Для использования в рабочей среде следует импортировать действительный сертификат, подписанный доверенным поставщиком, выполнив команду [az keyvault certificate import](/cli/azure/certificate#import). В следующем примере в этом руководстве показано, как можно командой [az keyvault certificate create](/cli/azure/certificate#create) создать самозаверяющий сертификат, использующий политику сертификата по умолчанию.

```azurecli
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Подготовка сертификата для использования с виртуальной машиной
Чтобы использовать сертификата во время создания виртуальной машины, получите идентификатор сертификата, выполнив команду [az keyvault secret list-versions](/cli/azure/keyvault/secret#list-versions). Преобразуйте сертификат, выполнив команду [az vm format-secret](/cli/azure/vm#format-secret). Следующий пример присваивает переменным результаты этих команд, чтобы их было удобно использовать в дальнейшем.

```azurecli
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Создание конфигурации cloud-init для защиты сервера NGINX
При создании виртуальной машины сертификаты и ключи хранятся в защищенном каталоге `/var/lib/waagent/`. Чтобы автоматизировать добавление сертификата в виртуальную машину и настройку сервера NGINX, можно дополнить конфигурацию cloud-init из предыдущего примера.

Создайте файл с именем `cloud-init-secured.txt` и вставьте следующую конфигурацию:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Создание безопасной виртуальной машины
Теперь создайте виртуальную машину командой [az vm create](/cli/azure/vm#create). Данные сертификата внедряются из Key Vault с помощью параметра `--secrets`. Как и в предыдущем примере, можно передать файл конфигурации cloud-init с помощью параметра `--custom-data`.

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

На создание виртуальной машины, установку пакетов и запуск приложения может потребоваться несколько минут. Когда виртуальная машина будет создана, запишите значение `publicIpAddress`, отображаемое Azure CLI. Это адрес для доступа к приложению Node.js через веб-браузер.

Чтобы разрешить безопасную передачу веб-трафика для виртуальной машины, откройте порт 443 для Интернета командой [az vm open-port](/cli/azure/vm#open-port).

```azurecli
az vm open-port --port 443 --resource-group myResourceGroupAutomate --name myVMSecured
```

### <a name="test-secure-web-app"></a>Тестирование защищенного веб-приложения
Теперь можно открыть веб-браузер и ввести в адресной строке `https://<publicIpAddress>`. Укажите собственный общедоступный IP-адрес, настроенный при создании виртуальной машины. Если используется самозаверяющий сертификат, ответьте согласием на предупреждение системы безопасности.

![Принятие предупреждения о безопасности веб-браузера](./media/tutorial-automate-vm-deployment/browser-warning.png)

На экране отобразятся защищенный сайт NGINX и приложение Node.js, как в показано следующем примере.

![Просмотр работающего защищенного сайта NGINX](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как настроить виртуальную машину при первой загрузке. Перейдите к следующему руководству, чтобы научиться создавать пользовательские образы виртуальных машин.

[Создание образа настраиваемой виртуальной машины](./tutorial-custom-images.md)


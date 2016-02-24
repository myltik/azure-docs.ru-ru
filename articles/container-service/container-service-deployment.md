<properties
   pageTitle="Развертывание кластера службы контейнеров Azure | Microsoft Azure"
   description="Развертывание кластера службы контейнеров Azure с помощью портала Azure, интерфейса командной строки Azure или PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, контейнеры, микрослужбы, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Развертывание кластера службы контейнеров Azure

Служба контейнеров Azure предусматривает быстрое развертывание популярных решений с открытым кодом для кластеризации и оркестрации контейнеров. Служба контейнеров Azure позволяет развернуть кластеры Marathon Mesos и Docker Swarm с помощью шаблонов диспетчера ресурсов Azure или портала. Эти кластеры развертываются с помощью наборов масштабирования виртуальных машин Azure. Они также могут использовать возможности сети и хранилища Azure. Для доступа к службе контейнеров Azure вам понадобится подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

В этом документе описывается развертывание кластера службы контейнеров Azure с помощью [портала Azure](#creating-a-service-using-the-azure-portal), [интерфейса командной строки Azure CLI](#creating-a-service-using-the-azure-cli) и [модуля Azure PowerShell](#creating-a-service-using-powershell).
   
## Создание службы с помощью портала Azure
 
Выберите один из следующих шаблонов для развертывания кластера Mesos или Docker Swarm. **Примечание**. Эти шаблоны отличаются только выбором оркестратора по умолчанию.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Каждый шаблон включает кнопку "Развернуть в Azure". После нажатия этой кнопки будет запущен портал Azure с формой, которая выглядит примерно следующим образом. <br />

![Создать развертывание](media/create-mesos-params.png) <br />

Заполните форму в соответствии с инструкциями, приведенными в этом руководстве, а затем нажмите кнопку "ОК". <br />

Поле | Описание
----------------|-----------
DNSNAMEPREFIX | Это значение должно быть уникальным. Оно будет использоваться для создания DNS-имен для каждого компонента службы. Дополнительные сведения см. ниже.
AGENTCOUNT | Это количество виртуальных машин, которые будут созданы в наборе масштабирования агента ACS.
AGENTVMSIZE | Указывает размер виртуальных машин агента. Будьте внимательны при выборе размера, который обеспечит достаточно ресурсов для размещения самых крупных контейнеров.
ADMINUSERNAME | Это имя пользователя, которое будет использоваться для учетной записи на каждой виртуальной машине, а также в наборах масштабирования виртуальных машин в кластере ACS.
ORCHESTRATORTYPE| Выберите оркестратор, который будет использоваться в кластере ACS.
MASTERCOUNT | Это количество виртуальных машин, которые будут настроены в качестве образцов для кластера. Можно выбрать 1, но такое количество не сможет обеспечить отказоустойчивость кластера (рекомендуется только для тестирования). Рекомендуемое количество для рабочего кластера — 3 или 5. 
SSHRSAPUBLICKEY | Для проверки подлинности виртуальных машин требуется использовать SSH, что предполагает добавление открытого ключа. Вставляйте значение ключа в это поле очень внимательно. Некоторые редакторы добавляют символы разрыва строки в содержимое, что делает ключ недействительным. Проследите, чтобы в строке не было разрывов строки, но были префикс ssh-rsa и постфикс username@domain. Ключ должен выглядеть следующим образом: ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'. Если вам нужно создать ключ SSH, см. рекомендации для [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) и [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) на сайте документации по Azure.
  
Назначив соответствующие значения для параметров, нажмите кнопку "ОК". Теперь укажите имя группы ресурсов, выберите регион, а также просмотрите и примите условия использования.

> За использование службы контейнеров в режиме предварительного просмотра плата не взимается. Вы платите только за стандартные вычислительные операции, включая использование виртуальной машины, хранилища, сети и т. д.
 
![Выбор группы ресурсов](media/resourcegroup.png)
 
Наконец, нажмите кнопку "Создать". Вы вернетесь к панели мониторинга. Если до этого в колонке развертывания вы не сняли флажок "Закрепить на панели мониторинга", вы увидите анимированную плитку:

![развертывание](media/deploy.png)
 
Теперь устройтесь поудобнее и дождитесь завершения создания кластера. По завершении вы увидите несколько колонок с ресурсами, входящими в состав кластера ACS.
 
![Выполнено](media/final.png)

## Создание службы с помощью интерфейса командной строки Azure

Чтобы создать экземпляр службы контейнеров Azure с помощью командной строки, требуется подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы воспользоваться бесплатной пробной версией. Вам также нужно установить и настроить интерфейс командной строки Azure.
 
Выберите один из следующих шаблонов для развертывания кластера Mesos или Docker Swarm. **Примечание**. Эти шаблоны отличаются только выбором оркестратора по умолчанию.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Затем убедитесь, что интерфейс командной строки Azure подключен к подписке Azure. Это можно сделать с помощью следующей команды.

```bash
Azure account show
```
Если эта команда не возвращает учетную запись Azure, войдите в интерфейс командной строки Azure с помощью следующей команды.
 
```bash
azure login -u user@domain.com
```

Затем настройте средства интерфейса командной строки Azure для использования диспетчера ресурсов Azure.
 
```bash
azure config mode arm
```
 
Если вы хотите создать кластер в новой группе ресурсов, такую группу необходимо сначала создать. Используйте следующую команду, где `GROUP_NAME` — имя новой группы ресурсов и `REGION` — регион, в котором эта группа будет создана.
 
```bash
azure group create GROUP_NAME REGION
```
 
После создания группы ресурсов можно приступить к созданию кластера с помощью этой команды, где:

- **RESOURCE\_GROUP** — имя группы ресурсов, которая будет использоваться с этой службой.
- **DEPLOYMENT\_NAME** — имя этого развертывания.
- **TEMPLATE\_URI** — расположение файла развертывания. **Примечание**. Это должен быть НЕОБРАБОТАННЫЙ файл, который не указывает на интерфейс GitHub. Чтобы найти этот URL-адрес, выберите в GitHub файл azuredeploy.json и нажмите кнопку "RAW":

> Примечание. При выполнении этой команды оболочка предложит вам ввести значения параметров развертывания.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Выбор параметров шаблона
 
Эта версия команды предполагает определение параметров в интерактивном режиме. Если вы хотите указать параметры в виде строки в формате JSON, это можно сделать с помощью параметра-переключателя `-p`. Например:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Или вы можете предоставить файл параметров в формате JSON с помощью параметра-переключателя `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Пример файла параметров с именем `azuredeploy.parameters.json` и шаблоны ACS можно найти в GitHub.
 
## Создание службы с помощью PowerShell

Кластер ACS можно также развернуть с помощью PowerShell. В этом документе используется [модуль Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) версии 1.0 и выше.

Выберите один из следующих шаблонов для развертывания кластера Mesos или Docker Swarm. **Примечание**. Эти шаблоны отличаются только выбором оркестратора по умолчанию.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)

Перед созданием кластера в подписке Azure убедитесь, что для сеанса PowerShell выполнен вход в Azure. Это можно сделать с помощью команды `Get-AzureRMSubscription`.

```powershell
Get-AzureRmSubscription
```

Войти в Azure можно с помощью команды `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```
 
Если вы хотите развернуть кластер в новой группе ресурсов, такую группу необходимо сначала создать. Чтобы создать группу ресурсов, используйте команду `New-AzureRmResourceGroup`, указав имя группы ресурсов и регион назначения.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

После создания группы ресурсов можно приступить к созданию кластера с помощью этой команды: Универсальный код ресурса (URI) нужного шаблона будет определен для параметра `-TemplateUri`. При выполнении этой команды PowerShell предложит вам ввести значения параметров развертывания.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Дальнейшие действия
 
Теперь, когда у вас есть работающий кластер, ознакомьтесь со следующими документами, чтобы узнать о возможностях подключения и управления.
 
- [Connect with the ACS cluster](./container-service-connect.md) (Подключение к кластеру ACS).
- [Container management with the REST API](./container-service-mesos-marathon-rest.md) (Управление контейнерами с помощью REST API).

 

<!----HONumber=AcomDC_0218_2016-->
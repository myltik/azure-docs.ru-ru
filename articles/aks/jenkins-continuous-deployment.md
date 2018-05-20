---
title: Непрерывное развертывание Jenkins c помощью Kubernetes в Службе Azure Kubernetes
description: Автоматизация процесса непрерывного развертывания с помощью Jenkins для развертывания и обновления контейнерного приложения на платформе Kubernetes в Службе Azure Kubernetes.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 376d3b916c4e01ea6111e6c1db63e976dd1ea320
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Непрерывное развертывание Jenkins c помощью Kubernetes в Службе Azure Kubernetes

В этом документе показано, как настроить базовый рабочий процесс непрерывного развертывания между Jenkins и кластером Службы Azure Kubernetes (AKS).

Пример рабочего процесса включает в себя следующие шаги.

> [!div class="checklist"]
> * Развертывание приложения для голосования Azure в кластере Kubernetes.
> * Обновление кода приложения для голосования Azure и его отправка в репозиторий GitHub, после чего запускается процесс непрерывного развертывания.
> * Jenkins клонирует репозиторий и создает новый образ контейнера с обновленным кодом.
> * Этот образ отправляется в реестр контейнеров Azure (ACR).
> * Приложение, выполняющееся в кластере AKS, обновляется с помощью нового образа контейнера.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

- Базовое представление о Kubernetes, Git, непрерывной интеграции и непрерывном развертывании, а также о реестре контейнеров Azure (ACR).
- [Кластер Службы Azure Kubernetes (AKS)][aks-quickstart] и [настроенные учетные данные AKS][aks-credentials] в системе разработки.
- [Реестр из реестра контейнеров Azure (ACR)][acr-quickstart], имя сервера входа ACR и [учетные данные ACR][acr-authentication] с доступом для отправки и извлечения.
- Установленный компонент Azure CLI в системе разработки.
- Установленный компонент Docker в системе разработки.
- Учетная запись GitHub, [личный маркер доступа GitHub][git-access-token] и клиент Git, установленный в системе разработки.

## <a name="prepare-application"></a>Подготовка приложения

Приложение для голосования Azure, используемое в этом документе, содержит веб-интерфейс, размещенный в одном или нескольких pod, а во втором pod размещена служба Redis для хранения временных данных.

Перед сборкой интеграции Jenkins и AKS следует подготовить и развернуть приложение для голосования Azure в кластере AKS. Его можно рассматривать как версию 1.

Создайте вилку для следующего репозитория GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

После создания вилки клонируйте его в систему разработки. При клонировании этого репозитория убедитесь, что используется URL-адрес вашей вилки.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Измените каталоги, чтобы использовать клонированный каталог.

```bash
cd azure-voting-app-redis
```

Выполните файл `docker-compose.yaml`, чтобы создать образ контейнера `azure-vote-front` и запустить приложение.

```bash
docker-compose up -d
```

После завершения выполните команду [docker images][docker-images], чтобы просмотреть созданный образ.

Вы увидите, что были скачаны или созданы три образа. Образ `azure-vote-front` содержит приложение и использует образ `nginx-flask` в качестве основы. Образ `redis` используется для запуска экземпляра Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Получите имя сервера входа ACR, выполнив команду [az acr list][az-acr-list]. Обязательно укажите имя группы ресурсов, в которой размещен ваш реестр ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте команду [docker tag][docker-tag], чтобы добавить для образа теги имени сервера входа и номера версии `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Обновите значение сервера входа ACR, указав свой сервер входа ACR, и отправьте образ `azure-vote-front` в реестр.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Развертывание приложения в Kubernetes

Файл манифеста Kubernetes можно найти в корне репозитория приложения для голосования Azure. Этот файл можно использовать для развертывания приложения в кластере Kubernetes.

Сначала обновите файл манифеста **azure-vote-all-in-one-redis.yaml**, указав расположение реестра ACR. Откройте этот файл в любом текстовом редакторе и замените `microsoft` именем сервера входа ACR. Это значение можно найти в строке **47** файла манифеста.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Затем используйте команду [kubectl apply][kubectl-apply], чтобы запустить приложение. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Будет создана [служба Kubernetes][kubernetes-service], которая открывает доступ к приложению через Интернет. Это может занять несколько минут.

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Изначально для параметра *ВНЕШНИЙ IP-АДРЕС* службы *azure-vote-front* отображается состояние *ожидания*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Как только *ВНЕШНИЙ IP-АДРЕС* изменится с состояния *ожидания* на *IP-адрес*, используйте команду `control+c`, чтобы остановить процесс отслеживания kubectl.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Чтобы увидеть приложение, откройте в браузере внешний IP-адрес.

![Схема кластера Kubernetes в Аzure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Развертывание Jenkins на виртуальной машине

Был предварительно создан сценарий для развертывания виртуальной машины, настройки доступа к сети и выполнения базовой установки Jenkins. Кроме того, этот сценарий копирует файл конфигурации Kubernetes из системы разработки в систему Jenkins. Этот файл используется для аутентификации между Jenkins и кластером AKS.

Выполните следующие команды, чтобы скачать и запустить этот сценарий. Ниже приведен URL-адрес, который можно использовать для просмотра содержимого сценария.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

После завершения выполнения сценарий выводит адрес сервера Jenkins, а также ключ для разблокирования Jenkins. Перейдите по URL-адресу, введите ключ и следуйте инструкциям на экране, чтобы завершить настройку Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Переменные среды Jenkins

Переменная среды Jenkins используется для хранения имени сервера входа реестра контейнеров Azure (ACR). Эта переменная указывается во время выполнения задания непрерывного развертывания Jenkins.

На портале администрирования Jenkins щелкните **Manage Jenkins** (Управление Jenkins)  >  **Configure System** (Настройка системы).

В разделе **Global Properties** (Глобальные свойства) выберите **Environment variables** (Переменные среды) и добавьте переменную `ACR_LOGINSERVER` с именем сервера входа ACR в качестве значения.

![Переменные среды Jenkins](media/aks-jenkins/env-variables.png)

По завершении нажмите кнопку **Save** (Сохранить) на странице конфигурации Jenkins.

## <a name="jenkins-credentials"></a>Учетные данные Jenkins

Теперь сохраните учетные данные ACR в объекте учетных данных Jenkins. Эти учетные данные указываются во время выполнения задания сборки Jenkins.

Вернитесь на портал администрирования Jenkins и выберите **Credentials** (Учетные данные)  >  **Jenkins**  >  **Global credentials (unrestricted)** (Глобальные учетные данные (без ограничений))  >  **Add Credentials** (Добавить учетные данные).

Убедитесь, что выбран тип учетных данных **Username with password** (Имя пользователя с паролем), и введите следующие данные.

- **Username** (Имя пользователя) — идентификатор субъекта-службы для аутентификации в реестре ACR.
- **Password** (Пароль) — секрет клиента субъекта-службы для аутентификации в реестре ACR.
- **ID** (Идентификатор) — идентификатор учетных данных, например `acr-credentials`.

После заполнения форма учетных данных должна выглядеть примерно как на рисунке ниже.

![Учетные данные ACR](media/aks-jenkins/acr-credentials.png)

Нажмите кнопку **ОК** и вернитесь на портал администрирования Jenkins.

## <a name="create-jenkins-project"></a>Создание проекта Jenkins

На портале администрирования Jenkins щелкните **New Item** (Создать элемент).

Введите имя проекта, например `azure-vote`, выберите **Freestyle Project** (Универсальный проект) и нажмите кнопку **ОК**.

![Проект Jenkins](media/aks-jenkins/jenkins-project.png)

В разделе **General** (Общие) выберите **GitHub project** (Проект GitHub) и введите URL-адрес вилки проекта GitHub приложения для голосования Azure.

![Проект GitHub](media/aks-jenkins/github-project.png)

В разделе **Source Code Management** (Управление исходным кодом) выберите **Git** и введите URL-адрес вилки репозитория GitHub приложения для голосования Azure.

Для добавления учетных данных выберите **Add** (Добавить)  >  **Jenkins**. В разделе **Kind** (Вид) выберите **Secret text** (Секретный текст) и введите [личный маркер доступа GitHub][git-access-token] в качестве секрета.

Щелкните **Add** (Добавить) после завершения.

![Учетные данные GitHub](media/aks-jenkins/github-creds.png)

В разделе **Build Triggers** (Триггеры сборки) выберите **GitHub hook trigger for GITScm polling** (Обработчик триггера Github для опроса GITScm).

![Триггеры сборки Jenkins](media/aks-jenkins/build-triggers.png)

В разделе **Build Environment** (Среда сборки) выберите **Use secret texts or files** (Использовать секретные тексты или файлы).

![Среда сборки Jenkins](media/aks-jenkins/build-environment.png)

В разделе **Bindings** (Привязки) выберите **Add** (Добавить)  >  **Username and password (separated)** (Имя пользователя и пароль (отдельно)).

Введите `ACR_ID` в поле **Username Variable** (Переменная имени пользователя) и `ACR_PASSWORD` — в поле **Password Variable** (Переменная пароля).

![Привязки Jenkins](media/aks-jenkins/bindings.png)

Добавьте **шаг сборки** типа **Execute shell** (Запустить оболочку) и используйте следующий текст. Этот сценарий создает новый образ контейнера и отправляет его в реестр ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Добавьте другой **шаг сборки** типа **Execute shell** (Запустить оболочку) и используйте следующий текст. Этот сценарий обновляет развертывание Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

После завершения щелкните **Save** (Сохранить).

## <a name="test-the-jenkins-build"></a>Тестирование сборки Jenkins

Прежде чем продолжить, проверьте сборку Jenkins. Будет проверено, правильно ли настроено задание сборки, имеется ли соответствующий файл аутентификации Kubernetes и предоставлены ли соответствующие учетные данные ACR.

Щелкните **Build Now** (Выполнить сборку) в левом меню проекта.

![Тестовая сборка Jenkins](media/aks-jenkins/test-build.png)

Во время этого процесса репозиторий GitHub клонируется на сервер сборки Jenkins. Создается новый образ контейнера, который отправляется в реестр ACR. Наконец, приложение для голосования Azure в кластере AKS обновляется для использования нового образа. Так как в код приложения не были внесены изменения, приложение не изменится.

После завершения процесса щелкните **build #1** (Сборка № 1) в журнале сборки и выберите **Console Output** (Вывод в консоль), чтобы просмотреть все выходные данные процесса сборки. Последняя строка должна указывать, что сборка выполнена успешно.

## <a name="create-github-webhook"></a>Создание объекта webhook GitHub

Далее подключите обработчик репозитория приложений к серверу сборки Jenkins, чтобы при любой фиксации инициировалась новая сборка.

1. Перейдите к репозиторию GitHub, для которого вы создали вилку.
2. Щелкните **Settings** (Параметры), а затем выберите **Integrations & services** (Интеграция и службы) слева.
3. Выберите **Add Service** (Добавить службу), введите `Jenkins (GitHub plugin)` в поле фильтра и выберите подключаемый модуль.
4. В качестве URL-адреса обработчика Jenkins введите `http://<publicIp:8080>/github-webhook/`, где `publicIp` — IP-адрес сервера Jenkins. Убедитесь, что адрес содержит завершающую косую черту (/).
5. Выберите "Add service" (Добавить службу).

![Объект webhook GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Тестирование полного цикла процесса непрерывной интеграции и непрерывного развертывания

На компьютере разработки откройте клонированное приложении с помощью редактора кода.

В каталоге **/azure-vote/azure-vote** найдите файл **config_file.cfg**. Обновите значения для голосования в этом файле, указав что-нибудь иное вместо кошек и собак.

В следующем примере показан и обновлен файл **config_file.cfg**.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

После завершения сохраните этот файл, зафиксируйте изменения и отправьте их в вилку репозитория GitHub. После завершения фиксации веб-перехватчик GitHub активирует новую сборку Jenkins, которая обновит образ контейнера и развертывание AKS. Наблюдайте за процессом сборки в консоли администрирования Jenkins.

После завершения сборки снова перейдите к конечной точке приложения, чтобы увидеть изменения.

![Обновленное приложение для голосования Azure](media/aks-jenkins/azure-vote-updated-safari.png)

На данном этапе выполнен простой процесс непрерывного развертывания. Действия и конфигурации, показанные в этом примере, можно использовать для обеспечения более надежной и подходящей для рабочей среды автоматизации непрерывного выполнения сборки.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
---
title: Выполнение задач, объединенных в контейнеры, в службе "Экземпляры контейнеров Azure"
description: Узнайте, как использовать Экземпляры контейнеров Azure для задач, выполняемых до завершения, включая сборку, тестирование или преобразование изображений.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3bbe3e891423b6ad62a1d1093daef304206f3d76
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167135"
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"

Простота и скорость развертывания контейнеров в службе "Экземпляры контейнеров Azure" позволяет выполнять в экземпляре контейнера такие разовые задачи, как сборка, тестирование или преобразование изображений.

Настраиваемая политика перезапуска позволяет указать, что контейнер нужно остановить после завершения всех его процессов. Так как работа экземпляров контейнеров оплачивается посекундно, в счет включаются только те вычислительные ресурсы, которые использовались во время выполнения контейнера с задачей.

В этой статье представлены примеры, в которых используется Azure CLI. Для их выполнения потребуется [локально установленная][azure-cli-install] версия Azure CLI 2.0.21 или выше. Кроме того, можно использовать CLI в [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Политика перезапуска контейнера

При создании контейнера в экземплярах контейнера Azure вы можете выбрать один из трех режимов политики перезапуска.

| Политика перезапуска   | ОПИСАНИЕ |
| ---------------- | :---------- |
| `Always` | Контейнеры в группе контейнеров всегда перезапускаются. Эта политика применяется **по умолчанию**, если при создании контейнера не указать другую политику перезапуска. |
| `Never` | Контейнеры в группе контейнеров никогда не перезапускаются. Такие контейнеры будут выполнены не более одного раза. |
| `OnFailure` | Контейнеры в группе контейнеров перезапускаются, только если процесс в контейнере завершается ошибкой (то есть с ненулевым кодом выхода). Такие контейнеры будут выполнены не менее одного раза. |

## <a name="specify-a-restart-policy"></a>Указание политики перезапуска

Способ определения политики перезапуска зависит от способа создания экземпляра контейнера: с помощью Azure CLI, командлетов PowerShell Azure или портала Azure. В Azure CLI следует указать параметр `--restart-policy` при вызове команды [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Пример выполнения до завершения

Чтобы увидеть применение политики перезагрузки, создайте экземпляр контейнера из образа [aci/microsoft-wordcount][aci-wordcount-image] и укажите для него политику перезапуска `OnFailure`. Контейнер из этого примера запускает скрипт Python, который по умолчанию анализирует текст пьесы [Гамлет](http://shakespeare.mit.edu/hamlet/full.html) Уильяма Шекспира, выдает в STDOUT 10 самых употребимых слов в тексте, и завершает работу.

Запустите этот контейнер с помощью команды [az container create][az-container-create], как показано ниже.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Служба "Экземпляры контейнеров Azure" запускает контейнер, а затем останавливает его, когда завершится работа его приложения (или скрипта, как в нашем примере). Когда служба "Экземпляры контейнеров Azure" останавливает контейнер, для которого указана политика перезапуска `Never` или `OnFailure`, для состояния контейнера устанавливается значение **Terminated** (Завершено). Текущее состояние контейнера вы можете проверить с помощью команды [az container show][az-container-show], как показано ниже.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Выходные данные примера:

```bash
"Terminated"
```

Когда состояние контейнера примет значение *Terminated* (Завершено), вы сможете просмотреть выходные данные выполненной в нем задачи, используя журналы контейнера. Для просмотра выходных данных скрипта выполните команду [az container logs][az-container-logs], как показано ниже.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Выходные данные:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

В нашем примере вы увидите выходные данные, отправленные скриптом в STDOUT. Но задачи, заключенные в контейнер, могут записывать выходные данные в постоянное хранилище, чтобы их можно было использовать позднее. Например, их можно отправить в [файловый ресурс Azure](container-instances-mounting-azure-files-volume.md)

## <a name="configure-containers-at-runtime"></a>Настройка контейнеров во время выполнения

Когда вы создаете экземпляр контейнера, для него можно задать **переменные среды**, а также пользовательскую **командную строку** для выполнения при запуске контейнера. Эти параметры позволяют выполнять пакетные задания, создавая для каждого контейнера отдельную конфигурацию в соответствии с конкретной задачей.

## <a name="environment-variables"></a>Переменные среды

Используйте переменные среды в контейнере, чтобы динамически изменять конфигурацию выполняемого в нем приложения или скрипта. Эта функция работает так же, как и аргумент `--env` в команде `docker run`.

Например, в нашем примере вы можете изменить поведение скрипта, указав следующие переменные среды при создании экземпляра контейнера.

*NumWords* задает число слов для вывода в STDOUT.

*MinLength* обозначает минимальную длину учитываемых слов в символах. Если увеличить это значение, скрипт будет пропускать самые распространенные слова, например of и the.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Если вы определите для контейнера переменные среды `NumWords=5` и `MinLength=8`, выходные данные в журнале контейнера изменятся. Когда статус контейнера примет значение *Terminated* (Завершено), что можно проверить с помощью команды `az container show`, просмотрите его журналы для оценки новых выходных данных.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Выходные данные:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Переопределение командной строки

Если при создании экземпляра контейнера вы укажете командную строку, она переопределит командную строку, сохраненную в образе контейнера. Эта функция работает так же, как и аргумент `--entrypoint` в команде `docker run`.

Например, в контейнер из нашего примера вы можете передать другой текст для анализа, а не пьесу *Гамлет*, указав новую командную строку. Скрипт Python *wordcount.py*, который выполняется в этом контейнере, принимает в качестве аргумента URL-адрес текста, который нужно обработать вместо стандартного.

Например, так вы можете найти 3 самых употребляемых слова из пяти букв в тексте пьесы *Ромео и Джульетта*.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Как и прежде, вы сможете просмотреть выходные данные с помощью журналов контейнера, когда его состояние изменится на *Terminated* (Завершено), как показано ниже.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Выходные данные:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Дополнительная информация

### <a name="persist-task-output"></a>Сохранение выходных данных задачи

Дополнительные сведения о том, как сохранить выходные данные контейнеров, которые выполняются до завершения, вы найдете в статье [Подключение файлового ресурса Azure с помощью Экземпляров контейнеров Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli

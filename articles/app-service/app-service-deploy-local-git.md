---
title: Развертывание локального репозитория Git в службе приложений Azure
description: Узнайте, как включить локальное развертывание репозитория Git в службе приложений Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 842cd6f67a04bec0ed06282bdeeea8b8a51c0667
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Развертывание локального репозитория Git в службе приложений Azure

В этом руководстве показано, как развернуть в [службе приложений Azure](app-service-web-overview.md) свой код из репозитория Git на локальном компьютере.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Выполните следующие шаги для изучения данного руководства.

* [Установка Git](http://www.git-scm.com/downloads).
* Обеспечьте локальный репозиторий Git с кодом, который вы хотите развернуть.

Чтобы в дальнейшем использовать пример репозитория, выполните следующую команду в локальном окне терминала.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Подготовка репозитория

Убедитесь, что корень репозитория содержит нужные файлы проекта.

| Среда выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _SLN_-файлы, _CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core; | _SLN_-файлы или _CSPROJ_-файлы |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ или _package.json_ со сценарием запуска |
| Python (только для Windows) | _\*PY_-файлы, _requirements.txt_ или _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<имя_задания>/run.\<extension>_ в папке _App\_Data/jobs/continuous_ (для непрерывных веб-заданий) или в папке _App\_Data/jobs/triggered_ (для активируемых веб-заданий). Дополнительные сведения см. в [документации по веб-заданиям Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Чтобы настроить развертывание, можно добавить в корень репозитория _DEPLOYMENT_-файл. Дополнительные сведения см. в статьях [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Настройка развертываний) и [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Настраиваемый сценарий развертывания).

> [!NOTE]
> Не забудьте выполнить команду `git commit` для фиксации всех изменений, которые вы хотите развернуть.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Включение использования репозитория Git для приложения

Чтобы обеспечить развертывание Git для существующего приложения службы приложений, выполните команду [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) в Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Чтобы вместо этого создать приложение с поддержкой Git, выполните команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) в Cloud Shell с параметром `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Результат команды `az webapp create` должен выглядеть примерно так:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="deploy-your-project"></a>Развертывание проекта

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ URL-адресом удаленного репозитория Git, полученным на шаге [Включение использования репозитория Git для приложения](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При появлении запроса введите пароль, созданный в разделе на шаге [настройки пользователя развертывания](#configure-a-deployment-user) (а не используемый для входа на портал Azure).

```bash
git push azure master
```

В выходных данных могут отображаться автоматизированные операции времени выполнения, например MSBuild для ASP.NET, `npm install` для Node.js и `pip install` для Python. 

После завершения развертывания приложения на портале Azure должна быть запись вашей операции `git push` на странице **Варианты развертывания**.

![](./media/app-service-deploy-local-git/deployment_history.png)

Перейдите к своему приложению, чтобы убедиться, что содержимое развернуто.

## <a name="troubleshooting"></a>Устранение неполадок

Ниже перечислены распространенные ошибки или проблемы, возникающие при использовании Git для публикации в приложение службы приложений в Azure.

---
**Симптом**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Причина**: эта ошибка может возникнуть, если приложение не работает.

**Устранение**: запустите приложение на портале Azure. Если веб-приложение остановлено развертывание Git недоступно.

---
**Симптом**: `Couldn't resolve host 'hostname'`

**Причина**: эта ошибка может возникнуть, если при создании удаленного репозитория azure был введен неправильный адрес.

**Решение.** Используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.

---
**Симптом**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Причина**: эта ошибка может возникнуть, если при использовании команды `git push` не указана ветвь или если не задано значение `push.default` в `.gitconfig`.

**Решение**: выполните команду `git push` еще раз, указав в главную ветвь. Например: 

```bash
git push azure master
```

---
**Симптом**: `src refspec [branchname] does not match any.`

**Причина**: эта ошибка может возникнуть при попытке принудительно отправить данные в ветвь, отличную от главной, в удаленном репозитории azure.

**Решение**: выполните команду `git push` еще раз, указав в главную ветвь. Например: 

```bash
git push azure master
```

---
**Симптом**: `RPC failed; result=22, HTTP code = 5xx.`

**Причина**: эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.

**Решение**. Измените конфигурацию Git на локальном компьютере, чтобы увеличить значение postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Симптом**: `Error - Changes committed to remote repository but your web app not updated.`

**Причина**: эта ошибка может возникнуть при развертывании приложения Node.js, содержащего файл _package.json_, в котором указаны дополнительные необходимые модули.

**Решение**: до этой ошибки должны быть зарегистрированы дополнительные сообщения, содержащие "npm ERR!", и они могут предоставлять дополнительный контекст для данного сбоя. Ниже перечислены известные причины этой ошибки и соответствующее сообщение npm ERR!. сообщение:

* **Malformed package.json file**: npm ERR! Couldn't read dependencies (не удалось прочитать зависимости).
* **Собственный модуль, не имеющий двоичного дистрибутива для Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Или
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
* [Непрерывное развертывание в службе приложений Azure](app-service-continuous-deployment.md)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)

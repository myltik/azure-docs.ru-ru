---
title: Использование кластера пакета HPC с Azure Active Directory | Документация Майкрософт
description: Узнайте, как интегрировать кластер пакета Microsoft HPC 2016 в Azure с Azure Active Directory
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
ms.locfileid: "25452578"
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Управление кластером пакета HPC в Azure с помощью Azure Active Directory
[Пакет Microsoft HPC 2016](https://technet.microsoft.com/library/cc514029) поддерживает интеграцию с [Azure Active Directory](../../active-directory/index.md) (Azure AD) для администраторов, развертывающих кластер пакета HPC в Azure.



Выполните действия, описанные в этой статье, для выполнения следующих задач верхнего уровня: 
* Интеграция кластера пакета HPC с клиентом Azure AD.
* Планирование заданий кластера HPC в Azure и управление ими. 

Интеграция решения кластера пакета HPC с Azure AD выполняется стандартно и включает в себя действия по интеграции с другими приложениями и службами. В этой статье предполагается, что вы знакомы с основами пользовательского управления в Azure AD. Дополнительные сведения см. в [документации Azure Active Directory](../../active-directory/index.md) и в разделе ниже.

## <a name="benefits-of-integration"></a>Преимущества интеграции


Azure Active Directory (Azure AD) — многопользовательский облачный каталог и служба управления удостоверениями, предоставляющая возможность единого входа в облачные решения.

Интеграция кластера пакета HPC с Azure AD позволяет достичь следующих целей:

* Удалить традиционный контроллер домена Active Directory из кластера пакета HPC. Таким образом вы сможете снизить издержки на обслуживание кластера, если это необязательно для вашего бизнеса, а также ускорить процесс развертывания.
* Использовать следующие преимущества, предоставляемые Azure AD:
    *   Единый вход 
    *   Использование локального удостоверения AD для кластера пакета HPC в Azure. 

    ![Среда Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>предварительным требованиям
* **Кластер пакета HPC 2016, развернутый на виртуальных машинах Azure.** Дополнительные сведения см. в статье [Deploy an HPC Pack 2016 cluster in Azure](hpcpack-2016-cluster.md) (Развертывание кластера пакета HPC 2016 в Azure). Для выполнения инструкций в этой статье требуется DNS-имя головного узла, а также учетные данные администратора кластера.

  > [!NOTE]
  > Интеграция Azure Active Directory не поддерживается в версиях пакета HPC до версии HPC 2016.



* **Клиентский компьютер**. Требуется клиентский компьютер под управлением Windows или Windows Server для запуска клиентских служебных программ для пакета HPC. Если вы не планируете выполнять задачи помимо отправки задач через веб-портал или REST API для пакета HPC, то можете использовать любой клиентский компьютер.

* **Клиентские служебные программы для пакета HPC**. Установите клиентские служебные программы для пакета HPC на клиентском компьютере, используя бесплатный установочный пакет, доступный в Центре загрузки Майкрософт.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Шаг 1. Регистрация сервера кластера HPC с помощью клиента Azure AD
1. Войдите на [портале Azure](https://portal.azure.com).
2. Если учетная запись предоставляет доступ к нескольким клиентам Azure AD, щелкните учетную запись в правом верхнем углу. Затем выберите нужный клиент для этого сеанса работы с порталом. Вам нужно разрешение на доступ к ресурсам в каталоге. 
3. На панели навигации служб слева выберите **Azure Active Directory**, щелкните **Пользователи и группы** и убедитесь, что учетные записи пользователей уже созданы или настроены.
4. В разделе **Azure Active Directory** последовательно выберите **Регистрация приложений** > **Регистрация нового приложения**. Введите следующие сведения:
    * **Имя** — HPCPackClusterServer.
    * **Тип приложения** — выберите **Веб-приложение или API**.
    * **URL-адрес входа** — базовый URL-адрес примера, который по умолчанию имеет значение `https://hpcserver`.
    * Нажмите кнопку **Создать**.
5. Добавив приложение, выберите его в списке **Регистрация приложений**. Затем выберите **Параметры** > **Свойства**. Введите следующие сведения:
    * Для параметра **Мультитенантный** установите значение **Да**.
    * Замените **URI кода приложения** значением `https://<Directory_name>/<application_name>`. Замените `<Directory_name` полным именем вашего клиента Azure AD, например `hpclocal.onmicrosoft.com`, а `<application_name>` — именем, выбранным ранее.
6. Выберите команду **Сохранить**. Когда сохранение завершится, на странице приложения щелкните **Манифест**. Измените манифест. Для этого найдите параметр `appRoles`, добавьте приведенную ниже роль приложения и нажмите кнопку **Сохранить**.

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. В разделе **Azure Active Directory** выберите **Корпоративные приложения** > **Все приложения**. Выберите **HPCPackClusterServer** в списке.
8. Щелкните **Свойства** и измените значение параметра **Требуется назначение пользователей** на **Да**. Выберите команду **Сохранить**.
9. Последовательно выберите **Пользователи и группы** > **Добавить пользователя**. Выберите пользователя и роль, а затем нажмите кнопку **Назначить**. Назначьте пользователю одну из доступных ролей (HpcUsers или HpcAdminMirror). Повторите этот шаг для дополнительных пользователей в каталоге. Общие сведения о пользователях кластера см. в статье [Managing Cluster Users](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx) (Управление пользователями кластера).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Шаг 2. Регистрация клиента кластера HPC с помощью клиента Azure AD

1. Войдите на [портале Azure](https://portal.azure.com).
2. Если учетная запись предоставляет доступ к нескольким клиентам Azure AD, щелкните учетную запись в правом верхнем углу. Затем выберите нужный клиент для этого сеанса работы с порталом. Вам нужно разрешение на доступ к ресурсам в каталоге. 
3. В разделе **Azure Active Directory** последовательно выберите **Регистрация приложений** > **Регистрация нового приложения**. Введите следующие сведения:

    * **Имя** — HPCPackClusterClient.    
    * **Тип приложения** — выберите **Машинный код**.
    * **URI перенаправления** - `http://hpcclient`.
    * Нажмите кнопку **Создать**.

4. Добавив приложение, выберите его в списке **Регистрация приложений**. Скопируйте значение **идентификатора приложения** и сохраните его. Оно понадобится позже при настройке приложения.

5. Последовательно выберите **Параметры** > **Необходимые разрешения** > **Добавить** > **Выбор API**. Найдите и выберите приложение HpcPackClusterServer (созданное на шаге 1).

6. На странице **Разрешить доступ** выберите **Access HpcClusterServer** (Доступ к HpcClusterServer). Затем нажмите кнопку **Done**(Готово).


## <a name="step-3-configure-the-hpc-cluster"></a>Шаг 3. Настройка кластера HPC

1. Подключитесь к головному узлу пакета HPC 2016 в Azure.

2. Запустите HPC PowerShell.

3. Выполните следующую команду:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    где:

    * `AADTenant` указывает имя клиента Azure AD, например `hpclocal.onmicrosoft.com`.
    * `AADClientAppId` указывает идентификатор для приложения, созданного на шаге 2.

4. Выполните одно из следующих действий в зависимости от конфигурации головного узла:

    * В кластере пакета HPC с одним головным узлом перезапустите службу HpcScheduler.

    * В кластере пакета HPC с несколькими головными узлами выполните следующие команды PowerShell на головном узле для перезапуска службы HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Шаг 4. Отправка заданий из клиента и управление ими

Чтобы установить клиентские служебные программы для пакета HPC на свой компьютер, скачайте файлы установки пакета HPC 2016 (полная установка) из Центра загрузки Майкрософт. При запуске инсталляции выберите вариант установки **HPC Pack client utilities** (Клиентские служебные программы для пакета HPC).

Чтобы подготовить клиентский компьютер, установите на нем сертификат, используемый во время [установки кластера HPC](hpcpack-2016-cluster.md). Используйте стандартные процедуры управления сертификатами Windows для установки открытого сертификата в хранилище **Certificates – Current user**(Сертификаты — текущий пользователь) > **Доверенные корневые центры сертификации**. 

Теперь вы можете выполнять команды пакета HPC или использовать графический интерфейс пользователя диспетчера заданий пакета HPC для отправки заданий и управления ими с помощью учетной записи Azure AD. Варианты отправки заданий см. в статье [Отправка заданий HPC c локального компьютера в кластер на основе пакета HPC, развернутый в Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> При попытке подключиться к кластеру пакета HPC Pack в Azure впервые появится всплывающее окно. Введите учетные данные Azure AD для входа. Затем кэшируется маркер. При последующих попытках подключения к кластеру в Azure используется кэшированный маркер, если не произошли изменения аутентификации или кэш не очищен.
>
  
Например, выполнив предыдущие шаги, можно запросить задания из локального клиента следующим образом:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Полезные командлеты для отправки заданий с интеграцией Azure AD 

### <a name="manage-the-local-token-cache"></a>Управление локальным кэшем маркеров

В пакете HPC 2016 доступны приведенные ниже командлеты HPC PowerShell для управления локальным кэшем маркеров. Их можно использовать для отправки заданий в неинтерактивном режиме. Как в этом примере:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Установка учетных данных для отправки заданий с использованием учетной записи Azure AD 

В некоторых случаях может потребоваться выполнить задание от имени пользователя кластера HPC (для присоединенного к домену кластера HPC выполните от имени одного пользователя домена; для неприсоединенного к домену кластера HPC выполните от имени одного локального пользователя на головном узле).

1. Выполните следующие команды, чтобы задать учетные данные:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Затем отправьте задание следующим образом. Задание или задача выполняется в разделе $localUser на вычислительных узлах.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Если параметр `–Credential` не указан в `Submit-HpcJob`, задание или задача выполняется от имени локального сопоставленного пользователя, действующего в качестве учетной записи Azure AD. Для выполнения задачи кластер HPC создает локального пользователя с тем же именем, что и у учетной записи Azure AD.
    
3. Укажите подробные сведения для учетной записи Azure AD. Это полезно при выполнении задания MPI на узлах Linux с использованием учетной записи Azure AD.

   * Укажите подробные сведения для самой учетной записи Azure AD.

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Укажите подробные сведения и выполните задание от имени пользователя кластера HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```


---
title: "Создание первого главного экземпляра Jenkins на виртуальной машине под управлением Linux (Ubuntu) в Azure"
description: "Используйте шаблон решения для развертывания Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 748f1994d0ee5b6c6a988bca8e5b694b29c0b8c5
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017

---

<a id="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure" class="xliff"></a>

# Создание первого главного экземпляра Jenkins на виртуальной машине под управлением Linux (Ubuntu) в Azure

Это краткое руководство посвящено установке последней стабильной версии Jenkins на виртуальной машине под управлением Linux (Ubuntu 14.04 LTS), а также средств и подключаемых модулей, настроенных для работы с Azure. Вот эти средства:
<ul>
<li>Git для системы управления версиями;</li>
<li>подключаемый модуль учетных данных Azure для безопасного подключения;</li>
<li>подключаемый модуль Azure с агентами виртуальной машины для эластичной сборки, тестирования и непрерывной интеграции;</li>
<li>подключаемый модуль службы хранилища Azure для хранения артефактов;</li>
<li>Azure CLI для развертывания приложений с помощью скриптов.</li>
</ul>

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * создать бесплатную учетную запись Azure;
> * создать главный экземпляр Jenkins на виртуальной машине Azure с помощью шаблона решения; 
> * выполнить начальную настройку для Jenkins;
> * установить предлагаемые подключаемые модули.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

<a id="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins" class="xliff"></a>

## Создание виртуальной машины в Azure на основе шаблона решения для Jenkins

Шаблоны, представленные в кратком руководстве Azure, позволяют быстро и надежно развертывать комплексные технологии в Azure.  Azure Resource Manager позволяет подготавливать приложения с помощью [декларативного шаблона](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins). В одном шаблоне можно развернуть несколько служб и их зависимые компоненты. Один шаблон используется для развертывания приложения на каждом этапе его жизненного цикла.

Просмотрите сведения о [планах и ценах](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice) для этого шаблона, чтобы узнать стоимость различных вариантов.

Перейдите на страницу с [образом Marketplace для Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) и нажмите кнопку **ПОЛУЧИТЬ**  

На портале Azure нажмите кнопку **Создать**.  Для этого шаблона необходимо использовать Resource Manager, поэтому раскрывающийся список моделей шаблонов отключен.
   
![Диалоговое окно на портале Azure](./media/install-jenkins-solution-template/ap-create.png)

На вкладке **Настройка основных параметров** выполните приведенные ниже инструкции.

![Настройка основных параметров.](./media/install-jenkins-solution-template/ap-basic.png)

* Введите имя экземпляра Jenkins.
* Выберите тип диска виртуальной машины.  Для повышения производительности при рабочих нагрузках выберите виртуальную машину и диск SSD большего размера.  С дополнительными сведениями о типах дисков Azure можно ознакомиться [здесь.](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)
* Имя пользователя: должно соответствовать требованиям к длине и не должно содержать зарезервированные слова или неподдерживаемые символы. Использование таких имен, как admin, не допускается.  Дополнительные сведения о требованиях к имени пользователя и паролю см. [здесь](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq).
* Тип проверки подлинности: создайте экземпляр, защищенный паролем или [открытым ключом SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Если используется пароль, он должен содержать по меньшей мере три из следующих символов: одна строчная буква, одна прописная буква, одна цифра и один специальный символ.
* Оставьте тип выпуска Jenkins **LTS**.
* Выберите подписку.
* Создайте новую группу ресурсов или выберите существующую.
* Выберите расположение.

На вкладке **Configure additional options** (Настройка дополнительных параметров) выполните приведенные ниже инструкции.

![Настройка дополнительных параметров](./media/install-jenkins-solution-template/ap-addtional.png)

* Укажите метку доменного имени для уникальной идентификации главного экземпляра Jenkins.

Нажмите кнопку **ОК** для перехода к следующему шагу. 

После прохождения проверки нажмите кнопку **ОК**, чтобы скачать шаблон и параметры. 

Затем выберите **Купить** для подготовки всех ресурсов.

<a id="setup-ssh-port-forwarding" class="xliff"></a>

## Настройка перенаправления портов SSH

По умолчанию для экземпляра Jenkins используется протокол HTTP и ожидается передача данных через порт 8080. Пользователи не должны проходить проверку подлинности по незащищенным протоколам.
    
Настройте перенаправление портов для просмотра пользовательского интерфейса Jenkins на локальном компьютере.

<a id="if-you-are-using-windows" class="xliff"></a>

### При использовании Windows сделайте следующее.

Если для защиты Jenkins используется пароль, установите PuTTY и выполните следующую команду:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Введите пароль для входа в систему.

![Введите пароль для входа в систему.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Если используется SSH, выполните следующую команду:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="if-you-are-using-linux-or-mac" class="xliff"></a>

### При использовании Mac или Linux сделайте следующее.

Если для защиты главного экземпляра Jenkins используется пароль, выполните следующую команду:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Введите пароль для входа в систему.

Если используется SSH, выполните следующую команду:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="connect-to-jenkins" class="xliff"></a>

## Подключение к Jenkins
После запуска туннеля перейдите по адресу http://localhost:8080/ на локальном компьютере.

При первом использовании разблокируйте панель мониторинга Jenkins с помощью первоначального пароля администратора.

![Разблокировка Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Для получения маркера установите SSH-подключение к виртуальной машине и запустите `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Разблокировка Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Отобразится запрос на установку подключаемых модулей.

Создайте роль администратора для главного экземпляра Jenkins.

Теперь экземпляр Jenkins готов к использованию. С представлением только для чтения можно ознакомиться, перейдя по адресу http://\<общедоступное DNS-имя только что созданного экземпляра\>.

![Экземпляр Jenkins готов!](./media/install-jenkins-solution-template/jenkins-welcome.png)

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Изучив это руководство, вы:

> [!div class="checklist"]
> * создали главный экземпляр Jenkins с использованием шаблона решения;
> * выполнили начальную настройку Jenkins;
> * установили подключаемые модули.

Перейдите по ссылке ниже, чтобы узнать, как использовать агенты виртуальной машины для непрерывной интеграции с Jenkins.

> [!div class="nextstepaction"]
> [Виртуальные машины Azure как агенты Jenkins](jenkins-azure-vm-agents.md)


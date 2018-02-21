---
title: "Создание сервера Jenkins в Azure"
description: "Установите Jenkins на виртуальной машине Azure под управлением Linux на основе шаблона решения Jenkins и создайте образец приложения Java."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 422d133841a380b1ef02e95245207c464089138d
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Создание сервера Jenkins на виртуальной машине Azure под управлением Linux на портале Azure

В этом кратком руководстве описано, как установить [Jenkins](https://jenkins.io) на виртуальной машине под управлением Ubuntu Linux с помощью средств и подключаемых модулей, настроенных для работы с Azure. Мы создадим в Azure сервер Jenkins для сборки образца приложения Java из [GitHub](https://github.com).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure
* Доступ к SSH в командной строке компьютера (например, в оболочке Bash или [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Создание виртуальной машины Jenkins на основе шаблона решения

Откройте [образ marketplace для Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) в веб-браузере и нажмите кнопку **ПОЛУЧИТЬ СЕЙЧАС** в левой части страницы. Просмотрите сведения о ценах и выберите **Продолжить**, а затем нажмите **Создание**, чтобы настроить сервер Jenkins на портале Azure. 
   
![Диалоговое окно на портале Azure](./media/install-jenkins-solution-template/ap-create.png)

На вкладке **Настройка основных параметров** заполните следующие поля:

![Настройка основных параметров.](./media/install-jenkins-solution-template/ap-basic.png)

* Установите значение **Jenkins** для параметра **Имя**.
* Введите данные в поле **Имя пользователя**. Имя пользователя должно соответствовать [особым требованиям](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Установите значение **Пароль** для параметра **Тип проверки подлинности** и введите пароль. Пароль должен содержать прописную букву, цифру и один специальный символ.
* Установите значение **myJenkinsResourceGroup** для параметра **Группа ресурсов**.
* Выберите значение **Восток США** для параметра [Регион Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) из раскрывающегося списка **Расположение**.

Нажмите **ОК**, чтобы перейти к вкладке **Настройка дополнительных параметров**. Введите уникальное доменное имя для идентификации сервера Jenkins и нажмите **ОК**.

![Настройка дополнительных параметров](./media/install-jenkins-solution-template/ap-addtional.png)  

 После успешной проверки еще раз нажмите **ОК** на вкладке **Сводка**. Наконец выберите **Приобретение**, чтобы создать виртуальную машину Jenkins. Когда сервер будет готов, вы получите уведомление на портале Azure:   

![Уведомление о готовности Jenkins](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Подключение к Jenkins

Перейдите к виртуальной машине (например, http://jenkins2517454.eastus.cloudapp.azure.com/) в веб-браузере. Консоль Jenkins недоступна по незащищенному протоколу HTTP, поэтому на странице приведены инструкции по безопасному доступу к консоли Jenkins с компьютера с использованием туннеля SSH.

![Разблокировка Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Настройте туннель на странице из командной строки, используя команду `ssh`. Для этого замените `username` именем администратора виртуальной машины, указанным ранее при настройке виртуальной машины на основе шаблона решения.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

После запуска туннеля перейдите по адресу http://localhost:8080/ на локальном компьютере. 

Получите первоначальный пароль, выполнив команду ниже в командной строке при подключении к виртуальной машине Jenkins через SSH.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

При первом использовании разблокируйте панель мониторинга Jenkins с помощью этого пароля.

![Разблокировка Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Выберите **Install suggested plugins** (Установить предлагаемые подключаемые модули) на следующей странице и создайте учетную запись администратора Jenkins для доступа к панели мониторинга Jenkins.

![Экземпляр Jenkins готов!](./media/install-jenkins-solution-template/jenkins-welcome.png)

Сервер Jenkins готов к созданию кода.

## <a name="create-your-first-job"></a>Создание первого задания

Выберите **Create new jobs** (Создать задания) в консоли Jenkins. Назовите задание **mySampleApp**, выберите **Freestyle project** (Универсальный проект) и нажмите **ОК**.

![Создание задания](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Выберите вкладку **Управление исходным кодом**, включите **Git** и введите следующий URL-адрес в поле **URL-адрес репозитория**: `https://github.com/spring-guides/gs-spring-boot.git`

![Определение репозитория Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Выберите вкладку **Создание**, нажмите **Добавление шага сборки** и **Invoke Gradle script** (Вызов сценария Gradle). Выберите **Use Gradle Wrapper** (Использовать программу-оболочку Gradle), затем введите значение `complete` для параметра **Wrapper location** (Расположение программы-оболочки) и `build` для параметра **Задачи**.

![Использование программы-оболочки Gradle для сборки](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Нажмите кнопку **Advanced..** (Дополнительно...) и введите `complete` в поле **Root Build script** (Корневой скрипт сборки). Щелкните **Сохранить**.

![Установка дополнительных параметров на этапе сборки программы-оболочки Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Сборка кода

Выберите **Build Now** (Собрать сейчас), чтобы скомпилировать код и создать пакет для примера приложения. По завершении сборки выберите для проекта ссылку **Рабочая область**.

![Перейдите в рабочую область, чтобы получить JAR-файл из сборки](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Перейдите к `complete/build/libs` и проверьте наличие `gs-spring-boot-0.1.0.jar`, чтобы удостовериться в успешности сборки. Теперь сервер Jenkins готов к сборке ваших собственных проектов в Azure.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Использование агентов виртуальных машин для непрерывной интеграции с Jenkins.](jenkins-azure-vm-agents.md)

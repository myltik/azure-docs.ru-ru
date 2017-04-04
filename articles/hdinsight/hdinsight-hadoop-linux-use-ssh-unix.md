---
title: "Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X | Документация Майкрософт"
description: " Вы можете получить доступ к HDInsight с помощью Secure Shell (SSH). В этом документе содержатся сведения об использовании SSH для подключения к HDInsight из клиентов Windows, Linux, Unix и OS X."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Подключение к HDInsight (Hadoop) с помощью SSH

Узнайте, как выполнять безопасное подключение к HDInsight с помощью [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell). HDInsight может использовать Linux (Ubuntu) в качестве операционной системы для узлов в кластере. Протокол SSH подходит для подключения к головным и граничным узлам кластера под управлением Linux и выполнения команд непосредственно на этих узлах.

В следующей таблице содержатся сведения об адресе и порте, необходимые при подключении к HDInsight с помощью SSH.

| Адрес | Порт | Подключается к... |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Граничный узел (если он существует) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Основной головной узел |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Дополнительный головной узел |

> [!NOTE]
> Замените `<edgenodename>` на имя граничного узла. Дополнительные сведения об использовании граничных узлов см. в разделе [Доступ к граничному узлу](hdinsight-apps-use-edge-node.md#access-an-edge-node).
>
> Замените `<clustername>` на имя вашего кластера HDInsight.
>
> Мы советуем __всегда подключаться к граничному узлу__ (при наличии). Службы, размещенные на головных узлах, критически важны для работоспособности кластера. Граничный узел выполняет только размещаемые на нем службы.

## <a name="ssh-clients"></a>SSH-клиенты

Клиент `ssh` предоставляется в большинстве операционных систем. Microsoft Windows не предоставляет клиент SSH по умолчанию. Клиент SSH для Windows доступен в каждом из следующих пакетов:

* [Bash для Ubuntu в Windows 10](https://msdn.microsoft.com/commandline/wsl/about). Команда `ssh` доступна в командной строке Bash в Windows.

* [Git (https://git-scm.com/)](https://git-scm.com/). Команда `ssh` доступна в командной строке GitBash.

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/). Команда `ssh` доступна в командной строке Git Shell. GitHub Desktop можно настроить для использования Bash, командной строки Windows или PowerShell в качестве командной строки для Git Shell.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH). Команда PowerShell переносит OpenSSH в Windows и предоставляет тестовые выпуски.

    > [!WARNING]
    > В пакете OpenSSH содержится компонент сервера SSH `sshd`. Этот компонент запускает сервер SSH в системе, позволяя другим компонентам подключаться к нему. Если сервер SSH не нужно размещать в системе, не настраивайте этот компонент и не открывайте порт 22. Он не требуется для взаимодействия с HDInsight.

Существует также несколько графических SSH-клиентов, например [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) и [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Хотя эти клиенты можно использовать для подключения к HDInsight, процесс подключения к серверу отличается от использования служебной программы `ssh`. Дополнительные сведения см. в документации по используемому графическому клиенту.

## <a id="sshkey"></a>Проверка подлинности с помощью ключей SSH

Ключи SSH применяют [шифрование с открытым ключом](https://en.wikipedia.org/wiki/Public-key_cryptography) для защиты кластера. В отличие от паролей, они являются более безопасными и предоставляют простой способ защиты кластера HDInsight.

Если учетная запись SSH защищена ключом, при подключении клиент должен предоставить соответствующий закрытый ключ.

* В большинстве клиентов можно настроить использование __ключа по умолчанию__. Например, клиент `ssh` будет искать закрытый ключ в `~/.ssh/id_rsa` в средах Linux и Unix.

* Вы можете указать __путь к закрытому ключу__. В клиенте `ssh` это можно сделать с помощью параметра `-i`. Например, `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Если у вас есть __несколько закрытых ключей__ для использования на разных серверах, вы можете использовать служебные программы, например [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent), для автоматического выбора подходящего ключа.

> [!IMPORTANT]
>
> Если закрытый ключ защищен парольной фразой, при использовании ключа ее необходимо будет ввести. Существуют также служебные программы (например, `ssh-agent`), позволяющие кэшировать пароль.

### <a name="create-an-ssh-key-pair"></a>Создание пары ключей SSH

Чтобы создать файлы открытого и закрытого ключей, используйте команду `ssh-keygen`. Приведенная ниже команда создает пару 2048-разрядных ключей RSA, которую можно использовать с HDInsight.

    ssh-keygen -t rsa -b 2048

Во время создания ключа появится запрос на ввод сведений, например о том, где хранятся ключи и следует ли использовать парольную фразу. После завершения этой процедуры будут созданы два файла: файл открытого ключа и файл закрытого ключа.

* __Открытый ключ__ используется для создания кластера HDInsight. Этот файл имеет расширение `.pub`.

* __Закрытый ключ__ используется для проверки подлинности клиента в кластере HDInsight.

> [!IMPORTANT]
> Эти ключи можно защитить с помощью парольной фразы. По сути, это пароль закрытого ключа. Даже если кто-то получит ваш закрытый ключ, он не сможет использовать его, не зная парольную фразу.

### <a name="create-hdinsight-using-the-public-key"></a>Создание кластера HDInsight с помощью открытого ключа

| Метод создания | Использование открытого ключа |
| ------- | ------- |
| **Портал Azure** | Снимите флажок __Использовать тот же пароль в учетных данных кластера__, а затем выберите в качестве типа проверки подлинности SSH значение __Открытый ключ__. Наконец, выберите файл открытого ключа или вставьте текстовое содержимое файла в поле __Открытый ключ SSH__.</br>![Диалоговое окно открытого ключа SSH при создании кластера HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Используя параметр `-SshPublicKey` командлета `New-AzureRmHdinsightCluster`, передайте содержимое открытого ключа в виде строки.|
| **Azure CLI 1.0** | Используя параметр `--sshPublicKey` команды `azure hdinsight cluster create`, передайте содержимое открытого ключа в виде строки. |
| **Шаблон Resource Manager** | Пример использования ключей SSH с помощью шаблона см. на странице [Deploy HDInsight on Linux (w/ Azure Storage, SSH key)](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Развертывание HDInsight в Linux с использованием службы хранилища Azure и ключа SSH). Элемент `publicKeys` в файле [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) используется для передачи ключей в Azure при создании кластера. |

## <a id="sshpassword"></a>Проверка подлинности с помощью пароля

Учетные записи SSH можно защитить с помощью пароля. При подключении к HDInsight через SSH вам будет предложено ввести пароль.

> [!WARNING]
> Мы не советуем использовать такой метод проверки подлинности для SSH. Пароли можно угадать. К тому же они подвержены атакам методом подбора. Вместо этого рекомендуется использовать [проверку подлинности с помощью ключей SSH](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Создание кластера HDInsight с использованием пароля

| Метод создания | Указание пароля |
| --------------- | ---------------- |
| **Портал Azure** | По умолчанию учетная запись пользователя SSH содержит тот же пароль, что и учетная запись входа кластера. Чтобы использовать другой пароль, снимите флажок __Использовать тот же пароль в учетных данных кластера__, а затем введите пароль в поле __Пароль SSH__.</br>![Диалоговое окно пароля SSH при создании кластера HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Используя параметр `--SshCredential` командлета `New-AzureRmHdinsightCluster`, передайте объект `PSCredential`, содержащий имя учетной записи пользователя SSH и пароль. |
| **Azure CLI 1.0** | Используя параметр `--sshPassword` команды `azure hdinsight cluster create`, укажите значение пароля. |
| **Шаблон Resource Manager** | Пример использования пароля с помощью шаблона см. на странице [Deploy HDInsight cluster with Storage and SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Развертывание HDInsight с использованием службы хранилища и пароля SSH). Элемент `linuxOperatingSystemProfile` в файле [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) используется для передачи имени учетной записи SSH и пароля в Azure при создании кластера.|

### <a name="change-the-ssh-password"></a>Изменение пароля SSH

Дополнительные сведения об изменении пароля учетной записи пользователя SSH см. в разделе __Изменение паролей__ статьи [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Проверка подлинности при использовании присоединенного к домену кластера HDInsight

Если вы используете __присоединенный к домену кластер HDInsight__, после подключения по протоколу SSH необходимо выполнить команду `kinit`. Эта команда запрашивает имя и пароль пользователя домена и проверяет подлинность сеанса с помощью домена Azure Active Directory, связанного с кластером.

Дополнительные сведения см. в статье [Настройка присоединенных к домену кластеров HDInsight (предварительная версия)](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Подключение к рабочим узлам и узлам Zookeeper

Рабочие узлы и узлы Zookeeper недоступны непосредственно через Интернет, но к ним можно получить доступ из головного или граничного узла кластера. Шаги ниже описывают общую процедуру подключения к другим узлам.

1. Используйте SSH, чтобы подключиться к головному или граничному узлу:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. После подключения по протоколу SSH используйте команду `ssh`, чтобы соединиться с рабочим узлом в кластере:

        ssh sshuser@wn0-myhdi

    Чтобы получить список доменных имен для узлов кластера, ознакомьтесь с разделом [Пример. Получение полного доменного имени для узлов кластера](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Если учетная запись SSH защищена __паролем__, отобразится запрос на ввод пароля и будет установлено подключение.

Если она защищена __ключами SSH__, необходимо убедиться, что в локальной среде настроено перенаправление SSH-агента.

> [!NOTE]
> Другой способ получить прямой доступ ко всем узлам кластера — установить HDInsight в виртуальной сети Azure. Затем к той же виртуальной сети можно присоединить удаленный компьютер и получить прямой доступ ко всем узлам кластера.
>
> Дополнительные сведения см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Настройка перенаправления SSH-агента

> [!IMPORTANT]
> При описании шагов ниже предполагается использование системы под управлением Linux или UNIX, а также Bash Windows 10. Если выполнение этих шагов в вашей системе не дает результатов, см. документацию по соответствующему SSH-клиенту.

1. Откройте в текстовом редакторе файл `~/.ssh/config`. Если этот файл отсутствует, его можно создать. Для этого в командной строке введите команду `touch ~/.ssh/config`.

2. Добавьте следующий текст в файл `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Замените сведения об __узле__ на адрес узла, к которому вы подключаетесь с помощью SSH. В предыдущем примере используется граничный узел. Эта запись позволяет настроить перенаправление агента SSH для указанного узла.

3. Чтобы проверить перенаправление агента SSH, выполните следующую команду в терминале:

        echo "$SSH_AUTH_SOCK"

    Эта команда возвращает сведения аналогичные следующим:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Если сведения не возвращаются, то это значит, что `ssh-agent` не работает. Сведения о скриптах запуска агентов см. в документе [Using ssh-agent with ssh](http://mah.everybody.org/docs/ssh) (Использование агента SSH с SSH), а конкретные действия по установке и настройке `ssh-agent` см. в документации по соответствующему SSH-клиенту.

4. Убедившись в том, что **агент SSH** запущен, используйте следующую команду, чтобы добавить закрытый ключ SSH для агента:

        ssh-add ~/.ssh/id_rsa

    Если закрытый ключ хранится в другом файле, замените `~/.ssh/id_rsa` на путь к файлу.

5. Подключитесь к граничному или головному узлу кластера с помощью SSH. Затем воспользуйтесь командой SSH для подключения к рабочему узлу или узлу Zookeeper. Подключение устанавливается с использованием перенаправленного ключа.

## <a name="next-steps"></a>Дальнейшие действия

* [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md)
* [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md)
* [Доступ к граничному узлу](hdinsight-apps-use-edge-node.md#access-an-edge-node)

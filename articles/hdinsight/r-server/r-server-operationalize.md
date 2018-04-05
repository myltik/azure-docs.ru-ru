---
title: Ввод в эксплуатацию R Server в Azure HDInsight | Документация Майкрософт
description: Описание процедуры ввода в эксплуатацию R Server в Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 93957b7ee10527039bf2e96cc5470420cdef0651
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Ввод в эксплуатацию кластера R Server в Azure HDInsight

После того, как вы выполните моделирование данных на кластере R Server в HDInsight, полученную модель можно ввести в эксплуатацию для предоставления прогнозов. В этой статье описано выполнение этой задачи.

## <a name="prerequisites"></a>предварительным требованиям

* **Кластер R Server в HDInsight**. Инструкции см. в статье [Начало работы с R Server в HDInsight](r-server-get-started.md).

* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Ввод в эксплуатацию кластера R Server в универсальной конфигурации

1. Подключитесь к граничному узлу по протоколу SSH.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Инструкции по использованию SSH с Azure HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Перейдите в каталог для используемой версии и запустите DLL-файл для .NET с помощью команды sudo. 

    - Для Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Для Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. У вас есть несколько вариантов на выбор. Выберите первый вариант, как показано на следующем снимке экрана: **Настройка R Server для ввода в эксплуатацию**.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Теперь вам предлагаются доступные методы для ввода в эксплуатацию R Server. Выберите первый из представленных вариантов, введя символ **A**.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-2.png)

5. В ответ на соответствующий запрос дважды введите пароль пользователя с правами локального администратора.

6. Выходные данные сообщат, что операция выполнена успешно. Также вам будет предложено еще одно меню для выбора параметров. Выберите E, чтобы вернуться к главному меню.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-3.png)

7. По желанию вы можете запустить диагностический тест, как показано ниже.

    a. В главном меню выберите **6**, чтобы выполнить диагностические тесты.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-1.png)

    Б. В меню диагностических тестов выберите вариант **A**. Когда появится запрос, введите пароль пользователя с правами локального администратора.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-2.png)

    c. Убедитесь, что выходные данные подтверждают работоспособность системы.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-3.png)

    d. Из предложенного меню выберите вариант **E**, чтобы вернуться к главному меню, а затем введите **8** для выхода из служебной программы администрирования.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Длительные задержки при использовании веб-службы на Spark

Если при попытке использовать веб-службы, созданные с использованием функций mrsdeploy, в вычисленном контексте Spark возникают значительные задержки, может потребоваться добавить некоторые отсутствующие папки. Приложение Spark принадлежит пользователю с именем *rserve2* при вызове из веб-службы с помощью функций mrsdeploy. Для обхода этой проблемы:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Итак, вы завершили настройку для практического использования. Теперь с помощью пакета `mrsdeploy` на RClient вы можете подключаться к введенному в эксплуатацию решению на граничном узле и применять разные функции, например [удаленное выполнение](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) и [веб-службы](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). В зависимости от того, настроен ли кластер в виртуальной сети, может потребоваться настроить туннелирование с перенаправлением портов через сеанс SSH. В следующих разделах описано, как настроить этот туннель.

### <a name="r-server-cluster-on-virtual-network"></a>Кластер R Server в виртуальной сети

Убедитесь, что трафик через порт 12800 направляется на граничный узел. Это позволит использовать граничный узел для подключения к средству операционализации.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Если `remoteLogin()` не может подключиться к граничному узлу, но вам удается подключиться к нему по протоколу SSH, проверьте наличие и правильность настройки правила, разрешающего трафик через порт 12800. Если и после этого проблема не исчезнет, в качестве решения можно настроить туннелирование с перенаправлением портов через SSH. Инструкции см. в следующем разделе.

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Кластер RServer не настроен в виртуальной сети

Если ваш кластер не настроен в виртуальной сети или с подключением через виртуальную сеть возникают проблемы, можно использовать туннелирование с перенаправлением портов через SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Теперь при открытии сеанса SSH трафик от порта 12800 на вашем компьютере будет передаваться на порт 12800 граничного узла через сеанс SSH. Убедитесь, что вы используете `127.0.0.1:12800` в методе `remoteLogin()`. Этот параметр нужен для входа в решение на граничном узле через перенаправление портов.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Масштабирование вычислительных узлов, введенных в эксплуатацию на рабочих узлах HDInsight

Чтобы масштабировать вычислительные узлы, сначала нужно вывести рабочие узлы из эксплуатации, а затем настроить вычислительные узлы на выведенных из эксплуатации рабочих узлах.

### <a name="step-1-decommission-the-worker-nodes"></a>Шаг 1. Вывод рабочих узлов из эксплуатации

Кластер R Server не управляется через YARN. Если вы не выведете из эксплуатации рабочие узлы, диспетчер ресурсов YARN будет действовать неправильно из-за отсутствия информации об использовании ресурсов сервером. Чтобы этого избежать, мы рекомендуем вывести из эксплуатации рабочие узлы, прежде чем масштабировать вычислительные узлы.

Выполните следующие действия, чтобы вывести рабочие узлы из эксплуатации.

1. Войдите в консоль Ambari для кластера и щелкните вкладку **Hosts** (Узлы).

2. Выберите рабочие узлы, которые вы намерены вывести из эксплуатации.

3. Последовательно выберите **Actions** (Действия)  > **Selected Hosts** (Выбранные узлы)  > **Hosts** (Узлы)  > **Turn ON Maintenance Mode** (Включить режим обслуживания). Например, на следующем рисунке для вывода из эксплуатации выбраны узлы wn3 и wn4.  

   ![Вывод рабочих узлов из эксплуатации](./media/r-server-operationalize/get-started-operationalization.png)  

* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) и щелкните **Stop All Components** (Остановить все компоненты).
* Отмените выбор рабочих узлов и выберите головные узлы.
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) > **Restart All Components** (Перезапустить все компоненты).

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Шаг 2. Настройка вычислительных узлов на каждом из рабочих узлов, выведенных из эксплуатации

1. Поочередно откройте сеанс SSH для каждого рабочего узла, выведенного из эксплуатации.

2. Запустите служебную программу администрирования через подходящую библиотеку DLL для используемого кластера R Server. В R Server версии 9.1 выполните следующие действия.

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Введите **1**, чтобы выбрать параметр **Configure R Server for Operationalization** (Настройка R Server для ввода в эксплуатацию).

4. Введите **C**, чтобы выбрать пункт `C. Compute node`. Это настраивает вычислительный узел на рабочем узле.

5. Выйдите из служебной программы администрирования.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Шаг 3. Добавление сведений о вычислительных узлах на веб-узел

Когда вы настроите функции вычислительных узлов на всех рабочих узлах, выведенных из эксплуатации, вернитесь на граничный узел и добавьте IP-адреса этих рабочих узлов в конфигурацию веб-узла Microsoft R Server.

1. Подключитесь к граничному узлу по протоколу SSH.

2. Запустите `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Найдите раздел URI и добавьте в него информацию об IP-адресах и портах рабочих узлов.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Дополнительная информация

* [Управление кластером R Server в Azure HDInsight](r-server-hdinsight-manage.md)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Решения службы хранилища Azure для R Server в HDInsight](r-server-storage.md)
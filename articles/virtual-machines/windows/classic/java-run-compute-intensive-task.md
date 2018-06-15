---
title: Выполнение на виртуальной машине приложения Java для ресурсоемких вычислений
description: Узнайте, как создать виртуальную машину Azure для выполнения ресурсоемкого приложения Java, за работой которого может следить другое приложение Java.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528466"
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Выполнение ресурсоемкой задачи в Java-коде на виртуальной машине
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

В Azure для обработки ресурсоемких задач можно использовать виртуальную машину. Например, виртуальная машина может обрабатывать задачи и предоставлять результаты на клиентские компьютеры или в мобильные приложения. После прочтения этой статьи вы получите представление о создании виртуальной машины для выполнения ресурсоемкого приложения Java, за работой которого может следить другое приложение Java.

В этом учебнике предполагается, что вы умеете создавать консольные приложения Java, импортировать библиотеки в свое приложение Java и создавать архив Java (JAR). Знания о платформе Microsoft Azure не требуются.

Вы узнаете:

* Как создать виртуальную машину с установленным комплектом разработчика Java (JDK).
* Как удаленно войти в виртуальную машину.
* Как создать пространство имен служебной шины.
* Как создать приложение Java, выполняющее ресурсоемкую задачу.
* Как создать приложение Java, отслеживающее ход выполнения ресурсоемкой задачи.
* Как запускать приложения Java.
* Как останавливать приложения Java.

В качестве ресурсоемкой задачи в этом учебнике будет использоваться задача коммивояжера. Ниже приведен пример приложения Java, выполняющего ресурсоемкую задачу.

![Средство поиска решения для задачи коммивояжера][solver_output]

Ниже приведен пример приложения Java, отслеживающего выполнение ресурсоемкой задачи.

![Клиент задачи коммивояжера][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Для создания виртуальной машины
1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать ресурс** > **Среда выполнения приложений** > **Виртуальная машина**, а затем — **Из коллекции**.
3. В диалоговом окне **Virtual machine image select** (Выбор образа виртуальной машины) выберите пункт **JDK 7 Windows Server 2012**.
   Обратите внимание, что вариант **JDK 6 Windows Server 2012** доступен только в случае использования устаревших приложений, которые не готовы к выполнению JDK 7.
4. Нажмите кнопку **Далее**.
5. В диалоговом окне **Конфигурация виртуальной машины** выполните указанные ниже действия.
   1. Укажите имя виртуальной машины.
   2. Укажите размер виртуальной машины.
   3. Введите имя администратора в поле **Имя пользователя**. Запомните это имя и пароль, которые вы будете вводить далее. Они понадобятся для удаленного входа в виртуальную машину.
   4. Введите пароль в поле **Новый пароль** и повторно введите его в поле **Подтверждение**. Это пароль учетной записи администратора.
   5. Нажмите кнопку **Далее**.
6. В следующем диалоговом окне **Конфигурация виртуальной машины** выполните указанные ниже действия.
   1. Для **облачных служб** используйте значение по умолчанию **Создать новую облачную службу**.
   2. Значение поля **DNS-имя облачной службы** должно быть уникальным для cloudapp.net. При необходимости измените это значение, чтобы система Azure показывала его уникальность.
   3. Укажите регион, территориальную группу или виртуальную сеть. Для целей данного учебника укажите регион, например **США, запад**.
   4. В поле **Учетная запись хранения** выберите **Использовать автоматически созданную учетную запись хранения**.
   5. В разделе **Группа доступности** выберите **(Нет)**.
   6. Нажмите кнопку **Далее**.
7. В последнем диалоговом окне **Конфигурация виртуальной машины** выполните указанные ниже действия.
   1. Примите стандартные параметры для конечной точки.
   2. Нажмите **Завершено**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Удаленный вход в виртуальную машину
1. Войдите на [портал Azure](https://portal.azure.com).
2. Нажмите **Виртуальные машины**.
3. Нажмите имя виртуальной машины, в которую нужно войти.
4. Щелкните **Подключить**.
5. Отвечайте на запросы, появляющиеся при подключении к виртуальной машине. При получении запроса на ввод имени и пароля администратора используйте значения, которые были указаны при создании виртуальной машины.

Обратите внимание, что функции служебной шины Azure требуют, чтобы в хранилище **cacerts** JRE был установлен сертификат Baltimore CyberTrust Root. Этот сертификат автоматически включается в состав среды выполнения Java (JRE), используемой в этом учебнике. Если у вас нет этого сертификата в хранилище **cacerts** JRE, то сведения о его добавлении (а также о просмотре сертификатов в хранилище cacerts) см. в статье [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert].

## <a name="how-to-create-a-service-bus-namespace"></a>Создание пространства имен служебной шины
Чтобы начать использовать очереди служебной шины в Azure, необходимо сначала создать пространство имен службы. Это пространство имен службы предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

Создание пространства имен службы:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В нижней левой области навигации портала Azure щелкните **Service Bus, Access Control & Caching** (Служебная шина, контроль доступа и кэширование).
3. В верхней левой области портала Azure выберите узел **Служебная шина**, а затем нажмите кнопку **Создать**.  
   ![Снимок экрана узла Service Bus][svc_bus_node]
4. В диалоговом окне **Создание пространства имен** введите **пространство имен** и нажмите кнопку **Проверить доступность**, чтобы убедиться, что оно уникально.  
   ![Снимок экрана создания пространства имен][create_namespace]
5. Убедившись в доступности имени пространства имен, выберите страну или регион, где должно размещаться это пространство имен, и нажмите кнопку **Создать пространство имен** .  
   
   Созданное пространство имен появится на портале Azure, и его активация займет некоторое время. Прежде чем продолжить, дождитесь отображения состояния **Активно** .

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Получение учетных данных управления по умолчанию для пространства имен
Для выполнения операций управления, таких как создание очереди, над новым пространством имен необходимо получить учетные данные управления для пространства имен.

1. В левой области навигации щелкните узел **Служебная шина** , чтобы отобразить список доступных пространств имен.
   ![Снимок экрана доступных пространств имен][avail_namespaces]
2. Выберите пространство имен, которое вы только что создали, из появившегося списка.
   ![Снимок экрана списка пространств имен][namespace_list]
3. Правая область **Свойства** будет содержать свойства для нового пространства имен.
   ![Снимок экрана панели свойств][properties_pane]
4. Параметр **Ключ по умолчанию** скрыт. Нажмите кнопку **Просмотр**, чтобы отобразить учетные данные для безопасного доступа.
   ![Снимок экрана ключа по умолчанию][default_key]
5. Запишите значения параметров **Издатель по умолчанию** и **Ключ по умолчанию**, так как эти сведения будут использованы ниже для выполнения операций с пространством имен.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Как создать приложение Java, выполняющее ресурсоемкую задачу
1. На компьютере для разработки (который не обязательно должен быть созданной вами виртуальной машиной) скачайте [пакет Azure SDK для Java](https://azure.microsoft.com/develop/java/).
2. Создайте консольное приложение Java с помощью примера кода в конце этого раздела. В этом учебнике мы будем использовать имя файла Java **TSPSolver.java** . Измените заполнители **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** и **your\_service\_bus\_key** на значения **Пространство имен**, **Издатель по умолчанию** и **Ключ по умолчанию** для служебной шины соответственно.
3. После написания кода экспортируйте приложение в готовый к запуску архив Java (JAR) и упакуйте необходимые библиотеки в созданный JAR-файл. В этом учебнике мы будем использовать имя созданного JAR-файла **TSPSolver.jar** .

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Как создать приложение Java, отслеживающее ход выполнения ресурсоемкой задачи
1. На компьютере для разработки создайте консольное приложение Java с помощью примера кода в конце этого раздела. В этом учебнике мы будем использовать имя файла Java **TSPClient.java** . Как показано выше, измените заполнители **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** и **your\_service\_bus\_key** на значения **Пространство имен**, **Издатель по умолчанию** и **Ключ по умолчанию** для служебной шины соответственно.
2. Экспортируйте приложение в готовый к запуску JAR-файл и упакуйте в него необходимые библиотеки. В этом учебнике мы будем использовать имя созданного JAR-файла **TSPClient.jar** .

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Как запускать приложения Java
Для запуска ресурсоемкого приложения сначала создайте очередь, а затем решите задачу коммивояжера, в результате чего в очередь служебной шины будет помещен оптимальный маршрут. Во время (или после) выполнения ресурсоемкого приложения запустите клиент для отображения результатов из очереди служебной шины.

### <a name="to-run-the-compute-intensive-application"></a>Запуск ресурсоемкого приложения
1. Войдите в виртуальную машину.
2. Создайте папку, где будет выполняться приложение. Например, **c:\TSP**.
3. Скопируйте файл **TSPSolver.jar** в папку **c:\TSP**.
4. Создайте файл с именем **c:\TSP\cities.txt** со следующим содержимым.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. В командной строке смените каталог на c:\TSP.
6. Убедитесь, что папка bin среды JRE указана в переменной среды PATH.
7. Вам необходимо создать очередь служебной шины перед запуском перестановок средства поиска решения для задачи коммивояжера. Выполните следующую команду, чтобы создать очередь служебной шины.
   
        java -jar TSPSolver.jar createqueue
8. После создания очереди можно запустить перестановки средства поиска решения для задачи коммивояжера. Например, выполните следующую команду, чтобы запустить поиск решения для 8 городов.
   
        java -jar TSPSolver.jar 8
   
   Если число не указано, выполняется поиск для 10 городов. Когда средство поиска решения находит кратчайшие маршруты, оно добавляет их в очередь.

> [!NOTE]
> Чем больше указываемое число, тем дольше будет выполняться поиск. Например, поиск для 14 городов может занять несколько минут, а для 15 городов — несколько часов. Поиск для 16 и более городов может занять несколько дней (а потом недель, месяцев и лет). Это вызвано резким ростом числа перестановок, которое приходится оценивать средству поиска решения по мере увеличения числа городов.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Запуск клиентского приложения мониторинга
1. Выполните вход на компьютер, где будет выполняться клиентское приложение. Это не обязательно должен быть именно компьютер с приложением **TSPSolver** .
2. Создайте папку, где будет выполняться приложение. Например, **c:\TSP**.
3. Скопируйте файл **TSPClient.jar** в папку **c:\TSP**.
4. Убедитесь, что папка bin среды JRE указана в переменной среды PATH.
5. В командной строке смените каталог на c:\TSP.
6. Выполните следующую команду:
   
        java -jar TSPClient.jar
   
    При необходимости укажите период ожидания в минутах между проверками очереди, передав аргумент командной строки. По умолчанию период ожидания при проверке очереди составляет 3 минуты, он используется, если в **TSPClient** не было передано никакого аргумента командной строки. Если вы хотите использовать другое значение интервала ожидания, например 1 минуту, выполните следующую команду:
   
        java -jar TSPClient.jar 1
   
    Клиент будет работать, пока не получит сообщение "Выполнено" из очереди. Обратите внимание, что в случае выполнения нескольких экземпляров средства поиска решения без запуска клиента для полной очистки очереди может потребоваться запустить клиент несколько раз. Кроме того, можно удалить очередь и создать ее заново. Чтобы удалить очередь, выполните следующую команду **TSPSolver** (не **TSPClient**):
   
        java -jar TSPSolver.jar deletequeue
   
    Средство поиска решения будет выполняться до завершения проверки всех маршрутов.

## <a name="how-to-stop-the-java-applications"></a>Как останавливать приложения Java
Как для средства поиска решения, так и для клиентского приложения можно нажать клавиши **CTRL+C** для выхода из программы, если потребовалось прервать работу до ее нормального завершения.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md

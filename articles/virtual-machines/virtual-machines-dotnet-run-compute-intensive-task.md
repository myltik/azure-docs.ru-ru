<properties
	pageTitle="Выполнение ресурсоемкого приложения .NET на виртуальной машине | Microsoft Azure"
	description="Узнайте, как развернуть и запустить ресурсоемкое приложение .NET на виртуальной машине Azure и использовать очереди служебной шины Azure для удаленного мониторинга хода выполнения."
	services="virtual-machines"
	documentationCenter=".net"
	authors="wadepickett"
	manager="wpickett"
	editor="mollybos"
	tags=“azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="wpickett"/>

# Выполнение ресурсоемкой задачи в .NET на виртуальной машине Azure

В Azure для обработки ресурсоемких задач можно использовать виртуальную машину. Например, виртуальная машина может обрабатывать задачи и предоставлять результаты на клиентские компьютеры или в мобильные приложения. После изучения этого учебника вы будете иметь представление о создании виртуальной машины с выполняемым ресурсоемким приложением .NET, за работой которого может следить другое приложение .NET.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

В этом учебнике предполагается, что вы знаете, как создавать консольные приложения .NET. Знания о платформе Azure не требуются.

Вы узнаете:

* Как создать виртуальную машину.
* Как удаленно войти в виртуальную машину.
* Создание пространства имен служебной шины Azure
* Как создать приложение .NET, выполняющее ресурсоемкую задачу.
* Как создать приложение .NET, отслеживающее ход выполнения ресурсоемкой задачи.
* Как запускать приложения .NET.
* Как останавливать приложения .NET.

В качестве ресурсоемкой задачи в этом учебнике будет использоваться задача коммивояжера. Ниже приведен пример приложения .NET, выполняющего ресурсоемкую задачу:

![Средство поиска решения для задачи коммивояжера][solver_output]

Ниже приведен пример приложения .NET, отслеживающего выполнение ресурсоемкой задачи:

![Клиент задачи коммивояжера][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Для создания виртуальной машины

1. Войдите на [портал Azure](https://manage.windowsazure.com).
2. Нажмите кнопку **Создать**.
3. Щелкните **Виртуальная машина**.
4. Щелкните **Быстрое создание**.
5. В окне **Создание виртуальной машины** введите значение для параметра **DNS-имя**.
6. В раскрывающемся списке **Образ** выберите образ, например **Windows Server 2012 R2**.
7. Введите имя администратора в поле **Имя пользователя**. Запомните это имя и пароль, которые вы будете вводить далее. Они понадобятся для удаленного входа в виртуальную машину.
8. Введите пароль в поле **Новый пароль** и повторно введите его в поле **Подтверждение**.
9. В раскрывающемся списке **Расположение** выберите расположение центра обработки данных для виртуальной машины.
10. Щелкните **Создать виртуальную машину**. Следить за состоянием можно в разделе **Виртуальные машины** на портале Azure. Когда отображается состояние **Активная**, можно выполнить вход в виртуальную машину.

## Для удаленного входа в виртуальную машину.

1. Войдите на [портал Azure](https://manage.windowsazure.com).
2. Нажмите **Виртуальные машины**.
3. Нажмите имя виртуальной машины, в которую нужно войти.
4. Щелкните **Подключить**.
5. Отвечайте на запросы, появляющиеся при подключении к виртуальной машине. При получении запроса на ввод имени и пароля администратора используйте значения, которые были указаны при создании виртуальной машины.

## Создание пространства имен служебной шины

Чтобы начать использовать очереди служебной шины в Azure, необходимо сначала создать пространство имен службы. Это пространство имен службы предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

Создание пространства имен службы:

1.  Войдите на [портал Azure](https://manage.windowsazure.com).
2.  В левой области навигации портала Azure нажмите кнопку **Служебная шина**.
3.  В нижней части портала Azure нажмите кнопку **Создать**.

    ![Создание новой шины служебной шины][create_service_bus]
4.  В диалоговом окне **Создать пространство имен** введите имя пространства имен. Система немедленно проверяет, доступно ли это имя, так как оно должно быть уникальным.

    ![Диалоговое окно "Создать пространство имен"][create_namespace_dialog]
5.  Проверив доступность имени пространства имен, выберите регион, в котором будет размещаться пространство имен (убедитесь, что используется тот же регион, где размещается виртуальная машина).

    > [AZURE.IMPORTANT]Выберите **тот же регион**, который вы используете или собираетесь использовать для своей виртуальной машины. Это обеспечит наилучшую производительность.

6. Если у вас несколько подписок Azure для активной учетной записи, выберите ту, которую следует использовать для данного пространства имен. (При наличии только одной подписки в активной учетной записи раскрывающийся список с подписками не отображается.)
7. Щелкните значок галочки. Теперь система создает пространство имен службы и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

	![Снимок экрана с элементом создания][click_create]

Созданное пространство имен появится на портале Azure; его активация займет некоторое время. Прежде чем продолжить, дождитесь отображения состояния **Активно**.

## Получение учетных данных управления по умолчанию для пространства имен

Для выполнения операций управления, таких как создание очереди, над новым пространством имен необходимо получить учетные данные управления для пространства имен.

1.  В левой области навигации щелкните узел **Служебная шина**, чтобы отобразить список доступных пространств имен: ![Снимок экрана доступных пространств имен][available_namespaces]
2.  Выберите в списке только что созданное пространство имен: ![Снимок экрана списка пространств имен][namespace_list]
3. Щелкните **Сведения о подключении**. ![Кнопка "Ключ доступа"][access_key_button]
4.  В диалоговом окне найдите запись **Строка подключения**. Запишите это значение. Оно понадобится вам в дальнейшем для выполнения операций с пространством имен.

## Создание приложения .NET, выполняющего ресурсоемкую задачу

1. На компьютере для разработки (который не обязательно должен быть созданной вами виртуальной машиной) скачайте [пакет Azure SDK для .NET](http://azure.microsoft.com/develop/net/).
2. Создайте консольное приложение .NET с проектом TSPSolver. Убедитесь, что в качестве целевой платформы выбран параметр **.NET Framework 4** или более поздней версии (а не **клиентский профиль .NET Framework 4**). Целевую платформу можно задать после создания проекта, выполнив следующие действия: в меню Visual Studio выберите пункт **Проекты**, щелкните **Свойства**, откройте вкладку **Приложение**, а затем установите значение для параметра **Целевая платформа**.
3. Добавьте библиотеку Microsoft ServiceBus. В обозревателе решений Visual Studio щелкните правой кнопкой мыши **TSPSolver**, выберите пункт **Добавить ссылку**, щелкните вкладку **Обзор**, перейдите к пакету SDK Azure .NET (например, в папку C:\\Program Files\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) и укажите в качестве ссылки **Microsoft.ServiceBus.dll**.
4. Добавьте библиотеку сериализации для среды выполнения системы. В обозревателе решений Visual Studio щелкните правой кнопкой мыши **TSPSolver**, выберите **Добавить ссылку**, откройте вкладку **.NET** и выберите **System.Runtime.Serialization** в качестве ссылки.
5. Используйте пример кода, приведенный в конце этого раздела, для содержимого файла Program.cs.
6. Измените заполнитель **ваша\_строка\_подключения** на значение **строки подключения** для служебной шины.
7. Скомпилируйте приложение. Это приводит к созданию TSPSolver.exe в папке bin вашего проекта (либо bin\\release, либо bin\\debug, в зависимости от ориентации на сборку выпуска или отладочную сборку). Позднее вы скопируете этот исполняемый файл и Microsoft.ServiceBus.dll на виртуальную машину.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;

	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;

	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;

	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";

	        private static void BuildDistances(String fileLocation, int numCities)
	        {

	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };

	                double[,] cityLocs = new double[numCities, 2];

	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();

	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }

	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {

	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }

	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";

	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        static void Main(string[] args)
	        {
	            try
	            {

                  String connectionString = @"your_connection_string";

	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {

	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }

	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }

	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }

	                Console.WriteLine("Running for {0} cities.", numCities);

	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();

	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));

	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



## Создание приложения .NET, отслеживающего ход выполнения ресурсоемкой задачи

1. На компьютере для разработки создайте консольное приложение .NET, используя TSPClient в качестве имени проекта. Убедитесь, что в качестве целевой платформы выбран параметр **.NET Framework 4** или более поздней версии (а не **клиентский профиль .NET Framework 4**). Целевую платформу можно задать после создания проекта, выполнив следующие действия: в меню Visual Studio выберите пункт **Проекты**, щелкните **Свойства**, откройте вкладку **Приложение**, а затем установите значение для параметра **Целевая платформа**.
2. Добавьте библиотеку Microsoft ServiceBus. В обозревателе решений Visual Studio щелкните правой кнопкой мыши **TSPClient**, выберите пункт **Добавить ссылку**, щелкните вкладку **Обзор**, перейдите к пакету SDK Azure .NET (например, в папку C:\\Program Files\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) и укажите в качестве ссылки **Microsoft.ServiceBus.dll**.
3. Добавьте библиотеку сериализации для среды выполнения системы. В обозревателе решений Visual Studio щелкните правой кнопкой мыши **TSPClient**, выберите **Добавить ссылку**, откройте вкладку **.NET** и выберите **System.Runtime.Serialization** в качестве ссылки.
4. Используйте пример кода, приведенный в конце этого раздела, для содержимого файла Program.cs.
5. Измените заполнитель **ваша\_строка\_подключения** на значение **строки подключения** для служебной шины.
6. Скомпилируйте приложение. Это приводит к созданию TSPClient.exe в папке bin вашего проекта (либо bin\\release, либо bin\\debug, в зависимости от ориентации на сборку выпуска или отладочную сборку). Вы можете выполнить этот код на компьютере разработчика или скопировать этот исполняемый файл и Microsoft.ServiceBus.dll на машину, где будет выполняться клиентское приложение (это необязательно должна быть ваша виртуальная машина).

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep

	namespace TSPClient
	{
	    class Program
	    {

	        static void Main(string[] args)
	        {

	            try
	            {

	                Console.WriteLine("Starting at {0}", DateTime.Now);

									String connectionString = @"your_connection_string";

	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                BrokeredMessage message;

	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.

	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }

	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

	                while (true)
	                {
	                    message = queueClient.Receive();

	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);

	                            // Remove message from queue.
	                            message.Complete();

	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

## Запуск приложений .NET

Для запуска ресурсоемкого приложения сначала создайте очередь, а затем решите задачу коммивояжера, в результате чего в очередь служебной шины будет помещен оптимальный маршрут. Во время (или после) выполнения ресурсоемкого приложения запустите клиент для отображения результатов из очереди служебной шины.

### Запуск ресурсоемкого приложения

1. Войдите в виртуальную машину.
2. Создайте папку с именем c:\\TSP. В ней будет выполняться приложение.
3. Скопируйте TSPSolver.exe и Microsoft.ServiceBus.dll, которые находятся в папке bin проекта TSPSolver, в папку c:\\TSP.
4. Создайте файл с именем c:\\TSP\\cities.txt со следующим содержимым:

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

5. В командной строке смените каталог на c:\\TSP.
6. Вам необходимо создать очередь служебной шины перед запуском перестановок средства поиска решения для задачи коммивояжера. Выполните следующую команду, чтобы создать очередь служебной шины:

        TSPSolver createqueue

7. После создания очереди можно запустить перестановки средства поиска решения для задачи коммивояжера. Например, выполните следующую команду, чтобы запустить поиск решения для 8 городов.

        TSPSolver 8

 Если число не указано, средство поиска решения выполняет поиск для 10 городов. Когда средство поиска решения находит кратчайшие маршруты, оно добавляет их в очередь.

Средство поиска решения будет выполняться до завершения проверки всех маршрутов.

> [AZURE.NOTE]Чем больше указываемое число, тем дольше будет выполняться поиск. Например, поиск для 14 городов может занять несколько минут, а для 15 городов — несколько часов. Поиск для 16 и более городов может занять несколько дней (а потом недель, месяцев и лет). Это вызвано резким ростом числа перестановок, которое приходится оценивать средству поиска решения по мере увеличения числа городов.

### Запуск клиентского приложения мониторинга
1. Выполните вход на компьютер, где будет запускаться клиентское приложение. Это не обязательно должен быть именно компьютер с приложением TSPSolver.
2. Создайте папку, где будет выполняться приложение. Например, c:\\TSP.
3. Скопируйте TSPClient.exe и Microsoft.ServiceBus.dll, которые находятся в папке bin проекта SPClient, в папку c:\\TSP.
4. В командной строке смените каталог на c:\\TSP.
5. Выполните следующую команду:

        TSPClient

    При необходимости укажите период ожидания в минутах между проверками очереди, передав аргумент командной строки. По умолчанию период ожидания при проверке очереди составляет 3 минуты, он используется, если в TSPClient не было передано никакого аргумента командной строки. Если вы хотите использовать другое значение интервала ожидания, например 1 минуту, выполните следующую команду:

	    TSPClient 1

    Клиент будет работать, пока не получит сообщение "Выполнено" из очереди. Обратите внимание, что в случае выполнения нескольких экземпляров средства поиска решения без запуска клиента для полной очистки очереди может потребоваться запустить клиент несколько раз. Кроме того, можно удалить очередь и создать ее заново. Чтобы удалить очередь, выполните следующую команду TSPSolver (не TSPClient):

        TSPSolver deletequeue

## Остановка приложений .NET.

Как для средства поиска решения, так и для клиентского приложения можно нажать клавиши Ctrl+C для выхода из программы, если потребовалось прервать работу до ее нормального завершения.

## Альтернативы созданию и удалению очереди с помощью TSPSolver
Для создания или удаления очереди вместо TSPSolver можно использовать [портал Azure](https://manage.windowsazure.com). Посетите раздел служебной шины портала Azure для доступа к пользовательским интерфейсам для создания или удаления очереди, а также для получения строки подключения, издателя и ключа доступа. Можно также использовать панель мониторинга очередей служебной шины, что позволяет просматривать метрики для входящих и исходящих сообщений.

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png

<!---HONumber=Sept15_HO4-->
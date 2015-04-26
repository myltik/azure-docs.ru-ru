<properties 
	pageTitle="Анализ данных датчиков с помощью Apache Storm и Microsoft Azure HDInsight (Hadoop)" 
	description="Сведения об использовании  Использование Apache Storm для обработки данных датчиков в режиме реального времени в HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Анализ данных датчиков с помощью Storm и HBase в HDInsight (Hadoop)

Узнайте, как построить решение на базе кластера Storm в HDInsight для обработки данных датчиков, полученных из концентратора событий Azure. Во время обработки топология Storm сохранит входящие данные в кластере HBase. Топология будет использовать также библиотеку SignalR для предоставления информации в режиме, близком к реальному времени, с помощью информационной панели, расположенной на веб-сайте Azure.

> [AZURE.NOTE] Полная версия проекта доступна на [https://github.com/Blackmist/hdinsight-eventhub-example](https://github.com/Blackmist/hdinsight-eventhub-example).

## Предварительные требования

* Подписка Azure

* Visual Studio с [Microsoft Azure SDK для .NET](http://azure.microsoft.com/downloads/archive-net-downloads/)

* [Java и JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, JDK, Maven и Git доступны также через диспетчер пакетов [Chocolatey NuGet](http://chocolatey.org/).

## Создание панели мониторинга

Панель мониторинга используется для отображения информации датчика в режиме, близком к реальному времени. В данном случае панель мониторинга является приложением ASP.NET, расположенным на веб-сайте Azure. Основной целью приложения является служить концентратором [SignalR](http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr), получающим информацию от топологии Storm по мере обработки сообщений.

Веб-сайт содержит также статический файл index.html, связанный с SignalR и использующий D3.js для графического представления данных, переданных топологией Storm.

> [AZURE.NOTE] Хотя вместо SignalR вы можете использовать необработанный WebSockets, WebSockets не имеет встроенного механизма масштабирования, который потребуется при необходимости масштабировать веб-страницу. SignalR можно масштабировать с помощью Azure Service Bus ([http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus](http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus)).
>
> Пример использования топологии Storm для взаимодействия с веб-сайтом Python с помощью WebSockets см. в проекте [Storm Tweet Sentiment D3 Visualization](https://github.com/P7h/StormTweetsSentimentD3Viz).

1. Создайте новое приложение на C# в Visual Studio с использованием шаблона проекта **веб-приложения ASP.NET**. Назовите новое приложение **Панель мониторинга**.

2. В окне **Новый проект ASP.NET** выберите шаблон приложения **Новый**. В разделе **Microsoft Azure** выберите **Разместить в облаке** и **Веб-сайт**. Чтобы завершить, нажмите кнопку **ОК**.

	> [AZURE.NOTE] Если появится соответствующая подсказка, выполните вход в подписку Azure.

3. В диалоговом окне **Настройка веб-сайта Windows Azure Site** введите **Имя веб-сайта** и **Регион** вашего веб-сайта, затем нажмите **OK**. Так вы создадите веб-сайт Azure, содержащий панель мониторинга.

4. В **обозревателе решений**щелкните правой кнопкой мыши проект, затем выберите **Добавить | Класс концентратора SignalR (v2)**. Присвойте новому классу имя **DashHub.cs** и добавьте его в проект. Он будет содержать концентратор SignalR, используемый для взаимодействия между HDInsight и веб-страницей панели мониторинга.

	> [AZURE.NOTE] Если вы используете Visual Studio 2012, то шаблон **Класс концентратора SignalR (v2)** не будет доступен. Вместо него можно добавить обычный **класс** DashHub. Вам может также потребоваться вручную установить пакет SignalR, открыв **Инструменты | Диспетчер пакета библиотек | Консоль диспетчера пакетов** и выполнив следующую команду:
	>
	> `install-package Microsoft.AspNet.SignalR`

5. Замените код в **DashHub.cs** на приведенный ниже:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using Microsoft.AspNet.SignalR;

		namespace dashboard
		{
		    public class DashHub : Hub
		    {
		        public void Send(string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(message);
		        }
		    }
		}

6. В **обозревателе решений**щелкните правой кнопкой мыши проект, затем выберите **Добавить | Класс запуска OWIN**. Назовите новый класс **Startup.cs**.

	> [AZURE.NOTE] Если вы используете Visual Studio 2012, шаблон **Класс запуска OWIN** не будет доступен. Вместо него можно добавить обычный **класс** Startup.

7. Замените содержимое файла **Startup.cs** следующим кодом.

		using System;
		using System.Threading.Tasks;
		using Microsoft.Owin;
		using Owin;

		[assembly: OwinStartup(typeof(dashboard.Startup))]

		namespace dashboard
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            app.MapSignalR();
		        }
		    }
		}

8. В **обозревателе решений** щелкните правой кнопкой мыши проект, затем выберите **Добавить | Страница HTML**. Присвойте новой странице имя **index.html**. Страница будет содержать панель мониторинга в режиме реального времени для этого проекта. Она будет получать информацию от DashHub и будет отображать диаграмму, используя D3.js.

9. В **Обозревателе решений** щелкните правой кнопкой мыши **index.html** и выберите **Задать как начальную страницу**.

10. Замените существующий код в файле **index.html** следующим кодом.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Dashboard</title>
		    <style>

		        .x.axis line {
		            shape-rendering: auto;
		        }

		        .line {
		            fill: none;
		            stroke-width: 1.5px;
		        }

		    </style>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Reference d3.js.-->
		    <script src="http://d3js.org/d3.v3.min.js"></script>
		</head>
		<body>
		    <script>
		        $(function () {
		            //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
		            var n = 243,                                 //number of x coordinates in the graph
		            duration = 750,                          //duration for transitions
		            deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
		            now = new Date(Date.now() - duration),   //Now
		            //fill an array of arrays with dummy data to start the chart
		            //each item in the top-level array is a line
		            //each item in the line arrays represents the X coordinate across a graph
		            //The 'value' within each line array represents the Y coordinate for that point
		            data = [
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; })
		            ];

		            //Color scale for 10 items
		            var color = d3.scale.category10();
		            //The domain for color (the device IDs)
		            var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
		            //This will auto-generate colors for this range of IDs
		            color.domain(devices);

		            //set margins and figure out width/height
		            var margin = {top: 6, right: 0, bottom: 20, left: 40},
		                width = 960 - margin.right,
		                height = 240 - margin.top - margin.bottom;

		            //the time scale for the X axis
		            var x = d3.time.scale()
		                .domain([now - (n - 2) * duration, now - duration])
		                .range([0, width]);

		            //the numerical scale for the Y axis
		            var y = d3.scale.linear()
		                .domain([100, 0])
		                .range([0, height]);

		            //The line, which is really just a
		            //couple functions that we can pass data to
		            //in order to get back x/y coords.
		            var line = d3.svg.line()
		                .interpolate("basis")
		                .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
		                .y(function (d, i) { return y(d.value); });

		            //Find the HTML body element and add a child SVG element
		            var svg = d3.select("body").append("svg")
		                .attr("width", width + margin.left + margin.right)
		                .attr("height", height + margin.top + margin.bottom)
		              .append("g")
		                .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

		            //Define a clipping path, because we need to clip
		            //the graph to render only the bits we want to see
		            //as it moves
		            svg.append("defs").append("clipPath")
		                .attr("id", "clip")
		              .append("rect")
		                .attr("width", width)
		                .attr("height", height);

		            //Append the x axis
		            var axis = svg.append("g")
		                .attr("class", "x axis")
		                .attr("transform", "translate(0," + height + ")")
		                .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

		            //append the y axis
		            var yaxis = svg.append("g")
		                .attr("class", "y axis")
		                .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

		            //append the clipping path
		            var linegroup = svg.append("g")
		              .attr("clip-path", "url(#clip)");

		            //magic. Select all paths with a class of .line
		            //if they don't exist, make them.
		            //use the points in the line object to define
		            //the paths
		            //set the color to the cooresponding auto-generated coclor
		            var path = linegroup.selectAll(".line")
		              .data(data)
		              .enter().append("path")
		              .attr("class", "line")
		              .attr("d", line)
		              .style("stroke", function (d, i) { return color(i); });

		            //We need to transition the graph after all
		            //lines have been updated. There's no
		            //built-in for this, so this function
		            //does reference counting on end events
		            //for each line, then applies whatever
		            //callback when all are finished.
		            function endall(transition, callback) {
		                var n = 0;
		                transition
		                    .each(function () { ++n; })
		                    .each("end", function () { if (!--n) callback.apply(this, arguments); });
		            }

		            //wire up the SignalR client and listen for messages
		            var chat = $.connection.dashHub;
		            chat.client.broadcastMessage = function (message) {
		                //parse the JSON data
		                var incomingData = JSON.parse(message);
		                //stuff it in the global array slot for the device ID
		                deviceValue[incomingData.device] = incomingData.temperature;

		            };
		            //start listening
		            $.connection.hub.start();
		            //tick for D3 graphics
		            tick();

		            function tick() {
		                // update the domains
		                now = new Date();
		                x.domain([now - (n - 2) * duration, now - duration]);

		                //push the (presumably) fresh data deviceValue array onto
		                //the arrays that define the lines.
		                for (i = 0; i < 10; i++) {
		                    data[i].push({ value: deviceValue[i] });
		                    //data[1].push({ value: maxValue });
		                }
		                //slide the x-axis left
		                axis.transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .call(x.axis);

		                //Update the paths based on the updated line data
		                //and slide left
		                path
		                    .attr("d", line)
		                    .attr("transform", null)
		                .transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
		                    .call(endall, tick);

		                // pop the old data point off the front
		                // of the arrays
		                for (var i = 0; i < data.length; i++) {
		                    data[i].shift();
		                };
		            };
		         })()
		        </script>
		    </body>
		</html>

	> [AZURE.NOTE] Диспетчер пакетов может установить новую версию сценариев SignalR. Проверьте, совпадают ли ссылки скриптов, приведенных ниже, с версиями файлов скриптов в проекте (если вместо концентратора с помощью NuGet была добавлена библиотека SignalR, они будут различаться.)

11. В **обозревателе решений** щелкните правой кнопкой мыши проект, затем выберите **Добавить | Страница HTML**. Присвойте новой странице имя **test.html**. Эту страницу можно использовать для проверки DashHub и панели мониторинга, отправляя и получая сообщения.

12. Замените существующий код в файле **test.html** следующим кодом.

		<!DOCTYPE html>
		<html>
		<head>
		    <title>Test</title>
		    <style type="text/css">
		        .container {
		            background-color: #99CCFF;
		            border: thick solid #808080;
		            padding: 20px;
		            margin: 20px;
		        }
		    </style>
		</head>
		<body>
		    <div class="container">
		        <input type="text" id="message" />
		        <input type="button" id="sendmessage" value="Send" />
		        <input type="hidden" id="displayname" />
		        <ul id="discussion"></ul>
		    </div>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Add script to update the page and send messages.-->
		    <script type="text/javascript">
		        $(function () {
		            // Declare a proxy to reference the hub.
		            var chat = $.connection.dashHub;
		            // Create a function that the hub can call to broadcast messages.
		            chat.client.broadcastMessage = function (message) {
		                // Html encode display the message.
		                var encodedMsg = $('<div />').text(message).html();
		                // Add the message to the page.
		                $('#discussion').append('<li>' + encodedMsg + '</li>');
		            };
		            // Set initial focus to message input box.
		            $('#message').focus();
		            // Start the connection.
		            $.connection.hub.start().done(function () {
		                $('#sendmessage').click(function () {
		                    // Call the Send method on the hub.
		                    chat.server.send($('#message').val());
		                    // Clear text box and reset focus for next comment.
		                    $('#message').val('').focus();
		                });
		            });
		        });
		    </script>
		</body>
		</html>

13. Нажмите **Сохранить все** для проекта.

14. В **обозревателе решений**, щелкните правой кнопкой мыши **Панель мониторинга** проекта и выберите **Опубликовать**. Выберите веб-сайт, созданный для этого проекта, а затем нажмите **Опубликовать**.

15. После того как вы опубликовали сайт, должна открыться веб-страница, отображающая движущуюся временную шкалу.

### Тестирование панели мониторинга

1. Чтобы проверить работоспособность SignalR и отображаются ли на панели мониторинга линии графика данных, отправленных в SignalRа, откройте новое окно браузера для страницы **test.html** на этом веб-сайте. Например **http://mydashboard.azurewebsites.net/test.html**.

2. Панель мониторинга ожидает данные в формате JSON с **идентификатором устройства** и значением **температуры**. Например **{"device":0, "temperature":80}**. Введите тестовые значения на странице **test.html**, используя коды устройств от 0 до 9, пока панель мониторинга открыта на другой странице. Обратите внимание, что линии для каждого отдельного устройства рисуются разными цветами.

## Настройка концентраторов Event Hub

Концентратор Event Hub применяется для получения сообщений (событий) от датчиков. Чтобы создать новый концентратор Event Hub, выполните следующие действия.

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ | Service Bus | Концентратор событий | Настраиваемое создание**.

2. В диалоговом окне **Добавить новый концентратор событий** введите **Имя концентратора событий**, выберите **Регион** для создания в нем концентратора, либо создайте новое пространство имен или выберите существующее. После этого щелкните **Стрелку**.

3. В диалоговом окне **Настройка концентратора событий** введите значения параметров **Число разделов** и **Хранение сообщений**. В этом примере числу разделов присвойте значение 10, а хранению сообщений - 1.

4. После создания концентратора событий выберите пространство имен, затем выберите **Концентраторы событий**. После этого выберите раннее созданный вами концентратор событий.

5. Выберите **Настроить**, затем создайте две новые политики доступа, используя следующую информацию.

	<table>
	<tr><th>Имя</th><th>Разрешения</th></tr>
	<tr><td>устройства</td><td>Отправка</td></tr>
	<tr><td>storm</td><td>Прием</td></tr>
	</table>

	После создания разрешений выберите значок **Сохранить** в нижней части страницы. Это создает политику совместного доступа, которая будет использоваться для отправки сообщений концентратору и чтения с него этих сообщений.

6. После сохранения политик используйте клавишу **Генератор ключей совместного доступа** в нижней части страницы для получения ключа для обеих политик: **устройства** и **storm**. Сохраните для дальнейшего использования.

### Отправка сообщений концентратору Event Hub

Из-за отсутствия стандартного и доступного всем набора датчиков для генерации случайных цифр используется приложение .NET. Приложение .NET, созданное согласно указанной ниже процедуре, будет ежесекундно генерировать события для 10 устройств до тех пор, пока вы не остановите приложение, нажав на клавишу.

1. В Visual Studio создайте новый проект **Рабочий стол Windows** и выберите шаблон проекта **Консольное приложение**. Присвойте проекту имя **SendEvents** и нажмите кнопку **OK**.

2. В **обозревателе решений** щелкните правой кнопкой мыши **SendEvents** и выберите **управление пакетами NuGet**.

3. В разделе **Управление пакетами NuGet** найдите и установите следующие пакеты.

	* **Microsoft Azure Service Bus**
	* **JSON.Net**

	После установки пакетов нажмите **Закрыть** диспетчер пакетов.

4. Замените содержимое **Program.cs** следующим.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using Newtonsoft.Json;
		using Microsoft.ServiceBus;
		using System.Threading;

		namespace SendEvents
		{
		    class Program
		    {

		        static int numberOfDevices = 10;
		        static string eventHubName = "temperature";
		        static string eventHubNamespace = "namespace";
		        static string sharedAccessPolicyName = "devices";
		        static string sharedAccessPolicyKey = "key for devices policy";

		        static void Main(string[] args)
		        {
		            var settings = new MessagingFactorySettings()
		            {
		                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
		                TransportType = TransportType.Amqp
		            };
		            var factory = MessagingFactory.Create(
		                 ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

		            EventHubClient client = factory.CreateEventHubClient(eventHubName);

		            try
		            {

		                List<Task> tasks = new List<Task>();
		                // Send messages to Event Hub
		                Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
		                Random random = new Random();
		                //for (int i = 0; i < numberOfMessages; ++i)
		                while(!Console.KeyAvailable)
		                {
		                    // One event per device
		                    for(int devices = 0; devices < numberOfDevices; devices++)
		                    {
		                        // Create the device/temperature metric
		                        Event info = new Event() {
		                            TimeStamp = DateTime.UtcNow,
		                            DeviceId = random.Next(numberOfDevices),
		                            Temperature = random.Next(100)
		                        };
		                        // Serialize to JSON
		                        var serializedString = JsonConvert.SerializeObject(info);
		                        Console.WriteLine(serializedString);
		                        EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
		                        {
		                            PartitionKey = info.DeviceId.ToString()
		                        };

		                        // Send the metric to Event Hub
		                        tasks.Add(client.SendAsync(data));
		                    }
		                    // Sleep a second
		                    Thread.Sleep(1000);
		                };

		                Task.WaitAll(tasks.ToArray());
		            }
		            catch (Exception exp)
		            {
		                Console.WriteLine("Error on send: " + exp.Message);
		            }

		        }
		    }
		}

	Пока вы будете получать предупреждения о линиях, ссылающихся на класс событий. Игнорируйте их.

5. В файле **Program.cs** установите значение следующих переменных в начале файла в соответствии со значениями, полученными из вашего концентратора событий на портале управления Azure.

	<table>
	<tr><th>Задать в качестве значения...</th><th>Следующее...</th></tr>
	<tr><td>eventHubName</td><td>Имя вашего концентратора событий. Например: <strong>temperature</strong>.</td></tr>
	<tr><td>eventHubNamespace</td><td>Пространство имен вашего концентратора событий. Например: <strong>sensors-ns</strong>.</td></tr>
	<tr><td>sharedAccessPolicyName</td><td>Созданная вами политика доступа к отправке. Например: <strong>устройства</strong>.</td></tr>
	<tr><td>sharedAccessPolicyKey</td><td>Ключ для политики доступа к отправке.</td></tr>
	</table>

6. В **Обозревателе решений** щелкните правой кнопкой мыши **SendEvents** и **Добавить | Класс**. Присвойте новому классу имя **Event.cs**. Это послужит описанием для сообщения, передаваемого Event Hub.

7. Замените содержимое **Event.cs** следующим.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Runtime.Serialization;
		using System.Text;
		using System.Threading.Tasks;

		namespace SendEvents
		{
		    [DataContract]
		    public class Event
		    {
		    	[DataMember]
		    	public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public int DeviceId { get; set; }
		        [DataMember]
		        public int Temperature { get; set; }
		    }
		}

	Этот класс описывает отправляемые нами данные - TimeStamp (метка времени), DeviceID (код устройства) и Temperature value (значение температуры).

8. Выполните команду **Сохранить все**, затем запустите приложение для заполнения концентратора событий сообщениями.

## Создание виртуальной сети Azure

Для того чтобы выполняемая на кластере топология Storm взаимодействовала непосредственно с HBase, вы должны подготовить оба сервера для ввода в виртуальную сеть Azure.

1. Войдите на [портал управления Azure][azure-portal].

2. В нижней части страницы щелкните **+СОЗДАТЬ**, выберите **Сетевые службы**, щелкните **Виртуальная сеть** и **Быстрое создание**.

3. Введите или выберите следующие значения:

	- **Имя**: Название виртуальной сети.
	- **Адресное пространство**: Выберите адресное пространство виртуальной сети таким образом, чтобы оно было достаточно большим для обеспечения адресов для всех узлов в кластере. В противном случае подготовка завершится с ошибкой.
	- **Максимальное количество виртуальных машин**: Выберите максимальное количество виртуальных машин.
	- **Расположение**: Расположение должно совпадать с расположением кластера HBase, который вы будете создавать.
	- **DNS-сервер**: В этой статье используется внутренний сервер DNS, предоставленный Azure, поэтому  можно выбрать **Нет**. Также поддерживаются более продвинутые сетевые конфигурации с другими серверами DNS. Подробное руководство см. по адресу [http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Щелкните **Создать виртуальную сеть**. В списке появится имя новой виртуальной сети. Подождите, пока в столбце "Состояние" не появится значение **Создано**.

5. В основной области щелкните только что созданную виртуальную сеть.

6. В верхней части страницы щелкните **ПАНЕЛЬ МОНИТОРИНГА**.

7. В разделе **Сводка** узнайте и запишите **ИДЕНТИФИКАТОР ВИРТУАЛЬНОЙ СЕТИ**. Он вам понадобится в процессе подготовки кластеров Storm и HBase.

8. В верхней части страницы щелкните **НАСТРОИТЬ**.

9. В нижней части страницы указано имя подсети по умолчанию **Subnet-1**. Используйте кнопку **Добавить подсеть**, чтобы добавить **Subnet-2**. Эти подсети будут содержать кластеры Storm и HBase

	> [AZURE.NOTE] В этой статье используются кластеры с одним узлом. Если создаются кластеры с несколькими узлами, то необходимо проверить параметр **CIDR(ЧИСЛО АДРЕСОВ)** для подсети, которая будет использоваться с кластером. Количество адресов должен быть больше, чем количество рабочих узлов плюс семь (шлюзов: 2, головной узел: 2, узел Zookeeper: 3). Например, если вам нужен кластер HBase на 10 узлов, количество адресов для подсети должно быть больше 17 (10 + 7). В противном случае подготовка завершится с ошибкой.
	>
	> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

11. В нижней части страницы щелкните **Сохранить**.

## Создание кластера Storm на HDInsight

1. Войдите на [портал управления Azure][azure-portal]

2. Щелкните **HDInsight** слева, затем **+СОЗДАТЬ** в нижнем левом углу страницы.

3. Щелкните значок HDInsight во втором столбце, а затем выберите **Настроить**.

4. На странице **Сведения о кластере** введите имя кластера и выберите значение **Storm** для параметра **Тип кластера**. Щелкните стрелку для продолжения.

5. В качестве значения количества **Узлов данных**, используемых в этом кластере, введите 1. В качестве параметра **Регион/Виртуальная сеть** выберите созданную ранее виртуальную сеть Azure. Для **Подсети виртуальной сети** выберите **Subnet-2**.

	> [AZURE.NOTE] Для сокращения затрат на кластер, приведенный в этой статье, уменьшите **Размер кластера** до 1 и удалите кластер после того, как завершите им пользоваться.

6. Введите **Имя пользователя** администратора и **Пароль**, затем выберите стрелку, чтобы продолжить.

7. Для **Учетной записи хранения** выберите **Создать новое хранилище** или укажите существующую запись хранения Выберите или введите **Имя учетной записи** и **Контейнер по умолчанию** для использования. Щелкните значок галочки слева внизу для создания кластера Storm.

## Создание кластера HDInsight HBase

1. Войдите на [портал управления Azure][azure-portal]

2. Щелкните **HDInsight** слева, затем **+СОЗДАТЬ** в нижнем левом углу страницы.

3. Щелкните значок HDInsight во втором столбце, а затем выберите **Настроить**.

4. На странице **Сведения о кластере** введите имя кластера и выберите **HBase** для **Типа кластера**. Щелкните стрелку для продолжения.

5. В качестве значения количества **Узлов данных** используемых в этом кластере, введите 1. В качестве параметра **Регион/Виртуальная сеть** выберите созданную ранее виртуальную сеть Azure. Для **Подсети виртуальной сети** выберите **Subnet-1**.

	> [AZURE.NOTE] Для сокращения затрат на кластер, приведенный в этой статье, уменьшите **Размер кластера** до 1 и удалите кластер после того, как завершите им пользоваться.

6. Введите **Имя пользователя** администратора и **Пароль**, затем выберите стрелку, чтобы продолжить.

7. Для **Учетной записи хранения** выберите **Создать новое хранилище** или укажите существующую запись хранения Выберите или введите **Имя учетной записи** и **Контейнер по умолчанию** для использования. Щелкните значок галочки слева внизу для создания кластера Storm.

	> [AZURE.NOTE] Вам следует использовать контейнер, который не занят под кластер Storm.

### Включение удаленного рабочего стола.

В этом учебнике для получения доступа к кластерам Storm и HBase мы должны использовать удаленный рабочий стол. Чтобы актировать удаленный рабочий стол для обоих кластеров, сделайте следующее.

1. Войдите на [портал управления Azure][azure-portal].

2. Слева выберите **HDInsight**, затем выберите из списка ваш кластер Storm. После этого в верхней части страницы щелкните **Настроить**.

3. В нижней части страницы щелкните **Включить удаленный доступ**. При появлении запроса введите имя пользователя, пароль и дату истечения доступа к удаленному рабочему столу. Щелкните флажок, чтобы включить удаленный рабочий стол.

После включения удаленного рабочего стола вы можете выбрать **Подключиться** в нижней части страницы. Следуйте инструкциям для подключения к кластеру.

### Обнаружение DNS-суффикса HBase

Для записи из кластера Storm в HBase вы должны использовать полное доменное имя (FQDN) кластера HBase. Чтобы получить эту информацию, сделайте следующее.

1. Подключитесь к кластеру HBase через удаленный рабочий стол.

2. После подключения к кластеру откройте командную строку Hadoop и выполните команду **ipconfig** для получения DNS-суффикса. **Индивидуальный для каждого подключения DNS-суффикс** будет содержать значение суффикса. Например, **mycluster.b4.internal.cloudapp.net**. Сохраните эти сведения.

## Разработка топологии Storm

> [AZURE.NOTE] Действия, указанные в этом разделе, должны выполняться в вашей локальной среде разработки.

### Загрузка и построение внешних зависимостей

Ряд зависимостей, используемых в этом проекте, необходимо загрузить и построить индивидуально, а затем установить в локальный репозиторий Maven в вашей среде разработки. В этом разделе вы загрузите и установите следующие компоненты.

* "Воронка" - это компонент, читающий сообщения концентратора событий.

* SDK Java-клиента SignalR

#### Загрузка и сборка "воронки" (spout) концентратора Event Hub

Для получения данных с концентратора событий мы воспользуемся **eventhubs-storm-spout**.

1. Используйте удаленный рабочий стол для подключения к вашему кластеру Storm, затем скопируйте файл **%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** в вашу локальную среду разработки. Он содержит пакет **events-storm-spout**.

2. Для установки пакета в локальный репозиторий Maven выполните следующую команду. Это позволит нам в дальнейшем легко добавлять его в качестве ссылки в проект Storm.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Загрузка и установка клиента SignalR

Для отправки сообщений в панели мониторинга ASP.NET, используйте [SignalR клиентского пакета SDK для Java](https://github.com/SignalR/java-client).

1. Откройте окно командной строки.

2. Перейдите в каталоги, куда вы хотите загрузить проект SDK клиента SignalR

3. Для загрузки проекта с GitHub выполните следующую команду.

	клон git https://github.com/SignalR/java-client

4. Измените каталог на **java-client\signalr-client-sdk** и скомпилируйте проект в файл JAR, выполнив следующие команды.

		cd java-client\signalr-client-sdk
		mvn package

	> [AZURE.NOTE] Если вы получаете сообщение о том, что зависимость **gson** не может быть загружена, удалите следующие строки из файла **java-client\signalr-client-sdk\pom.xml**.
	> 
<repository>
<id>central</id>
<name>Central</name>
<url>http://maven.eclipse.org/build</url>
</repository>
</repositories>

	> Удаление этих строк приведет к тому, что Maven запросит файл из репозитория (действие по умолчанию). Для повторного обращения Maven к репозиторию выполните команду `- U`. Например, `mvn package -U`

5. Для установки пакета в локальный репозиторий Maven выполните следующую команду. Это позволит нам в дальнейшем легко добавлять его в качестве ссылки в проект Storm.

		mvn install:install-file -Dfile=target/signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Создание шаблонов (скаффолдинг) проекта топологии Storm

После установки "воронки" (spout) концентратора Event Hub и клиента SignalR в локальный репозиторий воспользуйтесь Maven и создайте шаблоны для топологии проекта Storm.

1. Откройте командную строку, сеансы Bash, Terminal или любой тип команд, используемый на вашей системе.

2. Перейдите в каталог, где будут размещаться ваши проекты. Например, в каталог, куда вы устанавливаете все ваши проекты с кодами.

3. Используйте следующие команды Maven для создания основных шаблонов для ваших приложений.

		mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

	Эта команда...

	* Создаст новый каталог, используя указанный *artifactId*. В этом случае - **Temperature**.
	* Создаст файл **pom.xml**, который содержит информацию Maven для проекта.
	* Создаст структуру каталога **src**, которая содержит некоторые основные исходные коды и тесты.

### Добавление зависимостей и подключаемых модулей

Затем измените **pom.xml** так, чтобы файл содержал ссылки на зависимости проекта, а также на подключаемые модули Maven для использования при сборке и упаковке.

1. В текстовом редакторе откройте файл **pom.xml** и добавьте следующее в раздел **&lt;dependencies>**. Их можно добавить в конце данного раздела, после зависимостей для junit.

		<dependency>
	      <groupId>org.apache.storm</groupId>
	      <artifactId>storm-core</artifactId>
	      <version>0.9.2-incubating</version>
	      <!-- keep storm out of the jar-with-dependencies -->
	      <scope>provided</scope>
	    </dependency>
	    <dependency>
	      <groupId>microsoft.aspnet.signalr</groupId>
	      <artifactId>signalr-client-sdk</artifactId>
	      <version>1.0</version>
	    </dependency>
	    <dependency>
	      <groupId>com.microsoft.eventhubs</groupId>
	      <artifactId>eventhubs-storm-spout</artifactId>
	      <version>0.9</version>
	    </dependency>
	    <dependency>
	      <groupId>com.google.code.gson</groupId>
	      <artifactId>gson</artifactId>
	      <version>2.2.2</version>
	    </dependency>
		<dependency>
      	  <groupId>com.github.ptgoetz</groupId>
      	  <artifactId>storm-hbase</artifactId>
      	  <version>0.1.2</version>
    	</dependency>
	    <dependency>
	      <groupId>com.netflix.curator</groupId>
	      <artifactId>curator-framework</artifactId>
	      <version>1.3.3</version>
	      <exclusions>
	        <exclusion>
	          <groupId>log4j</groupId>
	            <artifactId>log4j</artifactId>
	          </exclusion>
	        <exclusion>
	          <groupId>org.slf4j</groupId>
	            <artifactId>slf4j-log4j12</artifactId>
	        </exclusion>
	      </exclusions>
	      <scope>provided</scope>
	    </dependency>

	Это добавляет зависимости для...

	* eventhubs-storm-spout - spout-компонент концентратора событий
	* signalr-client-sdk - клиент SignalR
	* gson - это зависимость клиента SignalR, может также использоваться для создания JSON при записи в SignalR
	* storm-core - обеспечивает основную функциональность приложений Storm
	* slf4j - обеспечивает возможность входа в систему и используется eventhubs-storm-spout
	* curator-framework - используется eventhubs-storm-spout
	* storm-core - основные классы для Storm
	* storm-hbase - классы, позволяющие записывать данные в HBase

	> [AZURE.NOTE] Заметьте, что некоторые зависимости отмечены областью **предоставлены** для указания на то, что их следует загрузить из репозитория Maven и использовать для построения и тестирования вашего приложения на локальном ресурсе, но что они будут также доступны в вашей среде выполнения, их не нужно компилировать и включать в JAR, созданный этим проектом.

2. В конце файла **pom.xml** непосредственно перед записью **&lt;/project>** добавьте следующее.

		  <build>
		    <plugins>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-compiler-plugin</artifactId>
		        <version>2.3.2</version>
		        <configuration>
		          <source>1.7</source>
		          <target>1.7</target>
		        </configuration>
		      </plugin>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-shade-plugin</artifactId>
		        <version>2.3</version>
		        <configuration>
		          <transformers>
		            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		            </transformer>
		          </transformers>
		        </configuration>
		        <executions>
		          <execution>
		            <phase>package</phase>
		            <goals>
		              <goal>shade</goal>
		            </goals>
		          </execution>
		        </executions>
		      </plugin>
		      <plugin>
		        <groupId>org.codehaus.mojo</groupId>
		        <artifactId>exec-maven-plugin</artifactId>
		        <version>1.2.1</version>
		        <executions>
		          <execution>
		          <goals>
		            <goal>exec</goal>
		          </goals>
		          </execution>
		        </executions>
		        <configuration>
		          <executable>java</executable>
		          <includeProjectDependencies>true</includeProjectDependencies>
		          <includePluginDependencies>false</includePluginDependencies>
		          <classpathScope>compile</classpathScope>
		          <mainClass>${storm.topology}</mainClass>
		        </configuration>
		      </plugin>
		    </plugins>
		    <resources>
		      <resource>
		        <directory>${basedir}/conf</directory>
		        <filtering>false</filtering>
		        <includes>
		          <include>Config.properties</include>
				  <include>hbase-site.xml</include>
		        </includes>
		      </resource>
		    </resources>
		  </build>

	Это укажет Maven, что делать при построении проекта:

	* Включите файл ресурсов **/conf/Config.properties**. Этот файл будет создан позже, но будет содержать данные о настройках для подключения к концентратору Event Hub в Azure.
	* Включите файл ресурсов **/conf/hbase-site.xml**. Этот файл будет создан позже, но будет содержать информацию о том, как подключиться к HBase.
	* Используйте **maven-compiler-plugin** для компиляции приложения.
	* Используйте **maven-shade-plugin** для создания uberjar или fat jar, который содержит проект и все необходимые зависимости.
	* Используйте **exec-maven-plugin**, который позволяет запускать приложение локально без кластера Hadoop.

### Добавление файлов конфигурации

**eventhubs-storm-spout** считывает информацию из файла **Config.properties**. В нем содержатся сведения о том, к чему подключать концентратор Event Hub. Поскольку вы можете указать файл конфигурации, когда запускаете топологию на кластере, включение одного из них в проект обеспечивает известную конфигурацию по умолчанию.

1. В каталоге **TemperatureMonitor** создайте новый каталог с именем **conf**.

2. В каталоге **conf** создайте два новых файла:

	* **Config.properties** - содержит настройки концентратора событий
	* **hbase-site.xml** - содержит настройки для подключения к hbase

3. Измените содержимое файла **Config.properties** следующим образом.

		eventhubspout.username = storm

		eventhubspout.password = <the key of the 'storm' policy>

		eventhubspout.namespace = <the event hub namespace>

		eventhubspout.entitypath = <the event hub name>

		eventhubspout.partitions.count = <the number of partitions for the event hub>

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 1024

	Замените **password** ключом для политики **storm**, созданным ранее на концентраторе событий.
	
	Замените **namespace** пространством имен вашего концентратора событий.
	
	Замените **entitpath** именем вашего концентратора событий.

4. Измените содержимое файла **hbase-site.xml** следующим образом.

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

5. В файле **hbase-site.xml** замените значение суффикса (**suffix**) данных zookeeper DNS-суффиксом, полученным вами ранее для HBase. Например, **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6. Сохраните файлы.

### Добавление вспомогательных приложений

Для поддержки сериализации в/из JSON нам необходимы несколько модулей поддержки, которые определили бы структуру объекта.

1. В каталоге **\temperaturemonitor\src\main\java\com\microsoft\examples** создайте новый каталог с именем **helpers**.

2. В каталоге **helpers** создайте два новых файла:

	* **EventHubMessage.java** - определяет формат сообщения в концентраторе событий

	* **SignalRMessage.java** - определяет формат сообщения, отправленного в SignalR

3. Вставьте в файл **EventHubMessage.java** следующее содержимое.

		package com.microsoft.examples;

		public class EventHubMessage {
		  String TimeStamp;
		  int DeviceId;
		  int Temperature;
		}

4. Вставьте в файл **SignalRMessage.java** следующее содержимое.

		package com.microsoft.examples;

		public class SignalRMessage {
		  int device;
		  int temperature;
		}

5. Сохраните и закройте эти файлы.

### Добавление компонентов "сито" (Bolt)

Компоненты "сито" (bolt) выполняют основную обработку данных в топологии. В данной топологии существует три компонента "сито" (bolt), один из которых - hbase, который автоматически загружается в процессе построения.

1. В каталоге **\temperaturemonitor\src\main\java\com\microsoft\examples** создайте новый каталог с именем **bolts**.

2. В каталоге **bolts** создайте два новых файла:

	* **ParserBolt.java** - выполняет синтаксический разбор входящих сообщений от концентратора событий на отдельные поля, затем генерирует два потока
	* **DashboardBolt.java** - записывает информацию в панель мониторинга через SignalR

3. Вставьте в файл **ParserBolt.java** следующее содержимое.

		package com.microsoft.examples;

		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.tuple.Fields;
		import backtype.storm.tuple.Values;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		public class ParserBolt extends BaseBasicBolt {

		  //Declare output fields & streams
		  //hbasestream is all fields, and goes to hbase
		  //dashstream is just the device and temperature, and goes to the dashboard
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
		    declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    //Should only be one tuple, which is the JSON message from the spout
		    String value = tuple.getString(0);

			//Deal with cases where we get multiple
			//EventHub messages in one tuple
			String[] arr = value.split("}");
			for (String ehm : arr)
			{

			    //Convert it from JSON to an object
				EventHubMessage msg = new Gson().fromJson(ehm.concat("}"),EventHubMessage.class);

			    //Pull out the values and emit as a stream
			    String timestamp = msg.TimeStamp;
			    int deviceid = msg.DeviceId;
			    int temperature = msg.Temperature;
			    collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
			    collector.emit("dashstream", new Values(deviceid, temperature));
			}
		  }
		}

4. Вставьте в файл **DashboardBolt.java** следующее содержимое.

		package com.microsoft.examples;

		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.Config;
		import backtype.storm.Constants;

		import microsoft.aspnet.signalr.client.Action;
		import microsoft.aspnet.signalr.client.ErrorCallback;
		import microsoft.aspnet.signalr.client.LogLevel;
		import microsoft.aspnet.signalr.client.Logger;
		import microsoft.aspnet.signalr.client.MessageReceivedHandler;
		import microsoft.aspnet.signalr.client.hubs.HubConnection;
		import microsoft.aspnet.signalr.client.hubs.HubProxy;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		import java.util.Map;

		public class DashboardBolt extends BaseBasicBolt {
		  //Connection and proxy for SignalR hub
		  private HubConnection conn;
		  private HubProxy proxy;

		  //Declare output fields
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    //no stream output - we talk directly to SignalR
		  }

		  @Override
		  public void prepare(Map config, TopologyContext context) {

		    // Connect to the DashHub SignalR server
		    conn = new HubConnection("http://dashboard.azurewebsites.net/");
		    // Create the hub proxy
		    proxy = conn.createHubProxy("DashHub");
		    // Subscribe to the error event
		    conn.error(new ErrorCallback() {
		      @Override
		      public void onError(Throwable error) {
		        error.printStackTrace();
		      }
		    });
		    // Subscribe to the connected event
		    conn.connected(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("CONNECTED");
		      }
		    });
		    // Subscribe to the closed event
		    conn.closed(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("DISCONNECTED");
		      }
		    });
		    // Start the connection
		    conn.start()
		      .done(new Action<Void>() {
		        @Override
		        public void run(Void obj) throws Exception {
		          System.out.println("Done Connecting!");
		        }
		    });
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    try {
		      //Get the deviceid and temperature by field name
		      int deviceid = tuple.getIntegerByField("deviceid");
		      int temperature = tuple.getIntegerByField("temperature");
		      //Construct the SignalR message
		      SignalRMessage srMessage = new SignalRMessage();
		      srMessage.device = deviceid;
		      srMessage.temperature = temperature;
		      // send it as JSON
		      proxy.invoke("send", gson.toJson(srMessage));
		    } catch (Exception e) {
		       // LOG.error("Bolt execute error: {}", e);
		       collector.reportError(e);
		    }
		  }
		}

	Замените `http://dashboard.azurewebsites.net/` адресом веб-сайта Azure, опубликованном вами в панели мониторинга ранее. Например, http://mydashboard.azurewebsites.net.

5. Сохраните и закройте файлы.

### Определение топологии

Топология описывает поток данных в топологии между компонентами "воронка" (spout) и "сито" (bolt), а также уровень параллелизма для топологии и ее компонентов.

1. В каталоге **\temperaturemonitor\src\main\java\com\microsoft\examples** создайте новый файл с именем **Temperature.java**.

2. Откройте файл **Temperature.java** и вставьте в него следующее содержимое.

		package com.microsoft.examples;

		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import backtype.storm.tuple.Fields;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		import java.io.FileReader;
		import java.util.Properties;

		//hbase
		import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
		import org.apache.storm.hbase.bolt.HBaseBolt;
		import java.util.Map;
		import java.util.HashMap;
		import backtype.storm.tuple.Fields;

		public class Temperature
		{
		  protected EventHubSpoutConfig spoutConfig;
		  protected int numWorkers;

		  // Reads the configuration information for the Event Hub spout
		  protected void readEHConfig(String[] args) throws Exception {
		    Properties properties = new Properties();
		    if(args.length > 1) {
		      properties.load(new FileReader(args[1]));
		    }
		    else {
		      properties.load(Temperature.class.getClassLoader().getResourceAsStream(
		        "Config.properties"));
		    }

		    String username = properties.getProperty("eventhubspout.username");
		    String password = properties.getProperty("eventhubspout.password");
		    String namespaceName = properties.getProperty("eventhubspout.namespace");
		    String entityPath = properties.getProperty("eventhubspout.entitypath");
		    String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
		    int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
		    int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
		    int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
		    System.out.println("Eventhub spout config: ");
		    System.out.println("  partition count: " + partitionCount);
		    System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
		    System.out.println("  receiver credits: " + receiverCredits);
		    spoutConfig = new EventHubSpoutConfig(username, password,
		      namespaceName, entityPath, partitionCount, zkEndpointAddress,
		      checkpointIntervalInSeconds, receiverCredits);

		    //set the number of workers to be the same as partition number.
		    //the idea is to have a spout and a partial count bolt co-exist in one
		    //worker to avoid shuffling messages across workers in storm cluster.
		    numWorkers = spoutConfig.getPartitionCount();

		    if(args.length > 0) {
		      //set topology name so that sample Trident topology can use it as stream name.
		      spoutConfig.setTopologyName(args[0]);
		    }
		  }

		  // Create the spout using the configuration
		  protected EventHubSpout createEventHubSpout() {
		    EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
		    return eventHubSpout;
		  }

		  // Build the topology
		  protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
		    TopologyBuilder topologyBuilder = new TopologyBuilder();
		    // Name the spout 'EventHubsSpout', and set it to create
		    // as many as we have partition counts in the config file
		    topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
		      .setNumTasks(spoutConfig.getPartitionCount());
		    // Create the parser bolt, which subscribes to the stream from EventHub
		    topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
		      .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
		    // Create the dashboard bolt, which subscribes to the stream from Parser
		    topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
		      .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    // Create the HBase bolt, which subscribes to the stream from Parser
		    // WARNING - uncomment the following two lines when deploying
			// leave commented when testing locally
			// topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
		    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    return topologyBuilder.createTopology();
		  }

		  protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
		    // Config config = new Config();
		    config.setDebug(false);

		    //Enable metrics
		    config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

		    // Is this running locally, or on an HDInsight cluster?
		    if (args != null && args.length > 0) {
		      config.setNumWorkers(numWorkers);
		      StormSubmitter.submitTopology(args[0], config, topology);
		    } else {
		      config.setMaxTaskParallelism(2);

		      LocalCluster localCluster = new LocalCluster();
		      localCluster.submitTopology("test", config, topology);

		      Thread.sleep(5000000);

		      localCluster.shutdown();
		    }
		  }

		  // Loads the configuration, creates the spout, builds the topology,
		  // and then submits it
		  protected void runScenario(String[] args) throws Exception{
		    readEHConfig(args);
		    Config config = new Config();

		    //hbase configuration
		    Map<String, Object> hbConf = new HashMap<String, Object>();
		    if(args.length > 0) {
		      hbConf.put("hbase.rootdir", args[0]);
		    }
		    config.put("hbase.conf", hbConf);
		    SimpleHBaseMapper mapper = new SimpleHBaseMapper()
		          .withRowKeyField("deviceid")
		          .withColumnFields(new Fields("timestamp", "temperature"))
		          .withColumnFamily("cf");

		    EventHubSpout eventHubSpout = createEventHubSpout();
		    StormTopology topology = buildTopology(eventHubSpout, mapper);
		    submitTopology(args, topology, config);
		  }

		  public static void main(String[] args) throws Exception {
		    Temperature scenario = new Temperature();
		    scenario.runScenario(args);
		  }
		}

	> [AZURE.NOTE] Обратите внимание, что строки **HBaseBolt** закомментированы. Это происходит потому, что следующим шагом является локальное выполнение топологии. Так как HBaseBolt обменивается данными непосредственно с HBase, если он включен, то начнут поступать сообщения об ошибках. Этого не произойдет, если вы настроили виртуальную сеть с DNS-сервером и подключили ваш локальный компьютер к виртуальной сети.

### Локальная проверка топологии

Для компилирования и проверки файла на компьютере разработчика выполните следующие действия.

1. Запустите приложение .NET **SendEvent**, чтобы начать посылать события и дать возможность прочитать какие-либо данные с концентратора событий.

2. Откройте веб-браузер с панелью мониторинга, которую вы ранее развернули на веб-сайте Azure. Это позволит вам увидеть, как график рисует значения по мере их прохождения через топологию.

3. Запустите топологию локально, выполнив следующую команду

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Эта команда запустит топологию, прочтет файлы с концентратора Event Hub и отправит их в панель мониторинга, функционирующую на Azure Websites. На панели мониторинга появятся линии.

4. Когда вы убедитесь, что топология функционирует, остановите ее, нажав Ctrl-C. Для остановки приложения SendEvent выберите окно и нажмите любую клавишу.

### Включение HBaseBolt и подготовка HBase

1. Откройте файл **Temperature.java** и удалите комментарий (//) из следующих строк:

		//topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	//  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Компонент "bolt" HBase будет активирован.

2. Сохраните **Temperature.java**.

3. С помощью удаленного рабочего стола подключитесь к кластеру HBase.

4. С рабочего стола запустите командную строку HDInsight и введите следующие команды.

		cd %hbase_home%
		bin\hbase shell

5. Из оболочки HBase введите следующие команды, чтобы создать таблицы для сохранения в них данных датчиков.

		create 'SensorData', 'cf'

6. Проверьте таблицу на отсутствие данных, введя следующую команду.

		scan 'SensorData'

Пока оставьте этот запрос открытым в оболочке HBase.

## Пакетирование и развертывание топологии HDInsight

Чтобы запустить топологию Temperature в кластере HDInsight Storm, в среде разработки выполните следующие действия.  Кластер Storm.

1. Выполните следующую команду, чтобы создать файл JAR из вашего проекта.

		mvn package

	Будет создан файл с именем **TemperatureMonitor-1.0-SNAPSHOT.jar** в папке **target** вашего проекта.

2. На локальном компьютере запустите приложение .NET **SendEvents**, чтобы дать возможность прочитать какие-либо события.

3. Подключитесь к вашему кластеру HDInsight Storm с помощью удаленного рабочего стола и скопируйте файл **TemperatureMonitor-1.0-SNAPSHOT.jar** в каталог **c:\apps\dist\storm&lt;номер_версии>**.

4. Используйте значок **Командная строка HDInsight** на рабочем столе, чтобы открыть новую командную строку, а также запустите топологию с помощью следующих команд.

		cd %storm_home%
		bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5. После запуска топологии перед появлением данных на веб-панели мониторинга пройдет несколько секунд.

6. После появления на панели мониторинга данных переключитесь на сеанс удаленного рабочего стола в кластере HBase.

7. Из оболочки HBase введите следующую команду.

		scan 'SensorData'

	Обратите внимание, что появятся строки с данными, написанными топологией Storm.

8. Для остановки топологии перейдите к сеансу удаленного рабочего стола с кластером Storm и введите в командную строку HDInsight следующее.

		bin\storm kill Temperature

	Через несколько секунд топология прекратит работу.

## Сводка

Теперь вы узнали, как использовать Storm для чтения данных с Event Hub, сохранения данных в HBase и демонстрации информации из Storm на внешних панелях мониторинга с помощью SignalR и D3.js.

* Дополнительную информацию об Apache Storm см. по адресу [https://storm.incubator.apache.org/](https://storm.incubator.apache.org/)

* Дополнительную информацию о HBase с HDInsight см. в разделе [Обзор HBase с HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-hbase-overview/)

* Дополнительную информацию о SignalR см. в разделе [ASP.NET SignalR](http://signalr.net/)

* Дополнительную информацию о D3.js см. в статье [D3.js - документы, управляемые данными](http://d3js.org/)

* Информацию о создании топологий в .NET см. разделе [Разработка приложений для обработки потоковой передачи данных на SCP.NET и C# в Storm и HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[azure-portal]: https://manage.windowsazure.com/

<!--HONumber=42-->

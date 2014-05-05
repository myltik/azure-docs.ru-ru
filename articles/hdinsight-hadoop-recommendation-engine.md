<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Подсистема рекомендаций Hadoop" pageTitle="Подсистема рекомендаций Hadoop (.NET) | Azure" metaKeywords="Azure Apache Mahout, пример рекомендаций Azure, учебник рекомендаций Azure, подсистема рекомендаций Azure" description="Учебник, в котором объясняется, как использовать подсистему рекомендаций Apache Mahout с Azure для создания предложений по композициям на основе слушательских привычек." disqusComments="1" umbracoNaviHide="1" title="Простая подсистема рекомендаций с использованием Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />



# Простая подсистема рекомендаций с использованием Apache Mahout

Apache Mahout™ — это библиотека машинного обучения, созданная для масштабируемых приложений машинного обучения. Подсистемы рекомендаций на сегодняшний день являются одним из самых популярных типов приложений машинного обучения и располагают множеством наглядных вариантов применения в сфере маркетинга.

Apache Mahout предоставляет встроенную реализацию для совместной (коллаборативной) фильтрации на основе элементов. Такой подход широко применяется для проведения интеллектуального анализа данных для создания рекомендаций. Совместная фильтрация на основе элементов была разработана компанией Amazon.com. Идея заключается в том, что на основании данных о предпочтениях пользователя, отражающих взаимосвязи между предпочитаемыми элементами, можно определить вкусы будущих пользователей из аналогичной группы.

При работе с этим учебником вы воспользуетесь сайтом [Million Song Dataset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) и загрузите [набор данных](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip), чтобы создать рекомендации по композициям для пользователей на основе того, что они предпочитали слушать прежде.



Вы узнаете следующее:

* Как использовать подсистемы рекомендаций

Этот учебник состоит из следующих сегментов:

1. [Установка и настройка](#setup)
2. [Проверка и форматирование данных](#segment1)
3. [Установка Mahout](#Segment2)
4. [Выполнение задания Mahout](#segment2)

## <a name="setup"></a>Установка и настройка 

В этом учебнике предполагается, что вы получили установленный экземпляр с Azure и предварительной версией HDInsight и создали кластер HDInsight, в котором можно запустить пример. Если вы еще не сделали этого, обратитесь к учебнику [Приступая к работе с Azure HDInsight](/ru-ru/manage/services/hdinsight/get-started-hdinsight/), который содержит инструкции по обеспечению соответствия предварительным требованиям.

## <a name="segment1"></a>Проверка и форматирование данных 

Этот пример касается способа, которым пользователи выражают предпочтение для определенных композиций. Предполагается, что пользовательское предпочтение определяется количеством раз, когда пользователь слушает композицию. Образцы, обнаруженные среди данных о предпочтениях, можно использовать для прогнозирования будущих пользовательских предпочтений на основе некоторых выраженных ими музыкальных предпочтений. Пример такого набора данных можно посмотреть в разделе **Description** (Описание) веб-страницы [Echo Nest Taste Profile Subset](http://labrosa.ee.columbia.edu/millionsong/tasteprofile):

![Echo Nest Taste Profile Subset][echo-nest]

###Пример данных из набора данных Million Song Dataset

Для использования этого набора данных с Mahout необходимо выполнить два действия:

1.	Преобразовать идентификаторы композиций и пользователей в целые значения.
2.	Сохранить новые значения с их рейтингами в файл с разделителями-запятыми.

Если у вас не установлена среда Visual Studio 2010, пропустите этот шаг и перейдите к разделу "Выполнение задания Mahout", чтобы получить предварительно созданную версию.

Начните с запуска Visual Studio 2010. В Visual Studio выберите **Файл -> Создать -> Проект**. На панели **Установленные шаблоны** в узле **Visual C#** выберите категорию **Окно**, а затем выберите из списка **Консольное приложение**. Назовите проект "ConvertToMahoutInput" и нажмите кнопку **ОК**.

![Создание консольного приложения][create-console-app]

###Создание консольного приложения

1. После создания приложения откройте файл **Program.cs** и добавьте в класс **Program** следующие статические элементы:


		const char tab = '\u0009';
		static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
		static Dictionary<string, int> songMapping = new Dictionary<string, int>();	


2. Теперь добавьте инструкцию `using System.IO;` и заполните метод **Main**, используя следующий код:

		var inputStream = File.Open(args[0], FileMode.Open);
		var reader = new StreamReader(inputStream);

		var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
		var writer = new StreamWriter(outStream);

		var i = 1;

		var line = reader.ReadLine();
		while (!string.IsNullOrWhiteSpace(line))
		{
    		i++;
    		if (i > 5000)
			break;
    		var outLine = line.Split(tab);

    		int user = GetUser(outLine[0]);
    		int song = GetSong(outLine[1]);

    		writer.Write(user);
	    	writer.Write(',');
	    	writer.Write(song);
	   	 	writer.Write(',');
	   	 	writer.WriteLine(outLine[2]);

    		line = reader.ReadLine();
		}

		Console.WriteLine("saved {0} lines to {1}", i, args[0]);
	
		reader.Close();
		writer.Close();
	
		SaveMapping(usersMapping, "users.csv");
		SaveMapping(songMapping, "mInput.csv");
	
		Console.WriteLine("Mapping saved");
		Console.ReadKey();


3. Создайте функции **GetUser** и **GetSong**, которые преобразуют идентификаторы в целые числа.

		static int GetUser(string user)
		{
    		if (!usersMapping.ContainsKey(user))
        		usersMapping.Add(user, usersMapping.Count + 1);

    		return usersMapping[user];
		}

		static int GetSong(string song)
		{
    		if (!songMapping.ContainsKey(song))
        		songMapping.Add(song, songMapping.Count + 1);

    		return songMapping[song];
		}

4. Наконец, создайте служебную программу, которая реализует метод SaveMapping, сохраняющий словари сопоставления программ-роботов в CSV-файлы.

		static void SaveMapping(Dictionary<string, int> mapping, string fileName)
		{
    		var stream = File.Open(fileName, FileMode.Create);
    		var writer = new StreamWriter(stream);

    		foreach (var key in mapping.Keys)
    		{
        		writer.Write(key);
        		writer.Write(',');
        		writer.WriteLine(mapping[key]);
    		}

    		writer.Close();
		}

5. Загрузите пример данных по [этой ссылке](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip). После загрузки откройте **train\_triplets.txt.zip** и извлеките файл **train\_triplets.txt**.

	При запуске служебной программы используйте аргумент командной строки, указывающий расположение файла **train\_triplets.txt**. Для этого щелкните правой кнопкой мыши узел проекта **ConvertToMahoutInput** в **обозревателе решений** и выберите **Свойства**. На странице свойств проекта перейдите на расположенную слева вкладку **Отладка** и добавьте путь &lt;локальный_путь&gt;train\_triplets.txt в текстовое поле **Аргументы командной строки**.

	![Задание аргументов командной строки][set-cmd-line-args]

###Задание аргумента командной строки

- Нажмите клавишу **F5** для запуска программы. По завершении откройте папку **bin\Debug** в расположении, в котором был сохранен проект, и просмотрите выходные данные служебной программы.  Найдите users.txt и mInput.txt

## <a name="segment2"></a>Установка Mahout 

- Откройте портал кластера HDInsight и щелкните значок **Удаленный рабочий стол**.

	![Значок управления кластером][mng-cluster-icon]

###Значок удаленного рабочего стола

HDInsight не содержит Mahout по умолчанию. Но поскольку библиотека является частью экосистемы Hadoop, ее можно загрузить с веб-сайта [Mahout](http://mahout.apache.org/). Новейшая версия — 0.7, но этот набор инструкций подходит для любой версии: 0.5 и 0.7.

1. Прежде всего загрузите [Mahout версии 0.7](http://www.apache.org/dyn/closer.cgi/mahout/) на локальный компьютер.

2. Затем выполните копирование в кластер, для чего выберите локальный ZIP-файл, нажмите клавиши Ctrl-C для копирования, а затем вставьте его в кластер Hadoop.

	![Отправка Mahout][uploading-mahout]

###Копирование Mahout в головной узел

1. После завершения процесса копирования щелкните правой кнопкой мыши ZIP-файл и извлеките дистрибутив Mahout в папку C:\apps\dist.  Теперь экземпляр mahout установлен в C:\apps\dist\mahout-distriution-0.7.  

2. Для простоты измените имя папки на c:\apps\dist\mahout-0.7.  

### <a name="segment3"></a>Выполнение задания Mahout 

1. Скопируйте файл **mInput.txt** из папки **bin\Debug** в папку **c:\\** удаленного кластера. По завершении копирования извлеките файл. Как было сказано в предыдущем разделе, для копирования файла в удаленный сеанс RDP после выбора файлов нажмите клавиши Ctrl-C на локальном компьютере, а затем клавиши Ctrl-V в окне сеанса RDP. 

2. Создайте файл, содержащий идентификатор пользователя, для которого будете создавать рекомендации. Для этого просто создайте текстовый файл с именем **users.txt** в папке **c:\\**, содержащий идентификатор одного пользователя.

<div class="dev-callout"> 
<b>Примечание</b> 
<p>Чтобы создать рекомендации для нескольких пользователей, поместите их идентификаторы в отдельных строках. Если возникают проблемы при создании файлов mInput.txt и users.txt, можно загрузить предварительно созданную версию в этом <a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">репозитории</a> github. 

Удобнее всего загрузить сразу все в одном <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">ZIP-файле</a>. Найдите файлы users.txt и mInput.txt и скопируйте их в удаленный кластер в папку c:\</p> 
</div>.

На этом этапе следует открыть окно терминала Hadoop и перейти в папку, содержащую файлы users.txt и mInput.txt.  

![Командное окно Mahout][mahout-cmd-window]

###Командное окно Hadoop

1. Теперь скопируйте оба файла **mInput.txt** и **users.txt** в HDFS. Для этого откройте **командную оболочку Hadoop** и выполните следующие команды:

		hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
		hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2. Убедитесь, что файлы скопированы в HDFS.

		hadoop fs -ls input/

	Результат должен выглядеть следующим образом:  

		Found 2 items
		-rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
		-rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3. Теперь можно выполнить задание Mahout, используя следующую команду:

		c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

	Существует множество "дистанционных" функций, с помощью которых подсистема рекомендаций может сравнивать вектор признаков для разных пользователей. Вы можете поэкспериментировать, изменяя класс Similarity на SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE.  В этом учебнике мы не будем подробно рассматривать научный аспект Mahout. 

4. Задание Mahout должно выполняться в течение нескольких минут, после чего создается файл выходных данных. Выполните следующую команду для создания локальной копии файла выходных данных:

		hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5. Откройте файл **output.txt** из корневой папки **c:\\** и проверьте его содержимое. Структура файла выглядит следующим образом:

		user	[song:rating,song:rating, ...]

6. Если вы хотите использовать другие части Mahout в кластере, следует сохранить копию Mahout.cmd в каталоге bin развернутого экземпляра Mahout.  


## <a name="summary"></a>Сводка 

Подсистемы рекомендаций предоставляют важные функциональные возможности для многих современных сайтов социальных сетей, интернет-магазинов, сайтов потоковой передачи мультимедиа и других веб-сайтов. Mahout предоставляет готовую подсистему рекомендаций, которая проста в использовании, содержит множество полезных функций и масштабируется в Hadoop.

##Дальнейшие действия

В статье демонстрируется использование командной строки Hadoop, однако те же задачи можно выполнить с помощью интерактивной консоли HDInsight. Дополнительные сведения см. в разделе [Руководство. Интерактивные консоли JavaScript и Hive в HDInsight][interactive-console].


[echo-nest]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
[create-console-app]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
[set-cmd-line-args]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
[mng-cluster-icon]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
[uploading-mahout]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
[mahout-cmd-window]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG


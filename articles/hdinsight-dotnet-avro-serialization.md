<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="Сериализация HDInsight Avro.NET" pageTitle="Сериализация данных с помощью Avro.NET | Azure" metaKeywords="" description="Узнайте, как Azure HDInsight использует Avro.NET для сериализации данных большого размера." metaCanonical="" services="hdinsight" documentationCenter="" title="Сериализация данных с помощью Avro.NET" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />



# Сериализация данных с помощью Avro.NET 

##Обзор
В этом разделе показывается, как использовать библиотеку Microsoft Avro (Avro.NET) для сериализации объектов и других структур данных в потоки байтов для сохранения их в памяти, базе данных или файле. Она реализует систему сериализации данных Apache Avro, используя JSON для определения не зависящей от языка схемы, обеспечивающей компактный формат обмена двоичными данными, который может обрабатываться многими языками (в настоящее время C, C++, C#, Java, PHP, Python и Ruby), когда необходимо восстановить объекты с помощью десериализации.
 

Формат сериализации Apache Avro широко используется в средах Hadoop, включая Microsoft HDInsight. Подробные сведения об этом формате можно найти в спецификации Avro Apache. 

Библиотека Microsoft HDInsight Avro.NET поддерживает два способа сериализации объектов: 
с помощью отражения; 
с помощью универсальной записи.

##Предварительные требования

Библиотека Microsoft .NET для Avro распространяется как пакет NuGet. Установка из Visual Studio: выберите вкладку **Проект** -> **Управление пакетами NGet...**, найдите "Avro" в поле **Поиск в Интернете**, а затем нажмите кнопку **Установить** рядом с пунктом **Библиотека Microsoft .NET для Avro**. Зависимость Newtonsoft.Json (> =.5.0.5) также автоматически загружается с 


###Структура
Сценарии:

 * <a href="#Scenario1">Сериализация с помощью отражения

 * <a href="#Scenario2">Сериализация с помощью контейнеров объектов Avro

 * <a href="#Scenario3">Сериализация с помощью файлов контейнеров объектов и сериализация с помощью отражения


 * <a href="#Scenario4">Сериализация с помощью файлов контейнеров объектов и сериализация с помощью универсальной записи


 * <a href="#Scenario5">Сериализация с помощью файлов контейнеров объектов с пользовательским кодеком сжатия


<h2> <a name="Scenario1">Сериализация с помощью отражения </a></h2>

В следующем примере для класса и структуры выполняются сериализация, десериализация и сравнение с первоначальными экземплярами для проверки идентичности. Схема JSON для типов автоматически создается библиотекой Microsoft Avro с учетом атрибутов контракта данных учетной записи. Для определения сериализуемых полей библиотека Microsoft Avro использует [арбитр контрактов данных](http://msdn.microsoft.com/ru-ru/library/ms731072(v=vs.110).aspx).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set represented as an object using Reflection
    //No explicit schema definition is required - schema of serialized objects is automatically built
    public void SerializeDeserializeObjectUsingReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
    //for serializing only properties attributed with DataContract/DateMember
    var avroSerializer = AvroSerializer.Create<SensorData>();
    
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    //Create a data set using sample Class and struct 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };
    
    //Serialize the data to the specified stream
    avroSerializer.Serialize(buffer, expected);
    
      
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Deserialize data from the stream and cast it to the same type used for serialization
    var actual = avroSerializer.Deserialize(buffer);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify that deserialized data matches the original one
    bool isEqual = this.Equal(expected, actual);
    
    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);
    
    }
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    
      
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Reflection
    Sample.SerializeDeserializeObjectUsingReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario2"></a>Сериализация с помощью контейнеров объектов Avro</h2>

Классы .NET, представляющие данные, не всегда доступны. Схема данных может быть динамической, например, когда данные представлены в виде файлов значений, разделенных запятыми (CSV), с неизвестной заранее схемой и требуют преобразования в формат Avro.
В таких случаях необходимо создать экземпляр специального класса (AvroRecord) с явно заданной схемой JSON, заполнить поля требуемыми данными и выполнить его сериализацию. 
В этом примере показано, как определить схему, создать соответствующую запись, заполнить ее данными, а затем выполнить ее сериализацию и десериализацию. Наконец, исходные и десериализованные объекты сравниваются для проверки их идентичности.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set using Generic Record.
    //Generic Record is a special class with the schema explicitly defined in JSON.
    //All serialized data should be mapped to the fields of Generic Record,
    //which in turn will be then serialized.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a Memory Stream buffer
    using (var stream = new MemoryStream())
    {
    //Create a generic record to represent the data
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;
    
    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };
    
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Serialize the data
    serializer.Serialize(stream, expected);
    
    stream.Seek(0, SeekOrigin.Begin);
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Deserialize the data into a generic record
    dynamic actual = serializer.Deserialize(stream);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Generic Record
    Sample.SerializeDeserializeObjectUsingGenericRecords();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario3"></a>Сериализация с помощью файлов контейнеров объектов и сериализация с помощью отражения</h2>

Этот пример похож на пример 1: класс и структура сериализуются и сохраняется в файле контейнера объекта, расположенном в текущем каталоге примера приложения. Затем этот файл читается, данные десериализуются и, в заключение, сравниваются с исходными экземплярами для проверки идентичности.
Данные в файле контейнера объекта сжаты с помощью кодека сжатия deflate (ссылка?).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with Deflate codec
    public void SerializeDeserializeUsingObjectContainersReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will be compressed using Deflate codec
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario4"></a>Сериализация с помощью файлов контейнеров объектов и сериализация с помощью универсальной записи</h2>

Этот пример похож на пример 2: пример набора данных сериализуется с помощью AvroRecord с явно определенной схемой JSON. Сериализованные данные хранятся в новом файле контейнера объекта, расположенном в текущем каталоге примера приложения. Затем этот файл читается, данные десериализуются и, в заключение, сравниваются с исходными экземплярами для проверки идентичности.

Данные в файле контейнера объекта не сжаты (используется нулевой кодек сжатия (Null)).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
    //Serialized data is not compressed
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleGenericRecordNullCodec.avro";
    
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a generic record to represent the data
    var testData = new List<AvroRecord>();
    
    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);
    
    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);
    
    //Serializing and saving data to file
    //Create a MemoryStream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will not be compressed (Null compression codec)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(streamWriter.Write);
    }
    }
    
    Console.WriteLine("Saving serialized data to file...");
    
    //Save stream to file
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializng the data
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Generic Record to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario5"></a>Сериализация с помощью файлов контейнеров объектов с пользовательским кодеком сжатия</h2>

[Спецификация Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) позволяет использовать дополнительные кодеки сжатия (помимо Null и Deflate). В следующем примере показывается, как использовать пользовательский кодек сжатия для файлов контейнеров объектов. Этот пример реализует не "действительно" другой кодек (например, Snappy, упомянутый в качестве поддерживаемого дополнительного кодека в спецификации Avro), а использует реализацию [Deflate](http://msdn.microsoft.com/ru-ru/library/system.io.compression.deflatestream(v=vs.110).aspx) .NET Framework 4.5 (обеспечивает более эффективный алгоритм сжатия на основе zlib), которая может оказаться полезной для многих разработчиков.

    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //
    
    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    #region Defining objects for serialization
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    #endregion
    
    #region Defining custom codec based on .NET Framework V.4.5 Deflate
    //Avro.NET Codec class contains two methods 
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //which are the key ones for data compression.
    //To enable a custom codec one needs to implement these methods for the required codec
    
    #region Defining Compression and Decompression Streams
    //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
    //can not be directly used for Avro because it does not support vital operations like Seek.
    //Thus one needs to implement two classes inherited from Stream
    //(one for compressed and one for decompressed stream)
    //that use Deflate compression and implement all required features 
    internal sealed class CompressionStreamDeflate45 : Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;
    
    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");
    
    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }
    
    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }
    
    public override void Flush()
    {
    this.compressionStream.Close();
    }
    
    public override long Length
    {
    get { return this.buffer.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }
    
    set
    {
    this.buffer.Position = value;
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }
    
    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);
    
    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }
    
    internal sealed class DecompressionStreamDeflate45 : Stream
    {
    private readonly DeflateStream decompressed;
    
    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }
    
    public override bool CanRead
    {
    get { return true; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return false; }
    }
    
    public override void Flush()
    {
    this.decompressed.Close();
    }
    
    public override long Length
    {
    get { return this.decompressed.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }
    
    set
    {
    throw new NotSupportedException();
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }
    
    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);
    
    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }
    #endregion
    
    #region Define Codec
    //Define the actual codec class containing the required methods for manipulating streams:
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //Codec class uses classes for comressed and decompressed streams defined above
    internal sealed class DeflateCodec45 : Codec
    {
    
    //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
    public static readonly string CodecName = "deflate";
    
    public DeflateCodec45()
    : base(CodecName)
    {
    }
    
    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }
    
    return new CompressionStreamDeflate45(decompressed);
    }
    
    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }
    
    return new DecompressionStreamDeflate45(compressed);
    }
    }
    #endregion
    
    #region Define modified Codec Factory
    //Define modified Codec Factory to be used in Reader
    //It will catch the attempt to use "deflate" and provide Custom Codec 
    //For all other cases it will rely on the base class (CodecFactory)
    internal sealed class CodecFactoryDeflate45 : CodecFactory
    {
    
    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }
    #endregion
    
    #endregion
    
    #region Sample Class with demonstration methods
    //This class contains methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
    //
    //This sample uses Memory Stream for all operations related to serialization, deserialization and
    //Object Container manipulation, though File Stream could be easily used.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate45.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
    //when Codec Factory is explicitly specified
    //You may comment the line below if you want to use built-in Deflate (see next comment)
    AvroSerializerSettings settings = new AvroSerializerSettings();
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    //Here the custom Codec Factory is introduced.
    //For convenience the next commented code line shows how to use built-in Deflate
    //(no explicit Codec Factory parameter is required in this case).
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    #endregion
    
    #region Helper Methods
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
    }
    }
    #endregion
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File using Custom Codec
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

	




<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Microsoft .NET Library for Serialization with Avro" pageTitle="Serialize data with the Microsoft .NET Library for Avro | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize data with the Microsoft .NET Library for Avro " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Сериализация данных с помощью библиотеки Microsoft .NET для Avro

## Обзор

В этом разделе показывается, как использовать библиотеку Microsoft .NET для Avro для сериализации объектов и других структур данных в потоки байтов, чтобы сохранить их в памяти, базе данных или файле, а также как десериализовать их, чтобы восстановить исходные объекты.

### Apache Avro

В библиотеке Microsoft .NET для Avro реализуется система сериализации данных Apache Avro для среды Microsoft.NET. Apache Avro обеспечивает компактный формат обмена двоичными данными для сериализации. Библиотека использует [JSON][] для определения не зависящей от языка схемы, которая гарантирует взаимодействие языков. Данные, сериализованные в одном языке, могут быть прочитаны в другом. В настоящее время поддерживаются C, C++, C#, Java, PHP, Python и Ruby. Подробные сведения об этом формате см. в [Спецификации Аpache Avro][]. Обратите внимание, что текущая версия библиотеки Microsoft .NET для Avro не поддерживает часть удаленных вызовов процедур (RPC) этой спецификации.

Сериализованное представление объекта в системе Avro состоит из двух частей: схемы и фактического значения. Схема Avro описывает независимую от языка модель сериализованных данных с помощью JSON. Она существует параллельно с двоичным представлением данных. Наличие отдельной от двоичного представления схемы позволяет записывать каждый объект без нагрузок в зависимости от значения, что ускоряет сериализацию и обеспечивает небольшой размер представления.

### Сценарий Hadoop

Формат сериализации Apache Avro широко используется в Azure HDInsight и других средах Apache Hadoop. Это удобный способ для представления сложных структур данных в заданиях Hadoop MapReduce. Формат файлов Avro был разработан для поддержки распределенной модели программирования MapReduce. Основная особенность, которая обеспечивает распределение, состоит в «делимости» файлов в том смысле, что можно отыскать любую точку в файле и начать чтение с определенного блока.

### Сериализация данных с помощью библиотеки Microsoft .NET для Avro

Библиотека .NET для Avro поддерживает два типа сериализации объектов:

-   **отражение**: Схема JSON для типов создается автоматически с учетом атрибутов контракта данных типов .NET, которые необходимо сериализовать.
-   **универсальная запись**: схема JSON задается явно в записи, представленной классом [**AvroRecord**][], когда отсутствуют типы .NET для описания схемы, чтобы сериализвать данные.

Когда схема известна для модулей записи и чтения потока, данные могут передаваться без схемы. В противном случае схема должна использоваться совместно через файл контейнера Avro. Могут задаваться и другие параметры, такие как кодек для сжатия данных. Эти сценарии рассматриваются более подробно ниже и проиллюстрированы с помощью примеров кода.

### Предварительные условия использования библиотеки Microsoft .NET для Avro

-   [Microsoft .NET Framework v4.0][]
-   [Newtonsoft Json.NET][] (v5.0.5 или выше)

Обратите внимание, что зависимость Newtonsoft.Json.dll загружается автоматически при установке библиотеки Microsoft .NET для Avro. Эта процедура описывается в следующем разделе.

### Установка библиотеки Microsoft .NET для Avro

Библиотека Microsoft .NET для Avro распространяется как пакет NuGet, который можно установить из Visual Studio, используя следующую процедуру:

-   Перейдите на вкладку **Проект** -\> **Управление пакетами NuGet...**
-   Найдите "Microsoft.Hadoop.Avro" в поле **Поиск в Интернете**.
-   Нажмите кнопку **Установить** рядом с пунктом **Библиотека Microsoft .NET для Avro**.

Обратите внимание, что Newtonsoft.Json.dll (\>= .5.0.5) также загружается автоматически вместе с библиотекой Microsoft .NET для Avro.

## Руководство по примерам

Приведенные в данном разделе пять примеров иллюстрируют различные сценарии, поддерживаемые библиотекой Microsoft .NET для Avro.

В первых двух примерах показано, как сериализовать и десериализовать данные в буферах потока памяти с использованием отражения и универсальных записей Предполагается, что в этих двух примерах схема будет совместно использоваться модулями записи и чтения вне полосы пропускания, что освобождает от необходимости сериализовать схему вместе с данными в файле контейнера Avro.

В третьем и четвертом примерах показано, как сериализовать и десериализовать данные в буферах потока памяти с использованием отражения и универсальной записи с помощью файлов контейнера объектов Avro. При сохранении данных в файле контейнера Avro их схема всегда сохраняется вместе с ними, так как она должна совместно использоваться для десериализации.

Образец с первыми четырьмя примерами можно загрузить с сайта [Примеры кода Azure][].

В пятом и последнем примере показано, как использовать настраиваемый кодек сжатия для файлов контейнера объектов. Образец с кодом для этого примера можно загрузить с сайта [Примеры кода Azure][1].

Библиотека Microsoft .NET для Avro создана для работы с любым потоком. В этих примерах для простоты и согласованности данных они обрабатываются с использованием потоков в памяти, а не файловых потоков или баз данных. Подход для использования в производственной среде будет зависеть от конкретных требований сценария, источника данных и их объема, ограничений производительности, а также других факторов.

-   [**Сериализация с помощью отражения**][]: Схема JSON для типов, которые необходимо сериализовать, создается автоматически с учетом атрибутов контракта данных.
-   [**Сериализация с помощью универсальной записи**][]: Схема JSON задается явно в записи, когда типы .NET недоступны для отражения.
-   [**Сериализация с использованием файлов контейнеров объектов с помощью отражения**][]: Схема JSON неявно сериализуется вместе с данными и совместно используется через файл контейнера Avro.
-   [**Сериализация с использованием файлов контейнеров объектов с помощью универсальной записи**][]: Схема JSON явно сериализуется вместе с данными и совместно используется через файл контейнера Avro.
-   [**Сериализация с использованием файлов контейнеров объектов с помощью настраиваемого кодека сжатия**][]: Схема JSON неявно сериализуется вместе с данными и совместно используется через файл контейнера Avro с помощью настроенной реализации .NET кодека сжатия данных deflate.

## <a name="Scenario1"></a>Сериализация с помощью отражения

Схема JSON для типов может создаваться автоматически библиотекой Microsoft .NET для Avro с использованием отражения атрибутов контракта данных объектов C#, которые необходимо сериализовать. Библиотека Microsoft .NET для Avro создает сериализатор [**IAvroSeralizer<t>**][] для определения сериализуемых полей.

В этом примере объекты (класс **SensorData** c членом структуры **Location**) сериализуются в поток в памяти, а этот поток, в свою очередь, десериализуется. Далее результат сравнивается с исходным экземпляром для подтверждения, что объект **SensorData** был восстановлен в полном соответствии с оригиналом.

Предполагается, что схема в этом примере используется совместно модулями чтения и записи, в результате чего формат контейнера объектов Avro не требуется. Пример сериализиции и десериализации данных в буферах памяти при использовании отражения с помощью формата контейнера объектов, когда схема должна быть сериализована вместе данными, см. в разделе [Сериализация с использованием файлов контейнеров объектов с помощью отражения.][**Сериализация с использованием файлов контейнеров объектов с помощью отражения**]

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
        //the usage of Microsoft .NET Library for Avro
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

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

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

## <a name="Scenario2"></a>Сериализация с помощью универсальной записи

Схема JSON может быть явно задана в универсальной записи, если нельзя использовать отражение, когда данные не могут быть представлены при помощи классов .NET с контрактом данных. Этот метод как правило медленнее, чем использование отражения и сериализаторов для определенного класса C#. В подобных случаях схема для данных может быть также динамичной, поскольку оно неизвестна до выполнения компиляции. В качестве примера подобного рода динамического сценария можно привести данные, представленные в виде файлов значений, разделенных запятыми (CSV), с неизвестной заранее схемой до ее преобразования в формат Avro во время выполнения.

В этом примере показано, как создавать и использовать запись [**AvroRecord**][] для явного определения схемы JSON, как заполнять ее данными, а затем сериализовать и десериализовать. Далее результат сравнивается с исходным экземпляром для подтверждения, что запись была восстановлена в полном соответствии с оригиналом.

Предполагается, что схема в этом примере используется совместно модулями чтения и записи, в результате чего формат контейнера объектов Avro не требуется. Пример сериализиции и десериализации данных в буферах памяти при использовании универсальной записи с помощью формата контейнера объектов, когда схема должна быть включена вместе с сериализованными данными, см. в разделе [Сериализация с использованием файлов контейнеров объектов с помощью универсальной записи.][**Сериализация с использованием файлов контейнеров объектов с помощью универсальной записи**]

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
    //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario3"></a>Сериализация с помощью файлов контейнеров объектов и сериализация с помощью отражения

Этот пример аналогичен сценарию в [первом примере][**Сериализация с помощью отражения**], в котором схема определяется неявно с помощью отражения, за исключением предположения, что здесь схема неизвестна модулю чтения, который десериализует ее. Сериализуемые объекты **SensorData** и их неявно заданная схема сохраняются в файле контейнера объектов, представленном классом [**AvroContainer**][].

Данные в этом примере сериализуются с помощью [**SequentialWriter<sensordata>**][], а десериализуются с использованием [**SequentialReader<sensordata>**][**SequentialWriter<sensordata>**]. Далее результат сравнивается с исходными экземплярами для подтверждения их идентичности.

Данные в файле контейнера объекта сжимаются по умолчанию с использованием кодека сжатия [**Deflate**][] с платформы .NET Framework 4.0. См. [последний пример][**Сериализация с использованием файлов контейнеров объектов с помощью настраиваемого кодека сжатия**] в этом разделе, чтобы научиться использовать более новую и совершенную версию кодека сжатия [**Deflate**][2], доступную на платформе .NET Framework 4.5.

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
        //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario4"></a>Сериализация с помощью файлов контейнеров объектов и сериализация с помощью универсальной записи

Этот пример аналогичен сценарию во [втором примере][**Сериализация с помощью универсальной записи**], в котором схема определяется явно с помощью JSON, за исключением предположения, что здесь схема неизвестна модулю чтения, который десериализует ее.

Набор тестовых данных помещается в список объектов [**AvroRecord**][] с использованием явно определенной схемы JSON, а затем сохраняется как файл контейнера объекта, представленный классом [**AvroContainer**][]. Этот файл контейнера создает модуль записи, который используется для сериализации несжатых данных в поток в памяти, который затем сохраняется в файле. Это параметр [**Codex.Null**][], используемый для создания модуля чтения, который определяет, что эти данные не будут сжиматься.

Далее данные считываются из файла и десериализуются в коллекцию объектов. Эта коллекция сравнивается с исходным списком записей Avro для подтверждения их идентичности.

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
        //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario5"></a>Сериализация с использованием файлов контейнеров объектов с помощью настраиваемого кодека сжатия

В следующем примере показывается, как использовать настраиваемый кодек сжатия для файлов контейнеров объектов Avro. [Спецификация Avro][] позволяет использовать дополнительные кодеки сжатия (помимо **Null** и **Deflate**, которые используются по умолчанию). В примере не реализуется полностью полностью новый кодек Snappy (упоминаемый как поддерживаемый дополнительный кодек вn [Спецификации Аvro][]). Здесь показано, как использовать реализацию .NET Framework 4.5 кодека [**Deflate**][2], которая обеспечивает выполнение лучшего алгоритма сжатия на основе библиотеки сжатия [zlib][]m, чем используемая по умолчанию версия .NET Framework 4.0.

    // 
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
        //the usage of Microsoft .NET Library for Avro
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

  [JSON]: http://www.json.org
  [Спецификации Аpache Avro]: http://avro.apache.org/docs/current/spec.html
  [**AvroRecord**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.avrorecord.aspx
  [Microsoft .NET Framework v4.0]: http://www.microsoft.com/en-us/download/details.aspx?id=17851
  [Newtonsoft Json.NET]: http://james.newtonking.com/json
  [Примеры кода Azure]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923
  [1]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111
  [**Сериализация с помощью отражения**]: #Scenario1
  [**Сериализация с помощью универсальной записи**]: #Scenario2
  [**Сериализация с использованием файлов контейнеров объектов с помощью отражения**]: #Scenario3
  [**Сериализация с использованием файлов контейнеров объектов с помощью универсальной записи**]: #Scenario4
  [**Сериализация с использованием файлов контейнеров объектов с помощью настраиваемого кодека сжатия**]: #Scenario5
  [**IAvroSeralizer<t>**]: http://msdn.microsoft.com/en-us/library/dn627341.aspx
  [**AvroContainer**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.container.avrocontainer.aspx
  [**SequentialWriter<sensordata>**]: http://msdn.microsoft.com/en-us/library/dn627340.aspx
  [**Deflate**]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.100).aspx
  [2]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.110).aspx
  [**Codex.Null**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.container.codec.null.aspx
  [Спецификация Avro]: http://avro.apache.org/docs/current/spec.html#Required+Codecs
  [Спецификации Аvro]: http://avro.apache.org/docs/current/spec.html#snappy
  [zlib]: http://zlib.net/

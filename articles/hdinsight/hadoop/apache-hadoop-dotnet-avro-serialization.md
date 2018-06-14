---
title: Сериализация данных в Hadoop с помощью библиотеки Microsoft Avro — Azure | Документы Майкрософт
description: Узнайте, как сериализовать и десериализовать данные в Hadoop на основе HDInsight с помощью библиотеки Microsoft Avro для их сохранения в памяти, базе данных или файле.
keywords: avro,hadoop avro
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 0d195ab3b84a522eae4010f3b08a829f7056a35f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202355"
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Сериализация данных в Hadoop с помощью библиотеки Microsoft Avro

>[!NOTE]
>Пакет SDK Avro больше не поддерживается корпорацией Майкрософт. Библиотека поддерживается сообществом разработчиков ПО с открытым кодом. Источники для библиотеки доступны на портале [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

В этой статье показано, как использовать [библиотеку Microsoft Avro](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) для сериализации объектов и других структур данных в потоки с целью их сохранения в памяти, базе данных или файле. Также показано, как десериализовать данные для восстановления исходных объектов.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Библиотека Microsoft Avro</a> реализует систему сериализации данных Apache Avro для среды Microsoft.NET. Apache Avro обеспечивает компактный формат обмена двоичными данными для сериализации. Она использует <a href="http://www.json.org" target="_blank">JSON</a> для определения не зависящей от языка схемы, которая гарантирует взаимодействие языков. Данные, сериализованные в одном языке, могут быть прочитаны в другом. В настоящее время поддерживаются C, C++, C#, Java, PHP, Python и Ruby. Подробные сведения об этом формате см. в <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">спецификации Аpache Avro</a>. 

>[!NOTE]
>Библиотека Microsoft Avro не поддерживает часть этой спецификации, связанную с удаленными вызовами процедур (RPC).
>

Сериализованное представление объекта в системе Avro состоит из двух частей: схемы и фактического значения. Схема Avro описывает независимую от языка модель сериализованных данных с помощью JSON. Она представлена параллельно с двоичным представлением данных. Наличие отдельной от двоичного представления схемы позволяет записывать каждый объект без нагрузок в зависимости от значения, что ускоряет сериализацию и обеспечивает небольшой размер представления.

## <a name="the-hadoop-scenario"></a>Сценарий Hadoop
Формат сериализации Apache Avro широко используется в Azure HDInsight и других средах Apache Hadoop. Это удобный способ для представления сложных структур данных в заданиях Hadoop MapReduce. Формат файлов Avro (файлов контейнера объектов Avro) был разработан для поддержки распределенной модели программирования MapReduce. Основная особенность, которая обеспечивает распределение, состоит в «делимости» файлов в том смысле, что можно отыскать любую точку в файле и начать чтение с определенного блока.

## <a name="serialization-in-avro-library"></a>Сериализация в библиотеке Avro
Библиотека .NET для Avro поддерживает два типа сериализации объектов:

* **отражение** — схема JSON для типов создается автоматически с учетом атрибутов контракта данных типов .NET, которые необходимо сериализовать;
* **универсальная запись** — схема JSON задается явно в записи, представленной классом [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx), когда отсутствуют типы .NET для описания схемы, чтобы сериализовать данные.

Когда схема известна для модулей записи и чтения потока, данные могут передаваться без схемы. В случаях, когда используется файл контейнера объектов Avro, схема хранится в этом файле. Могут задаваться и другие параметры, такие как кодек для сжатия данных. Эти сценарии рассматриваются более подробно и проиллюстрированы в следующих примерах кода:

## <a name="install-avro-library"></a>Установка библиотеки Avro
Ниже приведены компоненты, которые должны быть установлены до установки библиотеки.

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 или более поздней версии)

Обратите внимание, что зависимость Newtonsoft.Json.dll загружается автоматически вместе с установкой библиотеки Microsoft Avro. Процедура предоставлена в следующем разделе:

Библиотека Microsoft Avro распространяется как пакет NuGet, который может быть установлен из Visual Studio с помощью следующей процедуры.

1. Перейдите на вкладку **Проект** -> **Управление пакетами NuGet...**
2. Найдите Microsoft.Hadoop.Avro в поле **Поиск в Интернете** .
3. Нажмите кнопку **Установить** рядом с пунктом **Microsoft Azure HDInsight Avro Library** (Библиотека Microsoft Azure HDInsight Avro).

Обратите внимание, что зависимость Newtonsoft.Json.dll (>=6.0.4) также скачивается автоматически вместе с библиотекой Microsoft Avro.

Исходный код библиотеки Microsoft Avro доступен на портале [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Компиляция схем с использованием библиотеки Avro
Библиотека Microsoft Avro содержит служебную программу создания кода, которая позволяет создавать типы C# автоматически на основе ранее определенной схемы JSON. Эта служебная программа создания кода не распространяется в виде двоичного исполняемого файла, но ее можно легко построить с помощью следующей процедуры.

1. Загрузите ZIP-файл с последней версией исходного кода пакета SDK для HDInsight со страницы <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK для Hadoop</a>. (Щелкните значок **Скачать**, а не вкладку **Загрузки**.)
2. Извлеките пакет SDK для HDInsight в каталог на подключенном к Интернету компьютере с установленной платформой .NET Framework 4 для загрузки пакетов NuGet необходимых зависимостей. Далее подразумевается, что исходный код извлечен в папку C:\SDK.
3. Откройте папку C:\SDK\SRC\Microsoft.Hadoop.Avro.Tools и запустите файл build.bat. (Файл вызывает MSBuild из 32-разрядного пакета распространения платформы .NET Framework. Если вы хотите использовать 64-разрядную версию, измените файл build.bat в соответствии с комментариями в этом файле.) Убедитесь, что сборка выполнена успешно. (В некоторых системах MSBuild может создавать предупреждения. Эти предупреждения не влияют на служебную программу, если отсутствуют ошибки сборки.)
4. Скомпилированная служебная программ находится в папке C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Чтобы ознакомиться с синтаксисом командной строки, выполните следующую команду из папки, в которой расположена эта служебная программа создания кода: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Чтобы протестировать программу, можно создать классы C# из примера файла схемы JSON, предоставляемого с исходным кодом. Выполните следующую команду.

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

В результате в текущем каталоге должно быть создано два файла C#: SensorData.cs и Location.cs.

Чтобы понять логику, которую использует служебная программа создания кода во время преобразования схемы JSON в типы C#, см. файл GenerationVerification.feature, расположенный в папке C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Для извлечения пространств имен из схемы JSON используется логика, которая описана в файле, упомянутом в предыдущем абзаце. Пространства имен, извлеченные из схемы, имеют приоритет над теми, которые указываются с помощью параметра /n в командной строке этой служебной программы. Если требуется переопределить пространства имен, которые содержатся в схеме, используйте параметр /nf. Например, чтобы изменить все пространства имен в SampleJSONSchema.avsc на my.own.nspace, выполните следующую команду:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Сведения об образцах
Приведенные в данном разделе шесть примеров иллюстрируют различные сценарии, поддерживаемые библиотекой Microsoft Avro. Библиотека Microsoft Avro создана для работы с любым потоком. В этих примерах для простоты и последовательности управление данными осуществляется с использованием потоков в памяти, а не файловых потоков или баз данных. Подход для использования в производственной среде зависит от конкретных требований сценария, источника данных и их объема, ограничений производительности, а также других факторов.

В первых двух примерах показано, как сериализовать и десериализовать данные в буферах потоков памяти с помощью отражения и универсальных записей. Предполагается, что схема в этих двух примерах используется совместно модулями чтения и записи вне полосы пропускания.

В третьем и четвертом примерах показано, как сериализовать и десериализовать данные с помощью файлов контейнера объектов Avro. При сохранении данных в файле контейнера Avro их схема всегда сохраняется вместе с ними, так как она должна совместно использоваться для десериализации.

Образец с первыми четырьмя примерами можно загрузить с сайта <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">Примеры кода Azure</a> .

В пятом примере показано, как использовать настраиваемый кодек сжатия для файлов контейнера объектов Avro. Образец с кодом для этого примера можно загрузить с сайта <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">Примеры кода Azure</a> .

В шестом примере показано, как использовать сериализацию Avro для передачи данных в хранилище больших двоичных объектов Azure, а затем проанализировать их с помощью Hive в кластере HDInsight (Hadoop). Его можно загрузить с сайта <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Примеры кода Azure</a> .

Ниже приведены ссылки на шесть примеров, рассмотренных в статье.

* <a href="#Scenario1">**Сериализация с помощью отражения**</a> — схема JSON для типов, которые требуется сериализовать, создается автоматически с учетом атрибутов контракта данных.
* <a href="#Scenario2">**Сериализация с помощью универсальной записи**</a> — схема JSON задается явно в записи, когда типы .NET недоступны для отражения.
* <a href="#Scenario3">**Сериализация с использованием файлов контейнеров объектов с помощью отражения**</a> — схема JSON создается автоматически и используется совместно с сериализуемыми данными с помощью файла контейнера объектов Avro.
* <a href="#Scenario4">**Сериализация с использованием файлов контейнера объектов с помощью универсальной записи**</a> — схема JSON задается явно до сериализации и общего доступа совместно с данными с помощью файла контейнера объектов Avro.
* <a href="#Scenario5">**Сериализация с использованием файлов контейнера объектов с помощью кодека сжатия**</a> — в этом примере показано, как создать файл контейнера объектов Avro с помощью настроенной реализации .NET кодека сжатия данных Deflate.
* <a href="#Scenario6">**Использование Avro для отправки данных для службы Microsoft Azure HDInsight**</a> — этот пример показывает, как сериализация Avro взаимодействует со службой HDInsight. Для выполнения этого примера требуется активная подписка Azure и доступ к кластеру Azure HDInsight.

## <a name="Scenario1"></a>Пример 1. Сериализация с помощью отражения
Схема JSON для типов может создаваться автоматически библиотекой Microsoft Avro с использованием отражения атрибутов контракта данных объектов C#, которые необходимо сериализовать. Библиотека Microsoft Avro создает сериализатор [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) для определения сериализуемых полей.

В этом примере объекты (класс **SensorData** c элементом структуры **Location**) сериализуются в поток в памяти, а этот поток, в свою очередь, десериализуется. Далее результат сравнивается с исходным экземпляром для подтверждения, что объект **SensorData** был восстановлен в полном соответствии с оригиналом.

Предполагается, что схема в этом примере используется совместно модулями чтения и записи, в результате чего формат контейнера объектов Avro не требуется. Пример сериализации и десериализации данных в буферах памяти при использовании отражения с помощью формата контейнера объектов, когда схема должна совместно использоваться вместе с данными, см. в разделе <a href="#Scenario3">Сериализация с использованием файлов контейнеров объектов с помощью отражения</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
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

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
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

                //Serialization to memory using reflection
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


## <a name="sample-2-serialization-with-a-generic-record"></a>Пример 2. Сериализация с помощью универсальной записи
Схема JSON может быть явно задана в универсальной записи, если нельзя использовать отражение, поскольку данные не могут быть представлены при помощи классов .NET с контрактом данных. Этот метод занимает больше времени, чем использование отражения. В подобных случаях схема для данных может также быть динамической, то есть неизвестной во время компиляции. В качестве примера подобного рода динамического сценария можно привести данные, представленные в виде файлов значений, разделенных запятыми (CSV), с неизвестной заранее схемой до ее преобразования в формат Avro во время выполнения.

В этом примере показано, как создавать и использовать запись [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) для явного определения схемы JSON, как заполнять ее данными, а затем сериализовать и десериализовать. Далее результат сравнивается с исходным экземпляром для подтверждения, что запись была восстановлена в полном соответствии с оригиналом.

Предполагается, что схема в этом примере используется совместно модулями чтения и записи, в результате чего формат контейнера объектов Avro не требуется. Пример сериализации и десериализации данных в буферах памяти при использовании универсальной записи с помощью формата контейнера объектов, когда схема должна быть включена вместе с сериализованными данными, см. в примере <a href="#Scenario4">Сериализация с использованием файлов контейнеров объектов с помощью универсальной записи</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
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

            //Create a memory stream buffer
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

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
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


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Пример 3. Сериализация с помощью файлов контейнеров объектов и сериализация с помощью отражения
Этот пример похож на сценарий в <a href="#Scenario1">первом примере</a>, где схема задается неявно с помощью отражения. Различие состоит в том, что здесь схема считается неизвестной модулю чтения, который выполняет десериализацию. Сериализуемые объекты **SensorData** и их неявно заданная схема сохраняются в файле контейнера объектов Avro, представленном классом [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx).

Данные в этом примере сериализуются с помощью [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx), а десериализуются с помощью [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Далее результат сравнивается с исходными экземплярами для подтверждения идентичности.

Данные в файле контейнера объекта сжимаются с использованием кодека сжатия [**Deflate**][deflate-100] по умолчанию из платформы .NET Framework 4. Ознакомьтесь с <a href="#Scenario5">пятым примером</a> в этом разделе, чтобы научиться использовать более новую и совершенную версию кодека сжатия [**Deflate**][deflate-110], доступную на платформе .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
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

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
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

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
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

            //Reading a file content by using the given path to a memory stream
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

            //Deleting file by using given path
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

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
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


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Пример 4. Сериализация с помощью файлов контейнеров объектов и сериализация с помощью универсальной записи
Этот пример похож на сценарий во <a href="#Scenario2">втором примере</a>, где схема задается явно с помощью JSON. Различие состоит в том, что здесь схема считается неизвестной модулю чтения, который выполняет десериализацию.

Набор тестовых данных помещается в список объектов [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) с использованием явно определенной схемы JSON, а затем сохраняется как файл контейнера объектов, представленного классом [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). Этот файл контейнера создает модуль записи, который используется для сериализации несжатых данных в поток в памяти, который затем сохраняется в файле. Параметр [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx), используемый для создания модуля чтения, указывает, что эти данные не сжимаются.

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
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
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

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
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

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
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

            //Reading a file content by using the given path to a memory stream
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

            //Deleting file by using the given path
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

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
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




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Пример 5. Сериализация с использованием файлов контейнеров объектов с помощью настраиваемого кодека сжатия
В пятом примере показано, как использовать настраиваемый кодек сжатия для файлов контейнера объектов Avro. Образец с кодом для этого примера можно загрузить с сайта [Примеры кода Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) .

[Спецификация Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) позволяет использовать дополнительные кодеки сжатия (помимо **Null** и **Deflate**, которые используются по умолчанию). В этом примере не реализуется новый кодек, такой как Snappy (который упоминается как поддерживаемый дополнительный кодек в [спецификации Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Здесь показано, как использовать реализацию .NET Framework 4.5 кодека [**Deflate**][deflate-110], которая обеспечивает лучший алгоритм сжатия на основе библиотеки сжатия [zlib](http://zlib.net/), чем используемая по умолчанию версия .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
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
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
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
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
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
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
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
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
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

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
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

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
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

            //Reading file content by using the given path to a memory stream
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

            //Deleting file by using given path
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

                //Serialization using reflection to Avro object container file using custom codec
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

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Пример 6. Использование Avro для передачи данных в службу Microsoft Azure HDInsight
В шестом примере показаны некоторые методы программирования, предусматривающие взаимодействие со службой Azure HDInsight. Образец с кодом для этого примера можно загрузить с сайта [Примеры кода Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) .

Этот образец выполняет следующие задачи:

* Выполняет подключение к существующему кластеру службы HDInsight.
* Сериализует несколько CSV-файлов и передает результаты в хранилище больших двоичных объектов Azure. CSV-файлы распространяются вместе с образцом и представляют выдержку из исторических данных AMEX, распространяемых службой [Infochimps](http://www.infochimps.com/) за период 1970–2010. Образец считывает данные CSV-файла, преобразует записи в экземпляры класса **Stock** , а затем сериализует их с использованием отражения. Определение типа Stock создается из схемы JSON с помощью служебной программы создания кодов библиотеки Microsoft Avro.
* Создает новую внешнюю таблицу с именем **Stocks** в Hive и связывает ее с данными, отправленными в предыдущем шаге.
* Выполняет запрос с помощью Hive в таблице **Stocks** .

Кроме того, до и после выполнения основных операций образец выполняет очистку. Во время очистки удаляются все связанные данные и папки больших двоичных объектов Azure, а также таблица Hive. Вы также можете вызвать процедуру очистки из командной строки образца.

Предварительные требования для выполнения этого образца.

* Активная подписка Microsoft Azure и ее идентификатор.
* Сертификат управления для подписки с соответствующим закрытым ключом. Сертификат должен быть установлен в частном хранилище текущего пользователя на компьютере, используемом для запуска образца.
* Активный кластер HDInsight.
* Учетная запись хранения Azure, связанная с кластером HDInsight из предыдущего требования, с соответствующим первичным или вторичным ключом доступа.

Прежде чем запустить образец, введите всю информацию, изложенную в требованиях, в файл конфигурации образца. Это можно осуществить двумя путями:

* отредактировать файл app.config в корневом каталоге данного образца, а затем собрать образец;
* сначала собрать образец, а затем отредактировать файл AvroHDISample.exe.config в каталоге сборки.

В обоих случаях все изменения необходимо вносить в разделе параметров **<appSettings>**. Следуйте указаниям в файле.
Чтобы запустить образец, необходимо выполнить следующую команду из командной строки (предполагается, что ZIP-файл, предоставляемый с образцом, извлечен в папку C:\AvroHDISample; в противном случае используйте соответствующий путь к файлу):

    AvroHDISample run C:\AvroHDISample\Data

Чтобы очистить кластер, выполните следующую команду:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx

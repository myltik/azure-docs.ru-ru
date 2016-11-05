---
title: Руководство по созданию службы данных для Marketplace | Microsoft Docs
description: Подробные инструкции по созданию, сертификации и развертыванию службы данных для продажи в Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''

ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova

---
# Сопоставление существующей веб-службы и OData с помощью CSDL
> [!IMPORTANT]
> **В настоящее время мы больше не подключаем новые издатели служб данных. Новые службы данных не будут утверждены для добавления в список.** Если вы хотите опубликовать бизнес-приложение SaaS на AppSource, то можете ознакомиться с дополнительными сведениями [здесь](https://appsource.microsoft.com/partners). Если вы хотите опубликовать приложения IaaS или службу разработчика в Azure Marketplace, то можете найти дополнительные сведения [здесь](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

В этой статье приводятся общие сведения по использованию языка CSDL для сопоставления существующей и совместимой служб OData. Она объясняет, как создать документ сопоставления (CSDL), который преобразует входной запрос от клиента с помощью вызова службы и выходные данные обратно клиенту с помощью совместимого веб-канала OData. Microsoft Azure Marketplace предоставляет службы для конечных пользователей с помощью протокола OData. Службы, предоставляемые поставщиками содержимого (владельцами данных), представлены в различных формах, например REST, SOAP и т. д.

## CSDL и его структура
CSDL (язык определения концептуальной схемы) — это спецификация, определяющая, как описывать веб-службу или службу базы данных в общих формулировках XML в Azure Marketplace.

Краткий обзор **потока запроса**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**Поток данных** — в обратном направлении:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Рис. 1** показывает, как клиент будет принимать данные от поставщика содержимого (службы), перейдя в магазин Azure. CSDL используется компонентом сопоставления или преобразования для обработки запроса и передачи данных между службами поставщика содержимого и клиентом, отправившим запрос.

*Рисунок 1. Подробный поток от клиента, отправившего запрос, к поставщику содержимого через Azure Marketplace*

  ![рисунок](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Сведения об Atom, Atom Pub и протоколе OData, на базе которых создаются расширения Azure Marketplace, см. на странице: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Фрагмент из указанной выше ссылки: *"Протокол Open Data (в дальнейшем именуемый OData) предназначен для предоставления протокола на основе REST для операций CRUD (создание, чтение, обновление и удаление) с использованием ресурсов, используемых в качестве служб данных. "Служба данных" — это конечная точка, в которой представлены данные из одной или нескольких "коллекций", в каждой из которых ноль или более "записей" состоят из типизированной пары "имя-значение". OData публикуется корпорацией Майкрософт в разделе стандартов OASIS (организация для продвижения стандартов структурированной информации), чтобы любой пользователь мог создать серверы, клиенты или средства без отчислений и ограничений".*

### Вот три важнейшие части, которые должны быть определены в языке CSDL.
* **Конечная точка** службы поставщика. Веб-адрес (URI) службы
* **Параметры данных**, передаваемых в качестве входных данных поставщику услуг. Определения параметров, отправляемых службе поставщика содержимого до типа данных.
* **Схема** данных, возвращаемых запрашивающей службе. Схема данных, отправляемых службой поставщика содержимого, включая контейнер, коллекции и таблицы, переменные и столбцы и типы данных.

В примере ниже показан обзор потока, из которого клиент вводит инструкции OData (вызов веб-службы поставщика содержимого) для возврата результатов или данных.

  ![рисунок](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### Шаги:
1. Клиент отправляет запрос через вызов службы, дополненный входными параметрами, определенными в XML в Azure Marketplace
2. Язык CSDL используется для проверки вызова службы.
   * Форматированный вызов службы затем отправляется службе поставщиков содержимого в Azure Marketplace
3. Веб-служба запускает и выполняет действие команды Http (например GET). Данные возвращаются в Azure Marketplace, где запрошенные данные (если таковые имеются) предоставляются клиенту в формате XML с помощью сопоставления, определенного в языке CSDL.
4. Клиенту отправляются данные (если таковые имеются) в формате XML или JSON

## Определения
### OData ATOM pub
Расширение ATOM pub, где каждая запись представляет одну строку результирующего набора. Часть содержимого записи расширена и содержит значения строки — в виде пар ключ-значение. Дополнительные сведения можно найти здесь: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL — язык определения концептуальной схемы
Позволяет определять функции (SPROC) и сущности, предоставляемые через базу данных. Дополнительные сведения приведены здесь: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)

> [!TIP]
> Щелкните раскрывающийся список **Другие версии** и выберите версию, если вы не видите статью.
> 
> 

### EDM — модель данных записи
* Обзор: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]

[OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Предварительная версия: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]

[PreviewLink]: http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Типы данных: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]

[DataTypesLink]: http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Ниже представлен подробный поток слева направо, из которого клиент вводит инструкцию OData (вызов веб-службы поставщика содержимого) для получения данных и результатов:

  ![рисунок](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## Основы языка CSDL
CSDL (язык определения концептуальной схемы) — это спецификация, определяющая, как описывать веб-службу или службу базы данных в общих формулировках XML в Azure Marketplace. CSDL описывает важные части, которые **позволяют передавать данные из источника данных в Azure Marketplace.** Ниже описаны основные части.

* Сведения интерфейса, описывающие все общедоступные функции (узел FunctionImport)
* Сведения о типах данных для всех сообщений requests(input) и сообщений responses(outputs) (узлы EntityContainer, EntitySet и EntityType)
* Сведения об используемом протоколе транспорта для привязки (узел Header)
* Адресная информация для поиска указанной службы (атрибут BaseURI)

По сути CSDL представляет собой независимый от платформы и языка контракт между запрашивающим службу объектом и поставщиком службы. С помощью языка CSDL клиент может найти веб-службу или службу базы данных и вызвать любую из ее публично доступных функций.

### Связь CSDL с базой данных или коллекцией
**Спецификация языка CSDL**

CSDL — это грамматика XML для описания веб-служб. Сама спецификация состоит из 4 основных элементов: EntitySet, FunctionImport, NameSpace и EntityType.

Чтобы эту абстракцию было легче понять, сопоставим CSDL с таблицей.

Помните!

  Язык CSDL представляет собой независимый от платформы и языка контракт между **запрашивающим службу объектом** и **поставщиком службы**. С помощью языка CSDL **клиент** может найти **веб-службу или службу базы данных** и вызвать любую из ее публично доступных **функций**.

Для службы данных четыре части языка CSDL можно рассматривать как базу данных, таблицу, столбец и хранимую процедуру.

Они соотносятся следующим образом:

* EntityContainer ~= База данных
* EntitySet ~= Таблица
* EntityType ~= Столбцы
* FunctionImport ~= Хранимая процедура

**Допускаются команды HTTP**

* GET — возвращает значения из базы данных (возврат коллекции).
* POST — используется для передачи данных и необязательных возвращаемых значений из базы данных (создание новой записи в коллекции, возврат идентификатора или URI).
* DELETE – удаляет данные из базы данных (удаление коллекции).
* PUT – обновляет данные в базе данных (замена или создание коллекции).

## Документ метаданных и сопоставления
Документ метаданных и сопоставления используется для сопоставления существующих веб-служб поставщика содержимого, чтобы их можно было представить как веб-службы OData в системе Azure Marketplace. Он основан на языке CSDL и реализует несколько расширений языка CSDL для удовлетворения потребностей веб-служб на основе REST, доступных через Azure Marketplace. Расширения можно найти в пространстве имен [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04).

Ниже приведен пример CSDL (скопируйте и вставьте приведенный ниже пример CSDL в редактор XML и измените в соответствии с вашей службой. Затем вставьте в сопоставление CSDL на вкладке DataService при создании службы на [портале публикации Azure Marketplace](https://publish.windowsazure.com)).

**Термины**: о соответствии терминов CSDL и пользовательского интерфейса [портала публикации](https://publish.windowsazure.com) (PPUI).

* Заголовок Offer в PPUI соответствует MyWebOffer.
* MyCompany в PPUI соответствует **отображаемому имени издателя** в пользовательском интерфейсе [центра разработчиков Microsoft](http://dev.windows.com/registration?accountprogram=azure).
* API соответствует службе данных или веб-службе (плану в PPUI).

**Иерархия.** Компания (поставщик содержимого) владеет предложениями, которые включают планы, а именно службы, которые адаптированы к API.

### Пример WebService CSDL
Подключается к службе, которая предоставляет конечную точку веб-приложения (например, приложения на C#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Просмотрите дополнительные примеры веб-службы CSDL в статье [Примеры сопоставления существующей веб-службы и OData через CSDL](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### Пример DataService CSDL
Подключается к службе, которая предоставляет таблицу или представление базы данных как конечную точку. Ниже в примере представлены два API для базы данных на основе CSDL API (могут использовать скорее представления, чем таблицы).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## См. также
* Если вы заинтересованы в изучении и понимании конкретных узлов и их параметров, в статье [Узлы сопоставления службы данных OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) вы найдете определения и объяснения, примеры и контекст вариантов использования.
* Если вы хотите поработать с примерами, см. статью [Примеры сопоставления OData для службы данных](marketplace-publishing-data-service-creation-odata-mapping-examples.md), где приводятся примеры кода вместе с объяснением синтаксиса и контекста.
* Чтобы вернуться к указанному пути для публикации службы данных в Azure Marketplace, см. статью [Руководство по публикации службы данных](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_0831_2016-->
<properties 
	pageTitle="Обзор шаблонов лицензий PlayReady служб мультимедиа" 
	description="В этом разделе содержится обзор шаблона лицензий PlayReady, который используется для настройки лицензий PlayReady." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Обзор шаблонов лицензий PlayReady служб мультимедиа

Службы мультимедиа Azure теперь обеспечивают доставку лицензий Microsoft PlayReady. Когда проигрыватель конечного пользователя (например, Silverlight) пытается воспроизвести содержимое, защищенное с помощью PlayReady, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утверждает запрос, она выдает лицензию, которая отправляется клиенту и может использоваться для расшифровки и воспроизведения указанного содержимого.

Также службы мультимедиа предоставляют интерфейсы API, которые позволяют настраивать лицензии PlayReady. Лицензии определяют права и ограничения, которые должны применяться в среде выполнения PlayReady DRM при попытке пользователя воспроизвести защищенное содержимое. Ниже приведены некоторые примеры лицензионных ограничений PlayReady, которые вы можете указать.

- Значение даты и времени (DateTime) начала действия лицензии.
- Значение даты и времени (DateTime) окончания действия лицензии. 
- Для лицензии, которая будет храниться в постоянном хранилище на стороне клиента. Постоянные лицензии обычно используются для разрешения автономного воспроизведения содержимого.
- Минимальный уровень безопасности, необходимый для воспроизведения содержимого проигрывателем. 
- Уровень защиты выходных данных для элементов управления выводом аудио- или видеосодержимого. 
- Дополнительные сведения см. в разделе "Элементы управления выходными данными" (3.5) документа [Правила соответствия PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

>[AZURE.NOTE]В настоящее время можно настроить только право PlayRight лицензии PlayReady (это право является обязательным). Право PlayRight дает клиенту возможность воспроизводить содержимое. Также PlayRight позволяет настраивать ограничения, касающиеся воспроизведения. Дополнительные сведения см. в разделе [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).

Чтобы настроить лицензии PlayReady с помощью служб мультимедиа, необходимо настроить шаблон лицензии PlayReady служб мультимедиа. Шаблон определяется в XML-коде.

В следующем примере показан самый простой (и наиболее распространенный) шаблон, который позволяет настроить базовую лицензию на потоковую передачу. С этой лицензией клиенты могут воспроизводить защищенное содержимое PlayReady.
	
	<?xml version="1.0" encoding="utf-8"?>
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" 
	                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <PlayRight />
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

XML-код соответствует XML-схеме шаблона лицензий PlayReady, определенной в разделе XML-схемы шаблона лицензий PlayReady.

Службы мультимедиа также определяют набор классов .NET, которые могут использоваться для сериализации в XML и десериализации из XML. Описание основных классов, , которые используются для настройки шаблонов лицензий, см. в разделе [Классы .NET служб мультимедиа]((media-services-playready-license-template-overview.md#classes).

Комплексный пример использования классов .NET для настройки шаблона лицензии PlayReady см. в разделе [Использование динамического шифрования PlayReady и службы доставки лицензий](https://msdn.microsoft.com/library/azure/dn783467.aspx).

##<a id="classes"></a>Классы .NET служб мультимедиа, используемые для настройки шаблонов лицензий

Ниже перечислены основные классы .NET, которые используются для настройки шаблонов лицензий PlayReady служб мультимедиа. Эти классы сопоставляются с типами, определенными в [XML-схеме шаблона лицензий PlayReady](media-services-playready-license-template-overview.md#schema).

Класс [MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) используется для сериализации шаблона лицензии служб мультимедиа в XML-код и десериализации из XML-кода.

###PlayReadyLicenseResponseTemplate

[PlayReadyLicenseResponseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx) — этот класс представляет шаблон для ответа, отправляемого конечному пользователю. Он содержит поле для строки пользовательских данных, используемых сервером лицензий и приложением (может быть полезно для настраиваемой логики приложения), а также список из одного или нескольких шаблонов лицензий.

Это класс верхнего уровня в иерархии шаблонов. Это означает, что шаблон ответа включает список шаблонов лицензий, а шаблоны лицензий содержат (напрямую или косвенно) все прочие классы, составляющие сериализуемый шаблон данных.


###PlayReadyLicenseTemplate

[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx) — этот класс представляет шаблон лицензии для создания лицензий PlayReady, которые будут возвращены конечным пользователям. Он содержит данные о ключе содержимого в лицензии и возможные права или ограничения, которые должны применяться средой выполнения DRM PlayReady при использовании данного ключа содержимого.


###<a id="PlayReadyPlayRight"></a>PlayReadyPlayRight

[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx) — этот класс представляет право PlayRight лицензии PlayReady. Он предоставляет пользователю возможность воспроизводить содержимое с ограничениями и без, что указано в лицензии и самом праве PlayRight (для политики, касающейся воспроизведения). Большая часть политики в PlayRight связана с ограничениями выходных данных. Эти ограничения определяют типы выходных данных, в формате которых возможно воспроизведение содержимого. А также возможные ограничения, которые должны быть установлены при использовании заданных выходных данных. Например, если включено ограничение DigitalVideoOnlyContentRestriction, среда выполнения DRM будет разрешать отображение видео только через цифровые выходы (на аналоговые видеовыходы содержимое нельзя будет передавать).

>[AZURE.IMPORTANT]Эти типы ограничений могут быть очень мощными, но также могут негативно влиять на возможности, доступные для пользователя. Если указаны слишком строгие правила защиты выходных данных, содержимое может не воспроизводиться на некоторых клиентах. Дополнительные сведения см. в документе [Правила соответствия PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

Пример уровней защиты, которые поддерживает Silverlight, см. в разделе [Поддержка защиты выходных данных в Silverlight](http://go.microsoft.com/fwlink/?LinkId=617318).

##<a id="schema"></a>XML-схема шаблона лицензий PlayReady
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
	  <xs:complexType name="AgcAndColorStripeRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
	  <xs:simpleType name="ContentType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Unspecified" />
	      <xs:enumeration value="UltravioletDownload" />
	      <xs:enumeration value="UltravioletStreaming" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
	  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
	  <xs:complexType name="PlayReadyContentKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	  <xs:complexType name="ContentEncryptionKeyFromHeader">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence />
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
	  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence>
	          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
	  <xs:complexType name="PlayReadyLicenseResponseTemplate">
	    <xs:sequence>
	      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
	  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	  <xs:complexType name="PlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
	      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
	      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	  <xs:simpleType name="PlayReadyLicenseType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Nonpersistent" />
	      <xs:enumeration value="Persistent" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
	  <xs:complexType name="PlayReadyPlayRight">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	  <xs:simpleType name="UnknownOutputPassingOption">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="NotAllowed" />
	      <xs:enumeration value="Allowed" />
	      <xs:enumeration value="AllowedWithVideoConstriction" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
	  <xs:complexType name="ScmsRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
	</xs:schema>



##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->
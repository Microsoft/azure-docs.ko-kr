---
title: 서로 다른 프로토콜을 사용하는 앱 간의 이벤트 교환 - Azure Event Hubs| Microsoft Docs
description: 이 문서에서는 서로 다른 프로토콜(AMQP, Apache Kafka 및 HTTPS)을 사용하는 소비자와 생산자가 Azure Event Hubs를 사용할 때 이벤트를 교환하는 방법을 보여 줍니다.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821777"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>서로 다른 프로토콜(AMQP, Kafka 및 HTTPS)을 사용하는 소비자와 생산자 간의 이벤트 교환입니다.
Azure Event Hubs는 소비자와 생산자를 위한 세 가지 프로토콜 (AMQP, Kafka 및 HTTPS)을 지원합니다. 이러한 프로토콜 각각에는 메시지를 표현하는 자체의 고유한 방법이 있으므로 당연히 제기되는 질문은 다음과 같습니다. 애플리케이션에서 하나의 프로토콜을 사용하여 Event Hub에 이벤트를 보내고 다른 프로토콜을 사용하여 이벤트를 소비하는 경우 소비자에게 도착할 때 이벤트의 다양한 부분과 값은 어떻게 보일까요? 이 문서에서는 이벤트 내의 값이 소비 애플리케이션에서 올바르게 해석되도록 하는 생산자와 소비자 모두에 대한 모범 사례를 설명합니다.

이 문서에서 권장하는 경우 다음 클라이언트에 대해 구체적으로 설명하며, 코드 조각 개발에 사용된 버전도 나와 있습니다.

* Kafka Java 클라이언트(https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)의 버전 1.1.1)
* Java용 Microsoft Azure Event Hubs 클라이언트(https://github.com/Azure/azure-event-hubs-java)의 버전 1.1.0)
* .NET용 Microsoft Azure Event Hubs 클라이언트(https://github.com/Azure/azure-event-hubs-dotnet)의 버전 2.1.0)
* Microsoft Azure Service Bus(https://www.nuget.org/packages/WindowsAzure.ServiceBus)의 버전 5.0.0)
* HTTPS(생산자만 지원)

다른 AMQP 클라이언트는 약간 다르게 동작할 수 있습니다. AMQP는 잘 정의된 형식 시스템을 갖추고 있지만, 클라이언트에서 AMQP 메시지의 부분에 대한 액세스를 제공하는 방법을 수행하는 것처럼 해당 형식 시스템과의 언어별 형식 직렬화에 대한 세부 정보는 클라이언트에 따라 다릅니다.

## <a name="event-body"></a>이벤트 본문
모든 Microsoft AMQP 클라이언트는 이벤트 본문을 해석되지 않은 바이트 모음으로 나타냅니다. 생산 애플리케이션은 바이트 시퀀스를 클라이언트에 전달하고, 소비 애플리케이션은 이 클라이언트로부터 동일한 시퀀스를 받습니다. 바이트 시퀀스의 해석은 애플리케이션 코드 내에서 수행됩니다.

HTTPS를 통해 이벤트를 보낼 때 이벤트 본문은 게시된(POST) 내용이며, 해석되지 않은 바이트로도 처리됩니다. 다음 코드와 같이 제공된 ByteArraySerializer 및 ByteArrayDeserializer를 사용하여 Kafka 생산자 또는 소비자에서 동일한 상태를 쉽게 달성할 수 있습니다.

### <a name="kafka-byte-producer"></a>Kafka byte[] 생산자

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] 소비자
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

이 코드는 애플리케이션의 두 반쪽 간에 투명한 바이트 파이프라인을 만들며, 애플리케이션 개발자가 예를 들어 이벤트의 사용자 설정 속성에 있는 형식 또는 발신자 정보를 기반으로 하여 런타임에서의 역직렬화 결정을 포함하여 원하는 방식으로 수동으로 직렬화 및 역직렬화할 수 있도록 합니다.

고정된 단일 이벤트 본문 유형이 있는 애플리케이션은 다른 Kafka 직렬 변환기(serializer)와 역직렬 변환기(deserializer)를 사용하여 데이터를 투명하게 변환할 수 있습니다. 예를 들어 JSON을 사용하는 애플리케이션을 생각해 보세요. JSON 문자열의 구성 및 해석은 애플리케이션 수준에서 수행됩니다. Event Hubs 수준에서 이벤트 본문은 항상 문자열이며, UTF-8 인코딩의 문자를 나타내는 바이트 시퀀스입니다. 이 경우, Kafka 생산자 또는 소비자는 다음 코드와 같이 제공된 StringSerializer 또는 StringDeserializer를 활용할 수 있습니다.

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 문자열 생산자
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 문자열 소비자
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

AMQP 쪽의 경우 Java 및 .NET은 모두 문자열과 UTF-8 바이트 시퀀스 간에 변환하는 방법을 기본적으로 제공합니다. Microsoft AMQP 클라이언트는 이벤트를 EventData라는 클래스로 나타냅니다. 다음 예제에서는 AMQP 생산자에서 UTF-8 문자열을 EventData 이벤트 본문으로 직렬화하는 방법과 AMQP 소비자에서 EventData 이벤트 본문을 UTF-8 문자열로 역직렬화하는 방법을 보여 줍니다.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 문자열 생산자
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 문자열 소비자
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 문자열 생산자
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 문자열 소비자
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Kafka는 오픈 소스이므로 애플리케이션 개발자는 모든 직렬 변환기 또는 역직렬 변환기의 구현을 검사하고, AMQP 쪽에서 호환되는 바이트 시퀀스를 생산하거나 소비하는 코드를 구현할 수 있습니다.

## <a name="event-user-properties"></a>이벤트 사용자 속성

사용자 설정 속성은 AMQP 클라이언트(Microsoft AMQP 클라이언트에서는 속성이라고 함)와 Kafka(헤더라고 함) 모두에서 설정하고 검색할 수 있습니다. HTTPS 발신자는 POST 작업에서 사용자 속성을 HTTP 헤더로 제공하여 이벤트에 대한 사용자 속성을 설정할 수 있습니다. 그러나 Kafka는 이벤트 본문과 이벤트 헤더 값을 모두 바이트 시퀀스로 처리합니다. 반면 AMQP 클라이언트의 속성 값에는 AMQP 형식 시스템에 따라 속성 값을 인코딩하여 전달되는 형식이 있습니다.

HTTPS는 특별한 경우입니다. 발신 시점에서 모든 속성 값은 UTF-8 텍스트입니다. Event Hubs 서비스는 제한된 양의 해석을 수행하여 적절한 속성 값을 AMQP 인코딩 32비트 및 64비트 부호 있는 정수, 64비트 부동 소수점 수 및 부울로 변환합니다. 이러한 형식 중 하나에 맞지 않는 속성 값은 문자열로 처리됩니다.

속성 형식화에 이러한 접근 방식을 혼합하면 Kafka 소비자에서 AMQP 형식 정보를 포함하여 원시 AMQP 인코딩 바이트 시퀀스를 볼 수 있습니다. 반면 AMQP 소비자는 Kafka 생산자에서 보낸 형식화되지 않은 바이트 시퀀스를 볼 수 있지만, 이는 애플리케이션에서 해석해야 합니다.

AMQP 또는 HTTPS 생산자로부터 속성을 받는 Kafka 소비자의 경우 Kafka 에코시스템의 다른 역직렬 변환기를 모델로 하는 AmqpDeserializer 클래스를 사용합니다. 이 클래스는 AMQP 인코딩 바이트 시퀀스의 형식 정보를 해석하여 데이터 바이트를 Java 형식으로 역직렬화합니다.

AMQP 또는 HTTPS를 통해 전송되는 메시지에 속성을 포함하는 것이 가장 좋습니다. Kafka 소비자는 이를 사용하여 헤더 값에 AMQP 역직렬화가 필요한지 여부를 결정할 수 있습니다. 속성의 값은 중요하지 않습니다. Kafka 소비자가 헤더 목록에서 찾을 수 있고 이에 따라 동작을 조정할 수 있는 잘 알려진 이름만 있으면 됩니다.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP-Kafka 파트 1: C#(.NET)에서 속성이 포함된 이벤트 만들기 및 보내기
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP-Kafka 파트 2: AmqpDeserializer를 사용하여 Kafka 소비자에서 해당 속성 역직렬화
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

애플리케이션에서 예상되는 속성 형식을 알고 있는 경우 나중에 캐스팅할 필요가 없는 역직렬화 메서드가 있지만 속성이 예상되는 형식이 아니면 오류를 throw합니다.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP-Kafka 파트 3: Kafka 소비자에서 다른 방식으로 AmqpDeserializer 사용
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Kafka 생산자에서 설정된 헤더는 AMQP 소비자에서 항상 원시 바이트(Java용 Microsoft Azure Event Hubs 클라이언트의 경우 org.apache.qpid.proton.amqp.Binary 형식, Microsoft .NET AMQP 클라이언트의 경우 `System.Byte[]`)로 간주하므로 다른 방향으로 이동하는 것이 더 복잡합니다. 가장 쉬운 방향은 Kafka 제공 직렬 변환기 중 하나를 사용하여 Kafka 생산자 쪽에서 헤더 값에 대한 바이트를 생성한 다음, AMQP 소비자 쪽에서 호환되는 역직렬화 코드를 작성하는 것입니다.

AMQP-Kafka와 마찬가지로, 권장되는 가장 좋은 방법은 Kafka를 통해 전송된 메시지에 속성을 포함하는 것입니다. AMQP 소비자는 이 속성을 사용하여 헤더 값을 역직렬화해야 하는지 여부를 결정할 수 있습니다. 속성의 값은 중요하지 않습니다. AMQP 소비자가 헤더 목록에서 찾을 수 있고 이에 따라 동작을 조정할 수 있는 잘 알려진 이름만 있으면 됩니다. Kafka 생산자를 변경할 수 없으면 소비 애플리케이션에서 속성 값이 이진 또는 바이트 형식인지 확인하고 형식에 따라 역직렬화를 시도할 수도 있습니다.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka-AMQP 파트 1: Kafka에서 속성이 포함된 이벤트 만들기 및 보내기
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka-AMQP 파트 2: C#(.NET)에서 수동으로 해당 속성 역직렬화
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka-AMQP 파트 3: Java에서 수동으로 해당 속성 역직렬화
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Kafka 지원 Event Hubs로 스트리밍하는 방법을 배웠습니다. Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

* [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
* [Kafka용 Event Hubs에 대해 알아보기](event-hubs-for-kafka-ecosystem-overview.md)
* [Kafka GitHub용 Event Hubs에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [온-프레미스 Kafka에서 클라우드의 Kafka 지원 Event Hubs로 이벤트를 스트림합니다.](event-hubs-kafka-mirror-maker-tutorial.md)
* [네이티브 Kafka 애플리케이션](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md) 또는 [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)를 사용하여 Kafka 지원 Event Hubs에 스트림하는 방법을 알아봅니다.

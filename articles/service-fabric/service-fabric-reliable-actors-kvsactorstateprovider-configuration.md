---
title: Azure 서비스 패브릭 행위자에서 KVSActorStateProvider 설정 변경 | Microsoft Docs
description: "'KVSActorStateProvider' 형식의 Azure 서비스 패브릭 상태 저장 행위자를 구성하는 방법에 대해 알아봅니다."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 8b10ef18fd389179a4f5422783606c45fa2e0d32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728052"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Reliable Actors 구성--KVSActorStateProvider
KVSActorStateProvider의 기본 구성은 지정된 행위자에 대해 Microsoft Visual Studio 패키지 루트의 Config 폴더에 생성된 settings.xml 파일을 변경하여 수정할 수 있습니다.

Azure 서비스 패브릭 런타임은 settings.xml 파일에서 미리 정의된 섹션 이름을 찾아서 기본 런타임 구성 요소를 만드는 동안 해당 구성 값을 사용합니다.

> [!NOTE]
> Visual Studio 솔루션에서 생성된 settings.xml 파일에서 다음 구성의 섹션 이름을 삭제 또는 수정하지 **않도록** 합니다.
> 
> 

## <a name="replicator-security-configuration"></a>복제자 보안 구성
복제자 보안 구성은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 따라서 서비스는 서로의 복제 트래픽을 볼 수 없으므로 항상 사용 가능하게 설정한 데이터를 안전하게 보호할 수 있습니다.
기본적으로 빈 보안 구성 섹션에서는 복제 보안이 되지 않습니다.

> [!IMPORTANT]
> Linux 노드에서 인증서는 PEM 형식이어야 합니다. Linux에서 인증서 찾기 및 구성에 대해 자세히 알아보려면 [Linux에서 인증서 구성](./service-fabric-configure-certificates-linux.md)을 참조하세요. 
> 

### <a name="section-name"></a>섹션 이름
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>복제자 구성
복제자 구성은 행위자 상태 제공자의 상태를 매우 안정적으로 만드는 작업을 담당하는 복제자를 구성합니다.
기본 구성은 Visual Studio 템플릿에 의해 생성되며 충분해야 합니다. 이 섹션에서는 복제자 조정에 사용할 수 있는 추가 구성에 대해 설명합니다.

### <a name="section-name"></a>섹션 이름
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>구성 이름
| 이름 | 단위 | 기본값 | 설명 |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |초 |0.015 |작업을 수신한 후 주 복제본에 대한 승인을 다시 보내기 전에 보조 복제본의 복제자가 대기하는 시간. 이 간격 내에서 처리하는 작업에 대해 보낼 나머지 승인은 모두 하나의 응답으로 전송됩니다. |
| ReplicatorEndpoint |N/A |기본값 없음--필수 매개 변수 |주/보조 복제자가 복제본 세트의 다른 복제자와 통신하는 데 사용할 IP 주소 및 포트. 서비스 매니페스트의 TCP 리소스 엔드포인트를 참조해야 합니다. 서비스 매니페스트에서 엔드포인트 리소스를 정의하는 방법에 대한 자세한 내용은 [서비스 매니페스트 리소스](service-fabric-service-manifest-resources.md) 를 참조하세요. |
| RetryInterval |초 |5 |작업에 대한 승인을 받지 못한 경우 복제자가 메시지를 다시 전송한 후의 시간 간격. |
| MaxReplicationMessageSize |바이트 |50MB |단일 메시지에서 전송할 수 있는 복제 데이터의 최대 크기. |
| MaxPrimaryReplicationQueueSize |작업의 수 |1024 |기본 큐의 최대 작업 수. 작업은 주 복제자가 모든 보조 복제자로부터 승인을 받은 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다. |
| MaxSecondaryReplicationQueueSize |작업의 수 |2048 |보조 큐의 최대 작업 수. 작업은 지속성을 통해 상태를 항상 사용 가능하도록 설정한 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다. |

## <a name="store-configuration"></a>저장소 구성
저장소 구성은 복제 중인 상태를 유지하는 데 사용되는 로컬 저장소를 구성하는 데 사용됩니다.
기본 구성은 Visual Studio 템플릿에 의해 생성되며 충분해야 합니다. 이 섹션에서는 로컬 저장소 조정에 사용할 수 있는 추가 구성에 대해 설명합니다.

### <a name="section-name"></a>섹션 이름
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>구성 이름
| 이름 | 단위 | 기본값 | 설명 |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |밀리초 |200 |지속형 로컬 저장소 커밋에 대한 최대 배치 간격을 설정합니다. |
| MaxVerPages |페이지 수 |16384 |로컬 저장소 데이터베이스의 최대 버전 페이지 수. 처리되지 않은 트랜잭션의 최대 수를 결정합니다. |

## <a name="sample-configuration-file"></a>샘플 구성 파일
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>설명
BatchAcknowledgementInterval 매개 변수는 복제 지연을 제어합니다. '0' 값은 처리량을 희생하여 가장 낮은 대기 시간을 제공합니다(더 많은 승인 메시지를 보내고 처리해야 하므로 각각에 포함된 승인은 적음).
BatchAcknowledgementInterval의 값이 클수록 전체적인 복제 처리량은 높아지고 작업 대기 시간은 더욱 길어집니다. 이 값은 트랜잭션 커밋의 대기 시간으로 직접 변환됩니다.


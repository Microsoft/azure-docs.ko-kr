---
title: Azure Service Fabric 컨테이너 애플리케이션 매니페스트 예제 | Microsoft Docs
description: Service Fabric 애플리케이션에 대한 애플리케이션 및 서비스 매니페스트 설정을 구성하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 85a3066095cfc30da19b06d26f41bdc156f85832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718226"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric 애플리케이션 및 서비스 매니페스트 예제
이 섹션에는 애플리케이션 및 서비스 매니페스트 예제가 포함되어 있습니다. 이러한 예제의 목적은 중요한 시나리오를 보여주는 것아 아니라 사용 가능한 다양한 설정과 사용 방법을 보여주는 것입니다. 

다음은 표시되는 기능의 인덱스와 기능이 포함된 예제 매니페스트입니다.

|기능|매니페스트|
|---|---|
|[리소스 관리](service-fabric-resource-governance.md)|[Reliable Services 응용 프로그램 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [컨테이너 응용 프로그램 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[로컬 관리자 계정으로 서비스 실행](service-fabric-application-runas-security.md)|[Reliable Services 애플리케이션 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[모든 서비스 코드 패키지에 기본 정책 적용](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services 애플리케이션 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[사용자 및 그룹 보안 주체 만들기](service-fabric-application-runas-security.md)|[Reliable Services 애플리케이션 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|서비스 인스턴스 간에 데이터 패키지 공유|[Reliable Services 애플리케이션 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[서비스 엔드포인트 재정의](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services 애플리케이션 매니페스트](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[서비스 시작 시 스크립트 실행](service-fabric-run-script-at-service-startup.md)|[VotingWeb 서비스 매니페스트](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS 엔드포인트 정의](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb 서비스 매니페스트](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[구성 패키지 선언](service-fabric-application-and-service-manifests.md)|[VotingData 서비스 매니페스트](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[데이터 패키지 선언](service-fabric-application-and-service-manifests.md)|[VotingData 서비스 매니페스트](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[환경 변수 재정의](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[컨테이너 애플리케이션 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[컨테이너 포트-호스트 매핑 구성](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [컨테이너 애플리케이션 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[컨테이너 레지스트리 인증 구성](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[컨테이너 애플리케이션 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[격리 모드 설정](service-fabric-get-started-containers.md#configure-isolation-mode)|[컨테이너 애플리케이션 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[OS 빌드 관련 컨테이너 이미지 지정](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[컨테이너 애플리케이션 매니페스트](service-fabric-manifest-example-container-app.md#application-manifest)|
|[환경 변수 설정](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[컨테이너 FrontEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [컨테이너 BackEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[엔드포인트 구성](service-fabric-get-started-containers.md#configure-communication)|[컨테이너 FrontEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [컨테이너 BackEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData 서비스 매니페스트](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|컨테이너에 명령 전달|[컨테이너 FrontEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[컨테이너로 인증서 가져오기](service-fabric-securing-containers.md)|[컨테이너 FrontEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[볼륨 드라이버 구성](service-fabric-containers-volume-logging-drivers.md)|[컨테이너 BackEndService 서비스 매니페스트](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|


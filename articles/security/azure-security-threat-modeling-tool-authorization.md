---
title: 권한 부여 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 3c078f7246140ee966f1d202d2248758dde49059
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121524"
---
# <a name="security-frame-authorization--mitigations"></a>보안 프레임: 권한 부여 | 완화 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **컴퓨터 신뢰 경계** | <ul><li>[적절한 ACL이 디바이스의 데이터에 대한 무단 액세스를 제한하도록 구성되어 있는지 확인](#acl-restricted-access)</li><li>[사용자 고유의 중요한 애플리케이션 콘텐츠가 사용자 프로필 디렉터리에 저장되는지 확인](#sensitive-directory)</li><li>[배포된 애플리케이션이 최소 권한으로 실행되는지 확인](#deployed-privileges)</li></ul> |
| **웹 애플리케이션** | <ul><li>[비즈니스 논리 흐름을 처리할 때 순차적 단계 순서 적용](#sequential-logic)</li><li>[열거를 방지하는 비율 제한 메커니즘 구현](#rate-enumeration)</li><li>[적절한 권한이 부여되고 최소 권한의 원칙이 준수되는지 확인](#principle-least-privilege)</li><li>[들어오는 요청 매개 변수를 기반으로 하지 않는 비즈니스 논리 및 리소스 액세스 권한 부여 결정](#logic-request-parameters)</li><li>[강제 검색을 통해 콘텐츠와 리소스를 열거하거나 액세스할 수 없는지 확인](#enumerable-browsing)</li></ul> |
| **데이터베이스** | <ul><li>[최소 권한의 계정으로 데이터베이스 서버에 연결하는지 확인](#privileged-server)</li><li>[테넌트에서 다른 테넌트의 데이터에 액세스하지 못하도록 방지하는 RLS(행 수준 보안) 구현](#rls-tenants)</li><li>[유효한 필수 사용자에게만 부여되는 sysadmin 역할](#sysadmin-users)</li></ul> |
| **IoT 클라우드 게이트웨이** | <ul><li>[최소 권한의 토큰을 사용하여 클라우드 게이트웨이에 연결](#cloud-least-privileged)</li></ul> |
| **Azure 이벤트 허브** | <ul><li>[송신 전용 권한 SAS 키를 사용하여 디바이스 토큰 생성](#sendonly-sas)</li><li>[이벤트 허브에 직접 액세스할 수 있는 액세스 토큰 사용 금지](#access-tokens-hub)</li><li>[최소 권한이 필요한 SAS 키를 사용하여 이벤트 허브에 연결](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[가능한 경우 리소스 토큰을 사용하여 Azure Cosmos DB에 연결](#resource-docdb)</li></ul> |
| **Azure 신뢰 경계** | <ul><li>[RBAC(역할 기반 액세스 제어)를 사용하여 Azure 구독에 대해 세분화된 액세스 관리를 사용하도록 설정](#grained-rbac)</li></ul> |
| **Service Fabric 신뢰 경계** | <ul><li>[RBAC를 사용하여 클러스터 작업에 대한 클라이언트 액세스 제한](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[보안 모델링 수행 및 필요한 경우 필드 수준 보안 사용](#modeling-field)</li></ul> |
| **Dynamics CRM 포털** | <ul><li>[포털의 보안 모델이 CRM의 나머지 부분과 다르다는 점을 감안하여 포털 계정의 보안 모델링 수행](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Azure Table Storage의 엔터티 범위에 대해 세분화된 권한 부여](#permission-entities)</li><li>[Azure Resource Manager를 사용하여 Azure 저장소 계정에 역할 기반 Access Control(RBAC)을 사용하도록 설정](#rbac-azure-manager)</li></ul> |
| **모바일 클라이언트** | <ul><li>[암시적 무단 해제 또는 루팅 검색 구현](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[WCF - 약한 클래스 참조](#weak-class-wcf)</li><li>[WCF - 권한 부여 제어 구현](#wcf-authz)</li></ul> |
| **앱 API** | <ul><li>[ASP.NET Web API에서 적절한 권한 부여 메커니즘 구현](#authz-aspnet)</li></ul> |
| **IoT 디바이스** | <ul><li>[다른 권한 수준이 필요한 다양한 작업을 지원하는 경우 디바이스에서 권한 부여 확인 수행](#device-permission)</li></ul> |
| **IoT 필드 게이트웨이** | <ul><li>[다른 권한 수준이 필요한 다양한 작업을 지원하는 경우 필드 게이트웨이에서 권한 부여 확인 수행](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>적절한 ACL이 디바이스의 데이터에 대한 무단 액세스를 제한하도록 구성되어 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 적절한 ACL이 디바이스의 데이터에 대한 무단 액세스를 제한하도록 구성되어 있는지 확인합니다.|

## <a id="sensitive-directory"></a>사용자 고유의 중요한 애플리케이션 콘텐츠가 사용자 프로필 디렉터리에 저장되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 사용자 고유의 중요한 애플리케이션 콘텐츠가 사용자 프로필 디렉터리에 저장되는지 확인합니다. 이렇게 하면 컴퓨터의 여러 사용자가 다른 사용자의 데이터에 액세스하지 못하도록 방지할 수 있습니다.|

## <a id="deployed-privileges"></a>배포된 애플리케이션이 최소 권한으로 실행되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 컴퓨터 신뢰 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 배포된 애플리케이션이 최소 권한으로 실행되는지 확인합니다. |

## <a id="sequential-logic"></a>비즈니스 논리 흐름을 처리할 때 순차적 단계 순서 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 진정한 사용자가 이 단계를 실행했는지 확인하려면 순서대로 처리되지 않거나 건너뛴 단계, 다른 사용자가 처리한 단계 또는 너무 빨리 제출된 트랜잭션을 처리하지 않고, 현실적인 인간의 시간에서 모든 단계를 처리하는 순차적 단계 순서로만 비즈니스 논리 흐름을 처리하도록 애플리케이션을 적용하려고 합니다.|

## <a id="rate-enumeration"></a>열거를 방지하는 비율 제한 메커니즘 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 중요한 식별자가 임의적인 식별자인지 확인합니다. 익명 페이지에 대해 CAPTCHA 제어를 구현합니다. 오류 및 예외로 인해 특정 데이터가 표시되지 않아야 합니다.|

## <a id="principle-least-privilege"></a>적절한 권한이 부여되고 최소 권한의 원칙이 준수되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>사용자 작업에 필수적인 권한만 사용자 계정에 부여한다는 것이 원칙입니다. 예를 들어 백업 사용자는 소프트웨어를 설치할 필요가 없습니다. 따라서 백업 사용자에게는 백업 및 백업 관련 애플리케이션만 실행할 수 있는 권한이 있습니다. 새 소프트웨어 설치와 같은 다른 모든 권한은 차단됩니다. 이 원칙은 일반 사용자 계정에서 작업하는 개인용 컴퓨터 사용자에게도 일반적으로 적용되며, 반드시 요구되는 경우에만 권한이 있는 암호로 보호된 계정(슈퍼 사용자)을 엽니다. </p><p>또한 이 원칙은 웹 응용 프로그램에도 적용할 수 있습니다. 세션을 사용하는 역할 기반 인증 방법에만 의존하는 대신 데이터베이스 기반 인증 시스템을 통해 사용자에게 권한을 할당하려고 합니다. 사용자가 시스템에서 수행할 권한이 있는 작업을 확인하기 위해 권한을 부여한 특정 역할을 해당 사용자에게 할당하는 대신 사용자가 올바르게 로그인했는지 확인하기 위해 세션을 계속 사용합니다. 또한 이 방법의 가장 큰 이점은 사용자가 적은 권한을 할당받아야 할 때마다 할당이 먼저 만료된 세션에 의존하지 않으므로 변경 내용이 즉시 적용된다는 것입니다.</p>|

## <a id="logic-request-parameters"></a>들어오는 요청 매개 변수를 기반으로 하지 않는 비즈니스 논리 및 리소스 액세스 권한 부여 결정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 사용자가 특정 데이터를 검토하도록 제한되는지 확인할 때마다 액세스 제한이 서버 쪽에서 처리되어야 합니다. userID는 로그인에 대한 세션 변수 안에 저장해야 하며, 데이터베이스에서 사용자 데이터를 검색하는 데 사용해야 합니다. |

### <a name="example"></a>예
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
이제 데이터를 검색하기 위한 식별자를 서버 쪽에서 처리하므로 가능한 공격자가 애플리케이션 작업을 변조하거나 변경할 수 없습니다.

## <a id="enumerable-browsing"></a>강제 검색을 통해 콘텐츠와 리소스를 열거하거나 액세스할 수 없는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>중요한 정적 파일과 구성 파일을 웹 루트에 보관하면 안됩니다. 공개할 필요가 없는 콘텐츠의 경우 적절한 액세스 제어를 적용하거나 콘텐츠 자체를 제거해야 합니다.</p><p>또한 강제 검색은 일반적으로 무차별 암호 대입(Brute Force) 기술과 결합되어 가능한 한 많은 URL에 액세스하여 정보를 수집함으로써 서버의 디렉터리와 파일을 열거합니다. 공격자는 일반적으로 존재하는 파일의 모든 변형을 검사할 수 있습니다. 예를 들어 암호 파일 검색에는 passwd.txt, password.htm, password.dat 및 다른 변형 파일 등이 포함됩니다.</p><p>이 문제를 완화하려면 무차별 대입 시도를 감지하는 기능이 포함되어야 합니다.</p>|

## <a id="privileged-server"></a>최소 권한의 계정으로 데이터베이스 서버에 연결하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [SQL Database 권한 계층](https://msdn.microsoft.com/library/ms191465), [SQL 데이터베이스 보안 개체](https://msdn.microsoft.com/library/ms190401) |
| **단계** | 최소 권한이 부여된 계정을 사용하여 데이터베이스에 연결해야 합니다. 애플리케이션 로그인은 데이터베이스에서 제한되어야 하며 선택된 저장 프로시저만 실행해야 합니다. 애플리케이션애플리케이션의 로그인에는 직접적인 테이블 액세스가 없어야 합니다. |

## <a id="rls-tenants"></a>테넌트에서 다른 테넌트의 데이터에 액세스하지 못하도록 방지하는 RLS(행 수준 보안) 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure, 온-프레미스 |
| **특성**              | SQL 버전 - V12, SQL 버전 - MsSQL2016 |
| **참조**              | [SQL Server RLS(행 수준 보안)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **단계** | <p>행 수준 보안을 통해 고객은 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다.</p><p>RLS는 애플리케이션의 보안 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 자신의 부서와 관련된 데이터 행에만 액세스하거나 고객의 데이터 액세스를 회사와 관련된 데이터만으로 제한할 수 있습니다.</p><p>액세스 제한 논리는 다른 애플리케이션 계층의 데이터가 아닌 데이터베이스 계층에 있습니다. 데이터베이스 시스템은 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이렇게 하면 보안 시스템의 노출 영역을 줄임으로써 보안 시스템을 보다 안정적이고 강력하게 만들 수 있습니다.</p><p>|

기본 데이터베이스 기능인 RLS는 2016년 이후의 SQL Server 및 Azure SQL 데이터베이스에만 적용됩니다. 기본적인 RLS 기능이 구현되지 않는 경우 뷰와 프로시저를 사용하여 데이터 액세스를 제한해야 합니다.

## <a id="sysadmin-users"></a>유효한 필수 사용자에게만 부여되는 sysadmin 역할

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [SQL Database 권한 계층](https://msdn.microsoft.com/library/ms191465), [SQL 데이터베이스 보안 개체](https://msdn.microsoft.com/library/ms190401) |
| **단계** | SysAdmin 고정 서버 역할의 멤버는 매우 제한적이어야 하며 애플리케이션에서 사용하는 계정을 포함해서는 안됩니다.  역할에 포함된 사용자 목록을 검토하고 불필요한 계정을 제거합니다.|

## <a id="cloud-least-privileged"></a>최소 권한의 토큰을 사용하여 클라우드 게이트웨이에 연결

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 게이트웨이 선택 - Azure IoT Hub |
| **참조**              | [Iot Hub Access Control](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **단계** | 클라우드 게이트웨이(IoT Hub)에 연결하는 다양한 구성 요소에 대한 최소 권한을 제공합니다. 일반적인 예로 디바이스 관리/프로비전 구성 요소는 레지스트리 읽기/쓰기를 사용하고, 이벤트 프로세서(ASA)는 서비스 연결을 사용합니다. 개별 디바이스는 디바이스 자격 증명을 사용하여 연결합니다.|

## <a id="sendonly-sas"></a>송신 전용 권한 SAS 키를 사용하여 디바이스 토큰 생성

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Event Hub | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Event Hubs 인증 및 보안 모델 개요](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **단계** | SAS 키는 개별 디바이스 토큰을 생성하는 데 사용됩니다. 지정된 게시자의 디바이스 토큰을 생성하는 동안 송신 전용 권한 SAS 키를 사용합니다.|

## <a id="access-tokens-hub"></a>이벤트 허브에 직접 액세스할 수 있는 액세스 토큰 사용 금지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Event Hub | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Event Hubs 인증 및 보안 모델 개요](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **단계** | 이벤트 허브에 대한 직접 액세스 권한을 부여하는 토큰을 디바이스에 제공하면 안됩니다. 게시자에게만 액세스 권한을 부여하는 디바이스에 최소 권한의 토큰을 사용하면 악의적이거나 손상된 디바이스로 확인되는 경우 이를 식별하여 블랙리스트에 추가할 수 있습니다.|

## <a id="sas-minimum-permissions"></a>최소 권한이 필요한 SAS 키를 사용하여 이벤트 허브에 연결

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Event Hub | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Event Hubs 인증 및 보안 모델 개요](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **단계** | 이벤트 허브에 연결하는 다양한 백 엔드 애플리케이션에 최소 권한을 제공합니다. 백 엔드 애플리케이션마다 별도의 SAS 키를 생성하고 필요한 권한(송신, 수신 또는 관리)만 제공합니다.|

## <a id="resource-docdb"></a>가능한 경우 리소스 토큰을 사용하여 Cosmos DB에 연결

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Document DB | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 리소스 토큰은 Azure Cosmos DB 권한 리소스와 연관되며, 데이터베이스 사용자와 해당 사용자가 특정 Azure Cosmos DB 애플리케이션 리소스(예: 컬렉션, 문서)에 대해 갖고 있는 권한 사이의 관계를 캡처합니다. 최종 사용자 애플리케이션(예: 모바일 또는 데스크톱 클라이언트)과 같이 클라이언트에서 마스터 또는 읽기 전용 키를 처리하여 신뢰할 수 없는 경우 항상 리소스 토큰을 사용하여 Azure Cosmos DB에 액세스해야 합니다. 이러한 키를 안전하게 저장할 수 있는 백 엔드 애플리케이션에서 마스터 키 또는 읽기 전용 키를 사용합니다.|

## <a id="grained-rbac"></a>RBAC(역할 기반 액세스 제어)를 사용하여 Azure 구독에 대해 세분화된 액세스 관리를 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure 신뢰 경계 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **단계** | Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하여 사용자가 해당 작업을 수행하는 데 필요한 액세스 만큼 권한을 부여할 수 있습니다.|

## <a id="cluster-rbac"></a>RBAC를 사용하여 클러스터 작업에 대한 클라이언트 액세스 제한

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Service Fabric 트러스트 경계 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 환경 - Azure |
| **참조**              | [Service Fabric 클라이언트용 역할 기반 액세스 제어](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **단계** | <p>Azure 서비스 패브릭은 서비스 패브릭 클러스터에 연결된 클라이언트 즉, 관리자 및 사용자에 대한 액세스 제어 형식을 지원합니다. 액세스 제어를 사용하면 클러스터 관리자가 사용자 그룹마다 특정 클러스터 작업에 대한 액세스를 제한하여 클러스터의 보안을 강화할 수 있습니다.</p><p>관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다.</p><p>클러스터 생성 시 각각에 대해 개별 인증서를 제공하여 두 개의 클라이언트 역할(관리자 및 클라이언트)을 지정합니다.</p>|

## <a id="modeling-field"></a>보안 모델링 수행 및 필요한 경우 필드 수준 보안 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 보안 모델링을 수행하고 필요한 경우 필드 수준 보안을 사용합니다.|

## <a id="portal-security"></a>포털의 보안 모델이 CRM의 나머지 부분과 다르다는 점을 감안하여 포털 계정의 보안 모델링 수행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM 포털 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 포털의 보안 모델이 CRM의 나머지 부분과 다르다는 점을 감안하여 포털 계정의 보안 모델링을 수행합니다.|

## <a id="permission-entities"></a>Azure Table Storage의 엔터티 범위에 대해 세분화된 권한 부여

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | StorageType - 테이블 |
| **참조**              | [SAS를 사용하여 Azure 저장소 계정의 개체에 대한 액세스 권한을 위임하는 방법](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **단계** | 특정 비즈니스 시나리오에서 Azure Table Storage는 다른 당사자에게 제공하는 중요한 데이터(예: 다른 국가에 관한 중요한 데이터)를 저장해야 할 수 있습니다. 이러한 경우 사용자가 특정 국가의 특정 데이터에 액세스할 수 있도록 파티션 및 행 키 범위를 지정하여 SAS 서명을 구성할 수 있습니다.| 

## <a id="rbac-azure-manager"></a>Azure Resource Manager를 사용하여 Azure 저장소 계정에 역할 기반 Access Control(RBAC)을 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [RBAC(역할 기반 Access Control)를 사용하여 저장소 계정의 보안을 유지하는 방법](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **단계** | <p>새 저장소 계정을 만들 때 클래식 배포 모델 또는 Azure Resource Manager 배포 모델 중에서 선택합니다. Azure에서 리소스를 만드는 기본 모델에서만 구독 및 저장소 계정에 대한 완전한 액세스를 허용합니다.</p><p>Azure Resource Manager 모델에서는 Azure Active Directory를 사용하여 리소스 그룹에 저장소 계정을 추가하고 해당 특정 저장소 계정의 관리 평면에 대한 액세스를 제어합니다. 예를 들어 특정 사용자에게는 저장소 계정 키에 액세스할 수 있는 기능을 제공하고 다른 사용자에게는 저장소 계정에 대한 정보는 볼 수 있지만 저장소 계정 키에는 액세스하지 못하게 할 수 있습니다.</p>|

## <a id="rooting-detection"></a>암시적 무단 해제 또는 루팅 검색 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 모바일 클라이언트 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>휴대폰이 루팅되거나 무단 해제되는 경우 애플리케이션에서 자체 구성 및 사용자 데이터를 보호해야 합니다. 루팅/무단 해제는 일반 사용자가 자신의 휴대폰에서 수행하지 않는 무단 액세스를 의미합니다. 따라서 애플리케이션을 시작할 때 애플리케이션에 암시적 검색 논리가 있어야 휴대폰이 루팅되었는지 감지할 수 있습니다.</p><p>검색 논리는 일반적으로 루트 사용자만 액세스할 수 있는 파일에 간단히 액세스할 수 있습니다. 예를 들면 다음과 같습니다.</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>애플리케이션에서 이러한 파일 중 하나에 액세스할 수 있으면 애플리케이션이 루트 사용자로 실행되고 있음을 나타냅니다.</p>|

## <a id="weak-class-wcf"></a>WCF - 약한 클래스 참조

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **단계** | <p>공격자가 시스템에서 약한 클래스 참조를 사용하여 권한이 없는 코드를 실행할 수 있습니다. 프로그램은 고유하게 식별되지 않은 사용자 정의 클래스를 참조합니다. .NET에서 이처럼 약하게 식별된 클래스를 로드하면 CLR 형식 로더는 지정된 순서대로 다음 위치에 있는 클래스를 검색합니다.</p><ol><li>형식의 어셈블리를 알고 있는 경우 로더는 구성 파일의 리디렉션 위치, GAC, 구성 정보를 사용 중인 현재 어셈블리 및 애플리케이션 기본 디렉터리를 검색합니다.</li><li>어셈블리를 알고 있지 않은 경우 로더는 현재 어셈블리, mscorlib 및 TypeResolve 이벤트 처리기에서 반환한 위치를 검색합니다.</li><li>이 CLR 검색 순서는 형식 전달 메커니즘 및 AppDomain.TypeResolve 이벤트와 같은 후크를 사용하여 수정할 수 있습니다.</li></ol><p>공격자가 CLR 검색 순서를 악용하여 동일한 이름의 대체 클래스를 만들어 CLR에서 먼저 로드할 대체 위치에 배치하는 경우 CLR에서 의도한 것은 아니지만 공격자가 제공한 코드를 실행하게 됩니다.</p>|

### <a name="example"></a>예
아래 WCF 구성 파일의 `<behaviorExtensions/>` 요소는 특정 WCF 확장에 사용자 지정 동작 클래스를 추가하도록 WCF에 지시합니다.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
정규화된(강력한) 이름을 사용하면 형식을 고유하게 식별하고 시스템 보안을 더욱 강화할 수 있습니다. machine.config 및 app.config 파일에 형식을 등록할 때 정규화된 어셈블리 이름을 사용합니다.

### <a name="example"></a>예
아래 WCF 구성 파일의 `<behaviorExtensions/>` 요소는 특정 WCF 확장에 강력하게 참조되는 사용자 지정 동작 클래스를 추가하도록 WCF에 지시합니다.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>WCF - 권한 부여 제어 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **단계** | <p>이 서비스는 권한 부여 제어를 사용하지 않습니다. 클라이언트에서 특정 WCF 서비스를 호출하면 WCF는 호출자에게 서버에서 서비스 메서드를 실행할 수 있는 권한이 있는지 확인하는 다양한 권한 부여 체계를 제공합니다. WCF 서비스에 대해 권한 부여 제어를 사용할 수 없으면 인증된 사용자가 권한 에스컬레이션을 수행할 수 있습니다.</p>|

### <a name="example"></a>예
다음 구성에서는 서비스를 실행할 때 클라이언트의 권한 수준을 확인하지 않도록 WCF에 지시합니다.
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
서비스 권한 부여 체계를 사용하여 서비스 메서드의 호출자가 이렇게 수행할 수 있는 권한을 부여받았는지 확인합니다. WCF는 두 가지 모드를 제공하며 사용자 지정 권한 부여 체계를 정의할 수 있습니다. UseWindowsGroups 모드에서는 Windows 역할 및 사용자를 사용하며, UseAspNetRoles 모드에서는 SQL Server와 같은 ASP.NET 역할 공급자를 사용하여 인증합니다.

### <a name="example"></a>예
다음 구성에서는 추가 서비스를 실행하기 전에 클라이언트가 관리자 그룹에 속하는지 확인하도록 WCF에 지시합니다.
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
그러면 서비스가 다음과 같이 선언됩니다.
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>ASP.NET Web API에서 적절한 권한 부여 메커니즘 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, MVC5 |
| **특성**              | 해당 없음, ID 공급자 - ADFS, ID 공급자 - Azure AD |
| **참조**              | [ASP.NET Web API의 인증 및 권한 부여](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api)(영문) |
| **단계** | <p>애플리케이션 사용자의 역할 정보는 애플리케이션을 ID 공급자로 사용하거나 애플리케이션 자체에서 제공하는 경우 Azure AD 또는 ADFS 클레임에서 파생될 수 있습니다. 이러한 경우에는 사용자 지정 권한 부여 구현에서 사용자 역할 정보의 유효성을 검사해야 합니다.</p><p>애플리케이션 사용자의 역할 정보는 애플리케이션을 ID 공급자로 사용하거나 애플리케이션 자체에서 제공하는 경우 Azure AD 또는 ADFS 클레임에서 파생될 수 있습니다. 이러한 경우에는 사용자 지정 권한 부여 구현에서 사용자 역할 정보의 유효성을 검사해야 합니다.</p>

### <a name="example"></a>예
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
보호해야 하는 모든 컨트롤러와 작업 메서드는 위의 특성으로 데코레이팅해야 합니다.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>다른 권한 수준이 필요한 다양한 작업을 지원하는 경우 디바이스에서 권한 부여 확인 수행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>디바이스는 호출자에게 요청된 작업을 수행하는 데 필요한 권한이 있는지 확인할 수 있도록 호출자에게 권한을 부여해야 합니다. 예를 들어 디바이스가 클라우드에서 모니터링할 수 있는 스마트 도어 잠금이며, 원격 도어 잠금과 같은 기능도 제공합니다.</p><p>스마트 도어 잠금은 누군가가 실제로 도어에 가까이 올 때만 카드를 통한 잠금 해제 기능을 제공합니다. 이 경우 원격 명령 및 제어의 구현은 클라우드 게이트웨이에 도어 잠금 해제 명령을 보낼 권한이 없으므로 도어 잠금 해제 기능을 제공하지 않는 방식으로 수행되어야 합니다.</p>|

## <a id="field-permission"></a>다른 권한 수준이 필요한 다양한 작업을 지원하는 경우 필드 게이트웨이에서 권한 부여 확인 수행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 필드 게이트웨이는 호출자에게 요청된 작업을 수행하는 데 필요한 권한이 있는지 확인할 수 있도록 호출자에게 권한을 부여해야 합니다. 예를 들어 연결하는 필드 게이트웨이 v/s 디바이스를 구성하는 데 사용되는 관리자 사용자 인터페이스/API에 대해서는 다른 권한이 있어야 합니다.|

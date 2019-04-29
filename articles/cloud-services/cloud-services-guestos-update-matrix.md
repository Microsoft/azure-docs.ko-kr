---
title: 최신 Azure 게스트 OS 릴리스에 대해 알아보기 | Microsoft Docs
description: Azure  Cloud Services 게스트 OS에 대한 최신 릴리스 뉴스 및 SDK 호환성.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2019
ms.author: raiye
ms.openlocfilehash: 955ba0aae41d7e555c1248cf04073b1b6e3f2d4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622969"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스
Cloud Services용 최신 Azure 게스트 OS릴리스에 대한 최신 정보를 제공합니다. 이 정보는 게스트 OS를 사용하지 않도록 설정하기 전에 업그레이드 경로를 계획하는 데 도움이 됩니다. [Azure 게스트 OS 업데이트 설정][Azure Guest OS Update Settings]의 설명에 따라 *자동* 게스트 OS 업데이트를 사용하도록 역할을 구성하는 경우, 이 페이지를 읽지 않아도 됩니다.

> [!IMPORTANT]
> 이 페이지는 게스트 OS를 기반으로 실행하는 Cloud Services 웹 및 작업자 역할에 적용됩니다. IaaS Virtual Machines에 **적용되지 않습니다** .
>
>


> [!TIP]
>  [게스트 OS 업데이트 RSS 피드]를 구독하여 모든 게스트 OS 변경 사항에 가장 시기 적절한 알림을 받을 수 있습니다.
>
>

> [!IMPORTANT]
> 두 개의 최신 게스트 OS 버전만 지원되며, Azure Portal에서 사용할 수 있습니다.
>
>

게스트 OS를 업데이트하는 방법에 대해 불확실한가요? [이것][cloud updates]을 확인하세요.

## <a name="news-updates"></a>새 업데이트

###### <a name="march-26-2019"></a>**2019 년 3 월 26 일**
3월 게스트 OS가 릴리스되었습니다.

###### <a name="march-12-2019"></a>**2019 년 3 월 12 일**
2월 게스트 OS가 릴리스되었습니다.

###### <a name="february-5-2019"></a>**2019년 2월 5일**
1월 게스트 OS가 릴리스되었습니다.

###### <a name="january-24-2019"></a>**2019년 1월 24일**
제품군 6 게스트 OS(Windows Server 2019)가 릴리스되었습니다.

###### <a name="january-7-2019"></a>**2019년 1월 7일**
12월 게스트 OS가 릴리스되었습니다.

###### <a name="december-14-2018"></a>**2018년 12월 14일**
11월 게스트 OS가 릴리스되었습니다.

###### <a name="november-8-2018"></a>**2018년 11월 8일**
10월 게스트 OS가 릴리스되었습니다.

###### <a name="october-12-2018"></a>**2018년 10월 12일**
9월 게스트 OS가 릴리스되었습니다.

## <a name="releases"></a>릴리스

## <a name="family-6-releases"></a>제품군 6 릴리스
**Windows Server 2019**

설치된 .NET Framework: 3.5, 4.7.2

> [!NOTE]
> .NET용 Microsoft Azure SDK - 3.0은 [여기][Windows Azure SDK]서 다운로드할 수 있습니다.
>
>설치 단계:
>1. MicrosoftAzureAuthoringTools*.msi의 이전 버전을 제거 하세요.
>2. 설치는 [Azure SDK for.NET-3.0][Windows Azure SDK]
>3. 컴퓨터를 다시 시작
>4. 새 클라우드 서비스 프로젝트를 만들고 단일 작업자 역할 추가
>5. OS 제품군을 6으로 변경 하 고 패키지 빌드
>6. Azure portal 또는 Visual Studio를 사용 하 여 Azure에 패키지 배포
>


| 구성 문자열 | 릴리스 날짜 | 비활성화 날짜 |
| --- | --- | --- |
| WA-GUEST-OS-6.5_201903-01 |2019년 3월 26일 |Post 6.7 |
| WA-GUEST-OS-6.4_201902-01 |2019년 3월 12일 |Post 6.6 |
|~~WA-GUEST-OS-6.3_201901-01~~ |2019년 2월 5일 |2019년 3월 26일 |
|~~WA-GUEST-OS-6.2_201812-01~~ |2019년 1월 24일 |2019년 3월 12일 |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019년 1월 24일 |2019년 2월 5일 |

## <a name="family-5-releases"></a>제품군 5 릴리스
**Windows Server 2016**

설치된 .NET Framework: 3.5, 4.6.2

> [!NOTE]
> OS 제품군 5의 RDP 암호는 10자 이상이어야 합니다.
>


| 구성 문자열 | 릴리스 날짜 | 비활성화 날짜 |
| --- | --- | --- |
| WA-GUEST-OS-5.29_201903-01 |2019년 3월 26일 |Post 5.31 |
| WA-GUEST-OS-5.28_201902-01 |2019년 3월 12일 |Post 5.30 |
|~~WA-GUEST-OS-5.27_201901-01~~ |2019년 2월 5일 |2019년 3월 26일 |
|~~WA-GUEST-OS-5.26_201812-01~~ |2019년 1월 7일 |2019년 3월 12일 |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018년 12월 14일 |2019년 2월 5일 |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018년 11월 8일 |2019년 1월 7일 |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018년 10월 12일 |2018년 12월 14일 |

## <a name="family-4-releases"></a>제품군 4 릴리스
**Windows Server 2012 R2**

설치된 .NET Framework: 3.5, 4.5.1

| 구성 문자열 | 릴리스 날짜 | 비활성화 날짜 |
| --- | --- | --- |
| WA-GUEST-OS-4.64_201903-01 |2019년 3월 26일 |Post 4.66 |
| WA-GUEST-OS-4.63_201902-01 |2019년 3월 12일 |Post 4.65 |
|~~WA-GUEST-OS-4.62_201901-01~~ |2019년 2월 5일 |2019년 3월 26일 |
|~~WA-GUEST-OS-4.61_201812-01~~ |2019년 1월 7일 |2019년 3월 12일 |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018년 12월 14일 |2019년 2월 5일 |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018년 11월 8일 |2019년 1월 7일 |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018년 10월 12일 |2018년 12월 14일 |

## <a name="family-3-releases"></a>제품군 3 릴리스
**Windows Server 2012**

설치된 .NET Framework: 3.5, 4.5

| 구성 문자열 | 릴리스 날짜 | 비활성화 날짜 |
| --- | --- | --- |
| WA-GUEST-OS-3.71_201903-01 |2019년 3월 26일 |Post 3.73 |
| WA-GUEST-OS-3.70_201902-01 |2019년 3월 12일 |Post 3.72 |
|~~WA-GUEST-OS-3.69_201901-01~~ |2019년 2월 5일 |2019년 3월 26일 |
|~~WA-GUEST-OS-3.68_201812-01~~ |2019년 1월 7일 |2019년 3월 12일 |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018년 12월 14일 |2019년 2월 5일 |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018년 11월 8일 |2019년 1월 7일 |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018년 10월 12일 |2018년 12월 14일 |

## <a name="family-2-releases"></a>제품군 2 릴리스
**Windows Server 2008 R2 SP1**

설치된 .NET Framework: 3.5(2.0 및 3.0 포함)

| 구성 문자열 | 릴리스 날짜 | 비활성화 날짜 |
| --- | --- | --- |
| WA-GUEST-OS-2.84_201903-01 |2019년 3월 26일 |Post 2.86 |
| WA-GUEST-OS-2.83_201902-01 |2019년 3월 12일 |Post 2.85 |
|~~WA-GUEST-OS-2.82_201901-01~~ |2019년 2월 5일 |2019년 3월 26일 |
|~~WA-GUEST-OS-2.81_201812-01~~ |2019년 1월 7일 |2019년 3월 12일 |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018년 12월 14일 |2019년 2월 5일 |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018년 11월 8일 |2019년 1월 7일 |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018년 10월 12일 |2018년 12월 14일 |

## <a name="msrc-patch-updates"></a>MSRC 패치 업데이트
매달 각 게스트 OS 릴리스에 포함된 패치 목록은 [여기][patches]에서 사용 가능합니다.

## <a name="sdk-support"></a>SDK 지원
[Azure SDK에 대한 사용 중지 정책][retire policy sdk]에 2.2 이후 버전만 지원된다고 나와 있더라도 특정 게스트 OS 제품군을 통해 이전 버전을 사용할 수 있습니다. 항상 지원되는 최신 SDK를 사용해야 합니다.

| 게스트 OS 제품군 | 호환되는 SDK 버전 |
| --- | --- |
| 6 |버전 2.9.6 이상 |
| 5 |버전 2.9.5.1+ |
| 4 |버전 2.1 이상 |
| 3 |버전 1.8 이상 |
| 2 |버전 1.3 이상 |
| 1 |버전 1.0 이상 |

## <a name="guest-os-release-information"></a>게스트 OS 릴리스 정보
게스트 OS 릴리스에 있어서 중요한 날짜 세 개는 **릴리스** 날짜, **비활성화** 날짜 및 **만료** 날짜입니다. 포털에 있고 대상 게스트 OS로 선택할 수 있는 게스트 OS는 사용 가능한 것으로 간주됩니다. 게스트 OS가 **비활성화** 날짜에 도달하면 Azure에서 제거됩니다. 그러나 해당 게스트 OS를 대상으로 하는 모든 클라우드 서비스는 계속 정상적으로 작동합니다.

**비활성화** 날짜와 **만료** 날짜 사이의 기간 동안 한 게스트 OS에서 다른 게스트 OS로 간편하게 전환할 수 있습니다. 게스트 OS로 *자동* 을 사용하는 경우 항상 최신 버전이 유지되므로 만료에 대해 걱정할 필요가 없습니다.

**만료** 날짜가 지났는데 여전히 해당 게스트 OS를 사용 중인 Cloud Service는 중지, 삭제 또는 강제 업그레이드됩니다. 사용 중지 정책에 대한 자세한 내용은 [여기][retirepolicy]서 확인할 수 있습니다.

## <a name="guest-os-family-version-explanation"></a>게스트 OS 제품군 버전 설명
게스트 OS 제품군은 출시된 Microsoft Windows Server 버전을 기반으로 합니다. 게스트 OS는 Azure Cloud Services가 실행되는 기본 운영 체제입니다. 각 게스트 OS에는 제품군, 버전 및 릴리스 번호가 있습니다.

* **Guest OS family**  
  은 게스트 OS가 기반으로 하는 Windows Server 운영 체제 릴리스입니다. 예를 들어 *제품군 3* 은 Windows Server 2012를 기반으로 합니다.
* **게스트 OS 버전**  
  새 게스트 OS 버전이 생성되는 날짜에 사용 가능한 게스트 OS 제품군 이미지 및 관련 MSRC([Microsoft 보안 대응 센터)][msrc] 패치로 한정됩니다. 일부 패치는 포함될 수 있습니다.

    0에서 시작하고 새 업데이트 집합이 추가 될 때마다 1씩 증가하는 숫자입니다. 중요한 경우.후행 0만 표시됩니다. 즉, 버전 2.10은 다르며, 버전 2.1 버전보다 더 한참 후의 버전입니다.
* **게스트 OS 릴리스**  
   게스트 OS 버전의 릴리스입니다. 필요한 변경 내용을 테스트하는 동안 Microsoft가 문제를 발견하는 경우에 릴리스가 발생합니다. 최신 릴리스는 항상 이전의 모든 릴리스를 공개이든 또는 비공개이든 대체합니다. Azure Portal에서 사용자는 지정된 버전에 대한 최신 릴리스를 선택할 수만 있습니다. 이전 릴리스에서 실행 중인 배포는 일반적으로 버그의 심각도 따라 강제 업그레이드되지 않습니다.

아래 예제에서 2는 제품군이며, 12는 버전이고 "rel2"는 릴리스입니다.

**게스트 OS 릴리스** -2.12 rel2

**이 릴리스에 대한 구성 문자열** -WA-GUEST-OS-2.12_201208-02

게스트 OS에 대한 구성에는 문자열에서 보여주는 MSRC 패치가 해당 릴리스에 대한 것으로 간주된 날짜와 함께 여기에 포함된 동일한 정보가 있습니다. 이 예에서는 Windows Server 2008 R2에 대한 MSRC 패치가 생성되며 2012년 8월포함이 고려되었습니다. 특히 해당 버전의 Windows Server에 적용되는 패치만 포함됩니다. 예를 들어 MSRC 패치가 Microsoft office에 적용되는 경우, 해당 제품이 Windows Server 기본 이미지의 일부가 아니므로 포함되지 않습니다.

## <a name="guest-os-system-update-process"></a>게스트 OS 시스템 업데이트 프로세스
이 페이지에 예정된 게스트 OS 릴리스에 대한 정보가 포함됩니다. "자동" 업데이트로 설정된 경우 해당 클라우드 서비스 역할이 다시 부팅되기때문에 릴리스가 발생하는 시기를 알고 싶어하는지 표시합니다. 게스트 OS 릴리스는 일반적으로 매월 두 번째 화요일에 수행되는 MSRC 업데이트 릴리스 이후 2-3주 후에 수행됩니다. 새 릴리스는 각 게스트 OS 제품군에 대한 모든 관련 MSRC 패치를 포함합니다.

Microsoft Azure는 지속적으로 업데이트를 릴리스하고 있습니다. 이러한 업데이트로 게스트 OS가 파이프라인에서 유일합니다. 릴리스는 다양한 요인에 의해 영향을 받을 수 있어 나열할 수 없습니다. 또한 Azure는 수백 대의 컴퓨터에서 실행 됩니다. 역할이 다시 부팅 되는 정확한 날짜와 시간을 제공할 수 있다는 것을 의미합니다. 제한 또는 시간 재부팅에 대한 계획을 설정하고 있습니다.

게스트 OS의 새 릴리스를 게시하는 경우 Azure에 완전히 전파하는데 시간이 걸릴 수 있습니다. 서비스가 새 게스트 OS에 대해 업데이트되므로 업데이트 도메인을 구분하지 않고 다시 부팅됩니다. "자동" 업데이트를 사용하도록 설정된 서비스가 처음 릴리스됩니다. 업데이트 후 Azure Portal에서 서비스에 대해 나열된 새 게스트 OS 버전을 볼 수 있습니다. 이 기간 동안 다시 릴리스할 수 있습니다. 일부 버전은 오랜 시간을 통해 배포할 수 있으며 공식 릴리스 날짜 이후 수 주 동안 자동 업그레이드 재부팅이 발생하지 않을 수 있습니다. 게스트 OS를 사용할 수 있으면, 구성 파일이나 포털에서 해당 버전을 명시적으로 찾을 수 있습니다.

게스트 및 호스트 OS 업데이트의 기술 세부 정보에 대한 포인터 및 시작에 관한 중요 정보 처리는 [OS 업그레이드로 인한 역할 인스턴스 다시 시작][restarts]이라는 MSDN 블로그 게시물을 참조하세요.

게스트 OS를 수동으로 업데이트하는 경우 추가 정보는 [게스트 OS 사용 중지 정책][retirepolicy]을 참조하세요.

## <a name="guest-os-supportability-and-retirement-policy"></a>게스트 OS 지원 가능성 및 사용 중지 정책
게스트 OS 지원 가능성 및 사용 중지 정책은 [여기][retirepolicy]에 설명됩니다.

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[게스트 OS 업데이트 RSS 피드]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917

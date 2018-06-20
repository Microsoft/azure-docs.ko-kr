---
title: Azure SDK for .NET 2.9 릴리스 정보
description: Azure SDK for .NET 2.9 릴리스 정보
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 30013c0bd8ded31ccaf3cdc142bbf8dc6b375031
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150960"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Azure SDK for .NET 2.9 릴리스 정보

이 항목에는 Azure SDK for .NET 버전 2.9 및 2.9.6에 대한 릴리스 정보가 포함되어 있습니다.

## <a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK for .NET 2.9.6 릴리스 요약

릴리스 날짜: 11/16/2016
 
Azure SDK 2.9의 새로운 변경 내용은 이번 릴리스에 도입되지 않았습니다. 기존 클라우드 서비스 프로젝트에서 이 SDK를 활용하는 데 필요한 업그레이드 프로세스도 없습니다.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 릴리스 후보

- Visual Studio 2017 RC에서는 이 Azure SDK for .NET 릴리스가 Azure 워크로드에 구축되었습니다. Azure 개발을 수행하는 데 필요한 모든 도구는 앞으로 Visual Studio 2017 RC에 포함될 예정입니다. Visual Studio 2015 및 Visual Studio 2013의 경우 해당 SDK는 계속 WebPI를 통해 사용할 수 있습니다. Visual Studio 2017이 최종 제품으로 출시될 때는 Visual Studio 2013용 Azure SDK for .NET 릴리스가 중단될 것입니다. Visual Studio 2017 RC를 다운로드하려면 이 링크를 사용하세요. https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure 진단

- 이 동작은 키가 Cloud Services 진단 저장소 연결 문자열에 대한 토큰으로 교체되어 부분 연결 문자열만 저장하는 방식으로 변경되었습니다. 이제 실제 저장소 키가 액세스를 제어할 수 있도록 사용자 프로필 폴더에 저장됩니다. Visual Studio는 로컬 디버깅 및 게시 프로세스에 대한 사용자 프로필 폴더에서 저장소 키를 읽습니다. 
- 위의 변화에 따라, Visual Studio Online 팀은 사용자가 연속 통합 및 배포에서 Azure에 게시할 때 진단 확장을 설정하기 위한 저장소 키를 지정할 수 있도록 Azure Cloud Services 배포 작업 템플릿을 개선했습니다.
- 작업 환경 간 구성 문제를 해결하는 데 도움을 주기 위해 Azure 진단(WAD)에 대한 보안 연결 문자열 및 토큰화를 저장할 수 있게 했습니다.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 가상 머신

- 이제 Visual Studio에서는 OS 제품군 5(Windows Server 2016) 가상 머신에 Cloud Services를 배포하도록 지원합니다. 기존 클라우드 서비스의 경우, 새 OS 제품군을 대상으로 설정을 변경할 수 있습니다. 새 클라우드 서비스를 만들 때 .NET 4.6 이상을 사용하여 서비스를 만들려면 선택하는 경우 기본적으로 서비스는 OS 제품군 5를 사용하게 됩니다.  자세한 내용은 [게스트 OS 제품군 지원 테이블](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/)을 참조하세요.

#### <a name="known-issues"></a>알려진 문제

- Azure .NET SDK 2.9.6에는 지원되지 않는 .NET framework(예: .NET 4.6)를 사용하여 프로젝트를 OS 제품군 5 미만으로 배포하는 것을 차단하는 제한 사항이 도입되었습니다. 해결 방법이 [여기](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)에 제공됩니다.

 
### <a name="azure-in-role-cache"></a>Azure In-Role Cache 

- Azure In-Role Cache에 대한 지원은 2016년 11월 30일에 종료됩니다. 자세한 내용을 보려면 [여기](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)를 클릭하세요.

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Azure Stack용 Azure Resource Manager 템플릿

- Azure Resource Manager 템플릿에 대한 배포 대상으로 Azure Stack을 도입했습니다.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK for .NET 2.9 요약

## <a name="overview"></a>개요
이 문서에는 Azure SDK for .NET 2.9 릴리스의 릴리스 정보가 포함되어 있습니다. 

이 릴리스의 업데이트에 대한 자세한 정보는 [Azure SDK 2.9 발표 게시물](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)을 참조하세요.

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Visual Studio 2015 업데이트 2 및 Visual Studio "15" Preview용 Azure SDK 2.9
이 업데이트는 다음 버그 수정을 포함합니다.

* "알 수 없는 형식" 문자열이 코드 생성 폴더의 이름 및/또는 생성된 코드에 포함될 네임스페이스 이름으로 나타나는 REST API 클라이언트 생성과 관련된 문제
* 인증 정보가 Scheduler 프로비전 프로세스로 전달되지 못하는 예약된 WebJobs와 관련된 문제

이 업데이트는 다음 새로운 기능을 포함합니다.

* App Service 프로비전 대화 상자의 "서비스" 탭에서 보조 App Services에 대한 지원 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Visual Studio 2015 업데이트 2용 Azure Data Lake 도구
이 업데이트는 다음을 포함합니다.

* **Azure Data Lake 도구** 가 Azure SDK for .NET 릴리스에 병합됩니다. 이 도구는 Azure SDK를 설치할 때 자동으로 설치됩니다. 
  
    이 도구는 자주 업데이트되며 업데이트를 받으려면 [여기](http://aka.ms/datalaketool) 로 이동하세요.
* **서버 탐색기** 를 통해 모든 항목을 보고 일부 U-SQL 메타데이터 엔터티를 만들 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/documentation/services/data-lake-analytics/) 를 참조하세요.

## <a name="hdinsight-tools"></a>HDInsight 도구
**HDInsight 도구** 에서 Tez 그래프 표시 및 기타 언어 수정을 포함하여 HDInsight 버전 3.3을 지원합니다.

## <a name="azure-resource-manager"></a>Azure 리소스 관리자
이 릴리스는 Resource Manager 템플릿에 대한 [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) 지원을 추가했습니다.

## <a name="see-also"></a>참고 항목
[Azure SDK 2.9 발표 게시물](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)


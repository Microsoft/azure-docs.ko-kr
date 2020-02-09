---
title: 배포 모범 사례
description: Azure App Service에 배포 하는 주요 메커니즘에 대해 알아봅니다. 언어별 권장 사항 및 기타 주의 사항을 찾습니다.
keywords: azure app service, 웹 앱, 배포, 배포, 파이프라인, 빌드
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750473"
---
# <a name="deployment-best-practices"></a>배포 모범 사례

모든 개발 팀에는 클라우드 서비스에 대한 효율적인 배포 파이프라인을 구현 하기 위한 고유한 요구 사항이 있습니다. 이 문서에서는 App Service에 배포 하는 세 가지 주요 구성 요소인 배포 원본, 빌드 파이프라인 및 배포 메커니즘을 소개 합니다. 이 문서에는 특정 언어 스택에 대한 몇 가지 모범 사례 및 팁이 포함 되어 있습니다.

## <a name="deployment-components"></a>배포 구성 요소

### <a name="deployment-source"></a>배포 원본

배포 소스는 응용 프로그램 코드의 위치입니다. 프로덕션 앱의 경우 배포 원본은 일반적으로 [GitHub, BitBucket 또는 Azure Repos](deploy-continuous-deployment.md)와 같은 버전 제어 소프트웨어에서 호스트 되는 리포지토리입니다. 개발 및 테스트 시나리오의 경우 배포 원본은 [로컬 컴퓨터의 프로젝트](deploy-local-git.md)일 수 있습니다. App Service은 배포 원본으로 [OneDrive 및 Dropbox 폴더도](deploy-content-sync.md) 지원 합니다. 클라우드 폴더를 사용 하면 쉽게 App Service를 시작할 수 있지만, 일반적으로 엔터프라이즈 수준의 프로덕션 응용 프로그램에는이 원본을 사용 하지 않는 것이 좋습니다. 

### <a name="build-pipeline"></a>빌드 파이프라인

배포 소스를 결정 한 후 다음 단계는 빌드 파이프라인을 선택 하는 것입니다. 빌드 파이프라인은 배포 소스에서 소스 코드를 읽고 코드 컴파일, HTML 및 JavaScript 축소 테스트 실행, 구성 요소 패키징 등의 일련의 단계를 실행 하 여 응용 프로그램을 실행 가능한 상태로 가져옵니다. 빌드 파이프라인에 의해 실행 되는 특정 명령은 언어 스택에 따라 달라 집니다. 이러한 작업은 Azure Pipelines 같은 빌드 서버에서 실행 하거나 로컬로 실행할 수 있습니다.

### <a name="deployment-mechanism"></a>배포 메커니즘

배포 메커니즘은 웹 앱의 */home/site/wwwroot* 디렉터리에 빌드된 응용 프로그램을 배치 하는 데 사용 되는 작업입니다. */Wwwroot* 디렉터리는 웹 앱의 모든 인스턴스에서 공유 하는 탑재 된 저장소 위치입니다. 배포 메커니즘이 응용 프로그램을이 디렉터리에 배치 하면 인스턴스는 새 파일을 동기화 하는 알림을 받게 됩니다. App Service는 다음과 같은 배포 메커니즘을 지원 합니다.

- Kudu 엔드포인트: [Kudu](https://github.com/projectkudu/kudu/wiki) 는 Windows App Service에서 별도의 프로세스로 실행 되는 오픈 소스 개발자 생산성 도구 이며 Linux App Service의 두 번째 컨테이너로 실행 됩니다. Kudu는 연속 배포를 처리 하 고 zipdeploy와 같은 배포에 대한 HTTP 엔드포인트을 제공 합니다.
- FTP 및 WebDeploy: [사이트 또는 사용자 자격 증명](deploy-configure-credentials.md)을 사용 하 여 Ftp 또는 webdeploy를 [통해](deploy-ftp.md) 파일을 업로드할 수 있습니다. 이러한 메커니즘은 Kudu를 거치지 않습니다.  

Azure Pipelines, Jenkins 및 편집기 플러그 인 같은 배포 도구는 이러한 배포 메커니즘 중 하나를 사용 합니다.

## <a name="language-specific-considerations"></a>언어별 고려 사항

### <a name="java"></a>Java

Kudu [zipdeploy/](deploy-zip.md) API를 사용 하 여 JAR 응용 프로그램을 배포 하 고, WAR 응용 프로그램을 배포 [/](deploy-zip.md#deploy-war-file) 사용 합니다. Jenkins를 사용 하는 경우 배포 단계에서 직접 해당 Api를 사용할 수 있습니다. 자세한 내용은 [이 문서](../jenkins/execute-cli-jenkins-pipeline.md)(영문)를 읽어보세요.

### <a name="node"></a>노드

기본적으로 Kudu는 노드 응용 프로그램 (`npm install`)에 대한 빌드 단계를 실행 합니다. Azure DevOps와 같은 빌드 서비스를 사용 하는 경우 Kudu 빌드는 필요 하지 않습니다. Kudu 빌드를 사용 하지 않도록 설정 하려면 `false`값을 사용 하 여 `SCM_DO_BUILD_DURING_DEPLOYMENT`앱 설정을 만듭니다.

### <a name="net"></a>.NET 

기본적으로 Kudu는 .Net 응용 프로그램 (`dotnet build`)에 대한 빌드 단계를 실행 합니다. Azure DevOps와 같은 빌드 서비스를 사용 하는 경우 Kudu 빌드는 필요 하지 않습니다. Kudu 빌드를 사용 하지 않도록 설정 하려면 `false`값을 사용 하 여 `SCM_DO_BUILD_DURING_DEPLOYMENT`앱 설정을 만듭니다.

## <a name="other-deployment-considerations"></a>기타 배포 고려 사항

### <a name="use-deployment-slots"></a>배포 슬롯 사용

가능 하면 새 프로덕션 빌드를 배포할 때 [배포 슬롯](deploy-staging-slots.md) 을 사용 합니다. 표준 App Service 계획 계층 이상을 사용 하는 경우 스테이징 환경에 앱을 배포 하 고, 변경 내용의 유효성을 검사 하 고, 스모크 테스트를 수행할 수 있습니다. 준비가 되 면 스테이징 및 프로덕션 슬롯을 교환할 수 있습니다. 교환 작업을 수행 하면 프로덕션 규모와 일치 하는 데 필요한 작업자 인스턴스가 발생 하므로 가동 중지 시간을 없앨 수 있습니다. 

### <a name="local-cache"></a>로컬 캐시

Azure App Service의 콘텐츠는 Azure Storage에 저장되며 영구적 방식으로 콘텐츠 공유로 표시됩니다. 그러나 일부 앱은 고가용성으로 실행할 수 있는 고성능 읽기 전용 콘텐츠 저장소가 필요 합니다. 이러한 앱은 [로컬 캐시](overview-local-cache.md)를 사용 하는 이점을 누릴 수 있습니다. WordPress 등의 콘텐츠 관리 사이트에는 로컬 캐시를 사용할 수 없습니다.

가동 중지 시간을 방지 하려면 항상 [배포 슬롯과](deploy-staging-slots.md) 함께 로컬 캐시를 사용 합니다. 이러한 기능을 함께 사용 하는 방법에 대한 자세한 내용은 [이 섹션](overview-local-cache.md#best-practices-for-using-app-service-local-cache) 을 참조 하세요.

### <a name="high-cpu-or-memory"></a>높은 CPU 또는 메모리

App Service 계획에서 사용 가능한 CPU 또는 메모리의 90%를 초과 하 여 사용 하는 경우 기본 가상 컴퓨터에서 배포를 처리 하는 데 문제가 있을 수 있습니다. 이 경우 배포를 수행 하기 위해 인스턴스 수를 일시적으로 확장 합니다. 배포가 완료 되 면 인스턴스 수를 이전 값으로 반환할 수 있습니다.

모범 사례에 대한 자세한 내용은 [App Service 진단을](https://docs.microsoft.com/azure/app-service/overview-diagnostics) 방문 하 여 리소스와 관련 하 여 조치 가능한 모범 사례를 확인 하세요.

- [Azure Portal](https://portal.azure.com)에서 웹 앱으로 이동 합니다.
- 왼쪽 탐색에서 **문제 진단 및 해결** 을 클릭 하면 App Service 진단이 열립니다.
- **모범 사례** 홈 페이지 타일을 선택 합니다.
- 모범 사례에 **대한 모범 사례** 를 클릭 하 여 모범 사례에 대한 응용 프로그램의 현재 상태를 확인 하는 **최적의 구성을 위한** 모범 사례를 & 합니다.

또한이 링크를 사용 하 여 리소스에 대한 App Service 진단을 직접 열 수 있습니다. `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.

---
title: Azure Container Instances를 Jenkins 빌드 에이전트로 사용
description: Azure Container Instances를 Jenkins 빌드 에이전트로 사용하는 방법을 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 2687a64bfd952888086862d929a3e5869aee5a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584172"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances를 Jenkins 빌드 에이전트로 사용

ACI(Azure Container Instances)는 컨테이너화된 워크로드를 실행하기 위한 격리된 주문형, 버스터블 환경을 제공합니다. 이러한 특성 때문에 ACI는 Jenkins 빌드 작업을 대규모로 실행할 수 있는 우수한 플랫폼을 만들어 줍니다. 이 문서에서는 ACI를 통해 빌드 대상으로 미리 구성된 Jenkins 서버를 배포하고 사용하는 방법을 안내합니다.

Azure Container Instances에 대한 자세한 내용은 [Azure Container Instances 정보][about-aci]를 참조하세요.

## <a name="deploy-a-jenkins-server"></a>Jenkins 서버 배포

1. Azure Portal에서 **리소스 만들기**를 선택하고 **Jenkins**를 검색합니다. 게시자가 **Microsoft**인 Jenkins를 선택하고 **만들기**를 선택합니다.

2. **기본** 양식에서 다음 정보를 입력하고 **확인**을 선택합니다.

   - **이름**: Jenkins 배포의 이름을 입력합니다.
   - **사용자 이름**: Jenkins 가상 머신의 관리 사용자 이름을 입력합니다.
   - **인증 형식**: SSH 공개 키 인증을 사용하는 것이 좋습니다. 이 옵션을 선택하는 경우 Jenkins 가상 머신에 로그인할 때 사용할 SSH 공개 키를 붙여넣습니다.
   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: 리소스 그룹을 만들거나 기존 그룹을 선택합니다.
   - **Location**: Jenkins 서버의 위치를 선택합니다.

   ![Jenkins 포털 배포의 기본 설정](./media/container-instances-jenkins/jenkins-portal-01.png)

3. **추가 설정 양식**에서 다음 항목을 완료합니다.

   - **크기**: Jenkins 가상 머신에 적합한 크기 지정 옵션을 선택합니다.
   - **VM 디스크 유형**: Jenkins 서버의 **HDD**(하드 디스크 드라이브) 또는 **SSD**(반도체 드라이브)를 지정합니다.
   - **가상 네트워크**: 기본 설정을 수정하려면 화살표를 선택합니다.
   - **서브넷**: 화살표를 선택하고, 정보를 확인하고, **확인**을 선택합니다.
   - **공용 IP 주소**: 화살표를 선택하여 공용 IP 주소의 사용자 지정 이름을 지정하고, SKU를 구성하고, 메서드 할당을 설정합니다.
   - **도메인 이름 레이블**: Jenkins 가상 머신에 대한 정규화된 URL을 만드는 값을 지정합니다.
   - **Jenkins 릴리스 종류**: 다음 옵션에서 원하는 릴리스 종류를 선택합니다. **LTS**, **주간 빌드** 또는 **Azure 검증 완료**.

   ![Jenkins 포털 배포의 추가 설정](./media/container-instances-jenkins/jenkins-portal-02.png)

4. 서비스 주체 통합을 위해 **Auto(MSI)** 를 선택하여 [Azure 리소스용 관리 ID][managed-identities-azure-resources]가 Jenkins 인스턴스에 대한 인증 ID를 자동으로 만들게 합니다. 사용자 고유의 서비스 주체 자격 증명을 입력하려면 **수동**을 선택합니다.

5. 클라우드 에이전트는 Jenkins 빌드 작업을 위한 클라우드 기반 플랫폼을 구성합니다. 이 문서에서는 **ACI**를 선택합니다. ACI 클라우드 에이전트를 사용하면 각 Jenkins 빌드 작업이 하나의 컨테이너 인스턴스에서 실행됩니다.

   ![Jenkins 포털 배포의 클라우드 통합 설정](./media/container-instances-jenkins/jenkins-portal-03.png)

6. 통합 설정을 마쳤으면 **확인**을 선택한 다음, 유효성 검사 요약에서 **확인**을 다시 선택합니다. **사용 약관** 요약에서 **만들기**를 선택합니다. Jenkins 서버가 배포될 때까지 몇 분 정도 걸립니다.

## <a name="configure-jenkins"></a>Jenkins 구성

1. Azure Portal에서 Jenkins 리소스 그룹을 찾아서 Jenkins 가상 머신을 선택하고, DNS 이름을 기록해 둡니다.

   ![Jenkins 가상 머신에 대한 세부 정보의 DNS 이름](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Jenkins VM의 DNS 이름을 찾아서 반환된 SSH 문자열을 복사합니다.

   ![SSH 문자열을 사용한 Jenkins 로그인 지침](./media/container-instances-jenkins/jenkins-portal-04.png)

3. 개발 시스템에서 터미널 세션을 열고 마지막 단계의 SSH 문자열을 붙여넣습니다. `username`을 Jenkins 서버를 배포할 때 지정한 사용자 이름으로 업데이트합니다.

4. 세션이 연결되면 다음 명령을 실행하여 초기 관리자 암호를 검색합니다.

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. SSH 세션과 터널이 계속 실행되도록 그대로 두고, 브라우저에서 `http://localhost:8080`으로 이동합니다. 상자에 초기 관리자 암호를 붙여넣고 **계속**을 선택합니다.

   ![관리자 암호 상자가 있는 "Jenkins 잠금 해제" 화면](./media/container-instances-jenkins/jenkins-portal-05.png)

6. **권장 플러그 인 설치**를 선택하여 권장 Jenkins 플러그 인을 모두 설치합니다.

   !["권장 플러그인 설치"를 선택한 "Jenkins 사용자 지정" 화면](./media/container-instances-jenkins/jenkins-portal-06.png)

7. 관리 사용자 계정을 만듭니다. 이 계정은 Jenkins 인스턴스에 로그인하여 작업하는 데 사용됩니다.

   ![자격 증명을 입력한 "첫 번째 관리 사용자 만들기" 화면](./media/container-instances-jenkins/jenkins-portal-07.png)

8. **저장 및 끝내기**를 선택한 다음, **Jenkins를 사용하여 시작**을 선택하여 구성을 완료합니다.

이제 Jenkins는 코드를 빌드하고 배포할 준비가 완료되었습니다. 이 예에서는 간단한 Java 애플리케이션을 사용하여 Azure Container Instances에서 Jenkins 빌드를 시연합니다.

## <a name="create-a-build-job"></a>빌드 작업 만들기

이제 Jenkins 빌드 작업을 만들어서 Azure Container Instance에서 Jenkins 빌드를 보여줍니다.

1. **새 항목**을 선택하고, 빌드 프로젝트의 이름을 지정하고(예: **aci-demo**), **프리스타일 프로젝트**를 선택하고, **확인**을 선택합니다.

   ![빌드 작업 이름 상자와 프로젝트 형식 목록](./media/container-instances-jenkins/jenkins-new-job.png)

2. **일반**에서 **이 프로젝트를 실행할 수 있는 위치 제한**을 선택합니다. 레이블 식에 **linux**를 입력합니다. 이렇게 구성하면 이 빌드 작업이 ACI 클라우드에서 실행됩니다.

   ![구성 세부 정보가 제공되는 "일반" 탭](./media/container-instances-jenkins/jenkins-job-01.png)

3. **빌드** 아래에서 **빌드 단계 추가**를 선택하고 **셸 실행**을 선택합니다. `echo "aci-demo"`를 명령으로 입력합니다.

   ![빌드 단계에 대한 선택 영역이 있는 "빌드" 탭](./media/container-instances-jenkins/jenkins-job-02.png)

5. **저장**을 선택합니다.

## <a name="run-the-build-job"></a>빌드 작업 실행

빌드 작업을 테스트하고 빌드 플랫폼으로 Azure Container Instances를 관찰하려면 수동으로 빌드를 시작합니다.

1. **지금 빌드**를 선택하여 빌드 작업을 시작합니다. 작업이 시작될 때까지 몇 분 정도 걸립니다. 다음 이미지와 비슷한 상태가 표시됩니다.

   ![작업 상태가 포함된 "빌드 기록" 정보](./media/container-instances-jenkins/jenkins-job-status.png)

2. 작업이 실행되는 동안 Azure Portal을 열고 Jenkins 리소스 그룹을 살펴봅니다. 컨테이너 인스턴스가 만들어진 것을 볼 수 있습니다. Jenkins 작업이 이 인스턴스 내부에서 실행되고 있습니다.

   ![리소스 그룹의 컨테이너 인스턴스](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins가 구성된 Jenkins 실행기 수(기본값 2)보다 많은 작업을 실행하면 여러 컨테이너 인스턴스가 만들어집니다.

   ![새로 만들어진 컨테이너 인스턴스](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. 모든 빌드 작업이 완료되면 컨테이너 인스턴스가 제거됩니다.

   ![컨테이너 인스턴스가 제거된 리소스 그룹](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

Azure의 Jenkins에 대한 자세한 내용은 [Azure 및 Jenkins][jenkins-azure]를 참조하세요.

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md
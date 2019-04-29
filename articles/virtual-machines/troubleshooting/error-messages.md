---
title: Azure에서 일반적인 VM 오류 코드 | Microsoft Docs
description: Azure에서 가상 머신을 프로비전하고 관리할 때 발생하는 일반적인 일부 오류 코드를 이해
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 5945be210812a6cbc24c9a3bb12414be5212be17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711206"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Azure에서 가상 머신을 관리할 때 발생하는 일반적인 오류 메시지를 이해

이 문서에서는 Azure에서 VM(가상 머신)을 만들거나 관리할 때 발생할 수 있는 가장 일반적인 일부 오류 코드 및 메시지를 설명합니다.

>[!NOTE]
> 이 페이지에 의견을 남기거나 #azerrormessage 태그를 사용하여 [Azure 피드백](https://feedback.azure.com/forums/216843-virtual-machines)을 통해 의견을 남겨주세요.

## <a name="error-response-format"></a>오류 응답 형식 
Azure VM은 오류 응답에 다음 JSON 형식을 사용합니다.

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

오류 응답에는 상태 코드와 오류 개체가 항상 포함됩니다. 각 오류 개체에는 오류 코와 메시지가 항상 포함됩니다. VM이 템플릿으로 작성된 경우 오류 개체는 내부 수준의 오류 코드 및 메시지가 포함된 세부 사항 섹션을 포함합니다. 일반적으로 오류 메시지의 내부 수준은 루트 실패입니다.


## <a name="common-virtual-machine-management-errors"></a>일반적인 가상 머신 관리 오류

이 섹션은 VM을 관리할 때 발생할 수 있는 일반적인 오류 메시지를 나열합니다.

|  오류 코드  |  오류 메시지  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  URI가 {1}인 Blob을 사용하여 '{0}' 디스크를 만드는 동안 임대를 가져오지 못했습니다. Blob이 이미 사용 중입니다.  |  
|  AllocationFailed  |  할당하지 못했습니다. VM의 크기나 수를 줄여 보거나, 나중에 다시 시도하거나, 다른 가용성 집합 또는 Azure 위치에 배포해 보세요.  |  
|  AllocationFailed  |  내부 오류로 인해 VM을 할당하지 못했습니다. 나중에 다시 시도하거나 다른 위치에 배포해 보세요.  |
|  ArtifactNotFound  |  게시자가 '{0}'이고 유형이 '{1}'인 VM 확장을 '{2}' 위치에서 찾을 수 없습니다.  |
|  ArtifactNotFound  |  확장 리포지토리에서 게시자 '{0}', 형식 '{1}' 및 형식 처리기 버전 '{2}'의 확장을 찾을 수 없습니다.  |
|  ArtifactVersionNotFound  |  아티팩트 리포지토리에 요청된 버전 '{0}'을(를) 만족하는 버전이 없습니다.  |
|  ArtifactVersionNotFound  |  아티팩트 리포지토리에 '{1}' 게시자와 '{2}' 형식을 포함한 VM 확장자에 대한 요청된 버전 '{0}'을(를) 만족하는 버전이 없습니다.  |
|  AttachDiskWhileBeingDetached  |  현재 디스크가 분리되어 있으므로 데이터 디스크 '{0}'을(를) VM '{1}'에 연결할 수 없습니다. 디스크가 완전히 분리될 때까지 잠시 기다렸다가 다시 시도하세요.  |
|  BadRequest  |  정렬된 가용성 집합은 이 지역에서 아직 지원되지 않습니다.  |
|  BadRequest  |  관리되지 않는 가용성 집합에 Managed Disks를 사용하여 VM을 추가하거나 관리되는 가용성 집합에 Blob 기반 디스크를 사용하여 VM을 추가하는 작업은 지원되지 않습니다. Managed Disks를 사용하여 VM을 추가하기 위해 '관리되는' 속성을 설정한 가용성 집합을 만듭니다.  |
|  BadRequest  |  Managed Disks는 이 지역에서 지원되지 않습니다.  |
|  BadRequest  |  처리기당 여러 VMExtensions이 OS 유형 '{0}'에 지원되지 않습니다. 처리기 '{2}'을(를) 사용하는 VMExtension '{1}'을(를) 이미 추가했거나 입력에서 지정했습니다.  |
|  BadRequest  |  '{0}' 작업은 관리 디스크를 포함하는 리소스 '{1}'에서 지원되지 않습니다.  |
|  CertificateImproperlyFormatted  |  {0}에서 검색한 암호의 JSON 표현에 형식이 제대로 지정된 PFX 파일이 아닌 데이터 필드가 있거나, 제공된 암호가 PFX 파일을 올바로 디코드하지 않습니다.  |
|  CertificateImproperlyFormatted  |  {0}에서 검색한 데이터를 JSON으로 역직렬화할 수 없습니다.  |
|  충돌  |  디스크 크기 조정은 VM을 만들거나 VM의 할당을 취소할 때만 허용됩니다.  |
|  ConflictingUserInput  |  디스크 '{0}'은(는) VM '{1}'에서 이미 소유한 디스크로 연결될 수 없습니다.  |
|  ConflictingUserInput  |  원본 및 대상 리소스 그룹이 동일합니다.  |
|  ConflictingUserInput  |  디스크 {0}에 대한 원본 저장소 계정과 대상 저장소 계정이 서로 다릅니다.  |
|  ContainerAlreadyOnLease  |  URI가 {0}인 Blob을 보관하는 저장소 컨테이너에 임대가 이미 있습니다.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  리소스 이동 요청에는 요청에서 하나 이상의 {0}에 의해 참조되는 KeyVault 리소스가 포함됩니다. 현재 이 기능은 구독 간 이동에서 지원되지 않습니다. KeyVault 리소스 ID의 오류 세부 정보를 확인하세요.  |
|  DiagnosticsOperationInternalError  |  VM {0}의 진단 프로필을 처리하는 동안 내부 오류가 발생했습니다.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0}은(는) VM '{1}'에 속한 다른 디스크에서 이미 사용 중입니다. 디스크 참조 정보에 대한 Blob 메타데이터를 검사할 수 있습니다.  |
|  DiskBlobNotFound  |  디스크 '{1}'에 대해 URI가 {0}인 VHD Blob을 찾을 수 없습니다.  |
|  DiskBlobNotFound  |  URI가 {0}인 VHD Blob을 찾을 수 없습니다.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} 암호에 {1} 태그가 없습니다. 암호 버전을 업데이트하고 필요한 태그를 추가한 후 다시 시도하세요.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  {1} 키를 사용하여 암호 {0} 값의 래핑을 해제하지 못했습니다.  |
|  DiskImageNotReady  |  디스크 이미지 {0}이(가) {1} 상태에 있습니다. 이미지가 준비되면 다시 시도하세요.  |
|  DiskPreparationError  |  VM 디스크를 준비하는 동안 하나 이상의 오류가 발생했습니다. 자세한 내용은 디스크 인스턴스 보기를 참조하세요.  |
|  DiskProcessingError  |  VM이 실패한 디스크에서 다른 디스크를 보유한 경우 디스크 처리가 중지됩니다.  |
|  ImageBlobNotFound  |  디스크 '{1}'에 대해 URI가 {0}인 VHD Blob을 찾을 수 없습니다.  |
|  ImageBlobNotFound  |  URI가 {0}인 VHD Blob을 찾을 수 없습니다.  |
|  IncorrectDiskBlobType  |  디스크 Blob은 유형 페이지 Blob일 수 있습니다. 디스크 '{1}'에 대한 Blob {0}은(는) 유형 블록 Blob입니다.  |
|  IncorrectDiskBlobType  |  디스크 Blob은 유형 페이지 Blob일 수 있습니다. Blob {0}은(는) '{1}' 유형입니다.  |
|  IncorrectImageBlobType  |  디스크 Blob은 유형 페이지 Blob일 수 있습니다. 디스크 '{1}'에 대한 Blob {0}은(는) 유형 블록 Blob입니다.  |
|  IncorrectImageBlobType  |  디스크 Blob은 유형 페이지 Blob일 수 있습니다. Blob {0}은(는) '{1}' 유형입니다.  |
|  InternalOperationError  |  저장소 계정 {0}을(를) 확인할 수 없습니다. 이 계정이 계산 리소스와 동일한 위치에 있는 Storage 리소스 공급자를 통해 생성되었는지 확인하세요.  |
|  InternalOperationError  |  {0} 목표 검색 태스크에 실패했습니다.  |
|  InternalOperationError  |  VM '{0}'의 네트워크 프로필을 검사하는 동안 오류가 발생했습니다.  |
|  InvalidAccountType  |  AccountType {0}은(는) 올바르지 않습니다.  |
|  InvalidParameter  |  매개 변수 {0}의 값이 잘못되었습니다.  |
|  InvalidParameter  |  지정된 관리자 암호가 허용되지 않습니다.  |
|  InvalidParameter  |  "제공된 암호의 길이는 {0}~{1}자 사이여야 하고 최소한 다음의 암호 복잡성 요구 사항 중 적어도 {2}을(를) 충족해야 합니다. <ol><li> 대문자를 포함합니다.</li><li>소문자를 포함합니다.</li><li>숫자를 포함합니다.</li><li>특수 문자를 포함합니다.</li></ol>  |
|  InvalidParameter  |  지정된 관리자 사용자 이름이 허용되지 않습니다.  |
|  InvalidParameter  |  VM이 플랫폼 이미지 또는 사용자 이미지에서 만들어진 경우 기존 OS 디스크를 연결할 수 없습니다.  |
|  InvalidParameter  |  컨테이너 이름 {0}이(가) 올바르지 않습니다. 컨테이너 이름의 길이는 3-63자여야 하며 소문자 영숫자 및 하이픈만 포함할 수 있습니다. 하이픈 앞에 뒤에 영숫자 합니다.  |
|  InvalidParameter  |  URL {1}의 컨테이너 이름 {0}이(가) 올바르지 않습니다. 컨테이너 이름의 길이는 3-63자여야 하며 소문자 영숫자 및 하이픈만 포함할 수 있습니다. 하이픈 앞에 뒤에 영숫자 합니다.  |
|  InvalidParameter  |  URL {0}의 Blob 이름은 슬래시를 포함합니다. 이 항목은 현재 디스크에 지원되지 않습니다.  |
|  InvalidParameter  |  URI {0}이(가) 올바른 Blob URI가 아닌 것 같습니다.  |
|  InvalidParameter  |  이름이 '{0}'인 디스크가 이미 동일한 LUN {1}을(를) 사용합니다.  |
|  InvalidParameter  |  '{0}'(이)라는 디스크는 이미 존재합니다.  |
|  InvalidParameter  |  지정된 이미지 참조에 이미 정의된 디스크에 대해 사용자 이미지 재정의를 지정할 수 없습니다.  |
|  InvalidParameter  |  이름이 '{0}'인 디스크가 이미 동일한 VHD URL {1}을(를) 사용합니다.  |
|  InvalidParameter  |  지정된 장애 도메인 수 {0}은(는) {1}~{2} 범위에 있어야 합니다.  |
|  InvalidParameter  |  라이선스 유형 {0}이(가) 올바르지 않습니다. 올바른 라이선스 유형: Windows_Client 또는 Windows_Server(대/소문자 구분)  |
|  InvalidParameter  |  Linux 호스트 이름은 {0}자를 초과하거나 {1} 문자를 포함할 수 없습니다.  |
|  InvalidParameter  |  Linux 프로비전 에이전트에 있는 알려진 문제로 인해 SSH 공개 키에 대한 대상 경로가 현재 기본값 {0}(으)로 제한됩니다.  |
|  InvalidParameter  |  LUN {0}에 디스크가 이미 있습니다.  |
|  InvalidParameter  |  요청의 {0} 구독이 관리되는 디스크 ID에 포함된 {1} 구독과 일치해야 합니다.  |
|  InvalidParameter  |  OSProfile의 사용자 지정 데이터는 Base64 인코딩이어야 하고 최대 길이가 {0}자여야 합니다.  |
|  InvalidParameter  |  URL {0}의 Blob 이름은 '{1}' 확장명으로 끝나야 합니다.  |
|  InvalidParameter  |  {0}'은(는) 캡처된 유효한 VHD Blob 이름 접두사가 아닙니다. 유효한 접두사는 regex '{1}'와(과) 일치합니다.  |
|  InvalidParameter  |  VM 에이전트가 프로비전되어 있지 않으면 VM에 인증서를 추가할 수 없습니다.  |
|  InvalidParameter  |  LUN {0}에 디스크가 이미 있습니다.  |
|  InvalidParameter  |  현재 가용성 집합이 할당된 클러스터에서 요청한 크기 {0}을(를) 사용할 수 없기 때문에 VM을 만들 수 없습니다. 사용 가능한 크기는 {1}입니다. https://aka.ms/azure-resizevm에서 VM 크기 조정 전략에 대해 자세히 알아봅니다.  |
|  InvalidParameter  |  요청한 VM 크기 {0}을(를) 현재 지역에서 사용할 수 없습니다. 현재 지역에서 사용할 수 있는 크기는 {1}입니다. https://aka.ms/azure-regions에서 각 지역에서 사용 가능한 VM 크기에 대해 자세히 알아봅니다.  |
|  InvalidParameter  |  요청한 VM 크기 {0}을(를) 현재 지역에서 사용할 수 없습니다. https://aka.ms/azure-regions에서 각 지역에서 사용 가능한 VM 크기에 대해 자세히 알아봅니다.  |
|  InvalidParameter  |  Windows 관리자 사용자 이름은 길이가 {0}자 이하이거나 마침표(.)로 끝나거나 문자 {1}을(를) 포함할 수 없습니다.  |
|  InvalidParameter  |  Windows 컴퓨터 이름은 길이가 {0}자 이하이거나 전체가 숫자이거나 문자 {1}을(를) 포함할 수 없습니다.  |
|  MissingMoveDependentResources  |  리소스 이동 요청에는 모든 종속 리소스가 포함되지 않습니다. 누락된 리소스 ID에 대한 오류 세부 정보를 확인하세요.  |
|  MoveResourcesHaveInvalidState  |  리소스 이동 요청에는 잘못된 저장소 계정에 연결되어 있는 VM이 포함됩니다. 이러한 리소스 ID 및 참조된 저장소 계정 이름에 대한 세부 정보를 확인하세요.  |
|  MoveResourcesHavePendingOperations  |  작업이 보류 중인 동안 리소스 이동 요청은 리소스를 포함합니다. 이러한 리소스 ID에 대한 세부 정보를 확인하세요. 보류 중인 작업을 완료하면 작업을 다시 시도합니다.  |
|  MoveResourcesNotFound  |  리소스 이동 요청은 찾을 수 없는 리소스를 포함합니다. 이러한 리소스 ID에 대한 세부 정보를 확인하세요.  |
|  NetworkingInternalOperationError  |  알 수 없는 네트워크 할당 오류입니다.  |
|  NetworkingInternalOperationError  |  알 수 없는 네트워크 할당 오류입니다.  |
|  NetworkingInternalOperationError  |  VM의 네트워크 프로필을 처리하는 동안 내부 오류가 발생했습니다.  |
|  NotFound  |  가용성 집합 {0}을(를) 찾을 수 없습니다.  |
|  NotFound  |  요청에 지정된 원본 Virtual Machine '{0}'은(는) 이 Azure 위치에 존재하지 않습니다.  |
|  NotFound  |  ID {0}을(를) 갖는 테넌트를 찾을 수 없습니다.  |
|  NotFound  |  {0} 이미지를 찾을 수 없습니다.  |
|  NotSupported  |  라이선스 형식이 {0}이지만 이미지 Blob {1}이(가) 온-프레미스에서 제공되지 않았습니다.  |
|  OperationNotAllowed  |  가용성 집합 {0}을(를) 삭제할 수 없습니다. 가용성 집합을 삭제하려면 먼저 가용성 집합이 VM을 포함하지 않도록 하세요.  |
|  OperationNotAllowed  |  가용성 집합 SKU를 '정렬됨'에서 '클래식'으로 변경하도록 허용하지 않습니다.  |
|  OperationNotAllowed  |  VM이 실행 중이 아니면 VM에서 확장을 수정할 수 없습니다.  |
|  OperationNotAllowed  |  캡처 작업은 Blob 기반 디스크를 사용하는 Virtual Machine에서만 지원됩니다. '이미지' 리소스 API를 사용하여 관리되는 Virtual Machine에서 이미지를 만드세요.  |
|  OperationNotAllowed  |  이미지가 정상적으로 만들어질 때까지 이미지 {1}에서 리소스 {0}을(를) 만들 수 없습니다.  |
|  OperationNotAllowed  |  VM이 할당되어 있을 때는 encryptionSettings를 업데이트할 수 없습니다. VM의 할당이 해제된 후 다시 시도하세요.  |
|  OperationNotAllowed  |  관리 디스크를 Blob 기반 디스크가 있는 VM에 추가하는 작업은 지원되지 않습니다.  |
|  OperationNotAllowed  |  이 크기의 VM에 연결할 수 있도록 허용된 최대 데이터 디스크 수는 {0}입니다.  |
|  OperationNotAllowed  |  Blob 기반 디스크를 Managed Disks가 있는 VM에 추가하는 작업은 지원되지 않습니다.  |
|  OperationNotAllowed  |  이미지가 삭제되도록 표시되었기 때문에 이미지 '{1}'에서 작업 '{0}'이(가) 허용되지 않습니다. 삭제 작업을 다시 시도(하거나 실행 중인 작업이 완료되기를 기다림)할 수 있습니다.  |
|  OperationNotAllowed  |  VM이 생성되었으므로 VM '{1}'에서 작업 '{0}'은(는) 허용되지 않습니다.  |
|  OperationNotAllowed  |  복원 지점 컬렉션 '{1}'이(가) 삭제되도록 표시되어 있으므로 '{0}' 작업이 허용되지 않습니다.  |
|  OperationNotAllowed  |  VM 확장 '{1}'이(가) 삭제 예정으로 표시되었으므로 이 VM 확장에서는 작업 '{0}'이(가) 허용되지 않습니다. 삭제 작업을 다시 시도(하거나 실행 중인 작업이 완료되기를 기다림)할 수 있습니다.  |
|  OperationNotAllowed  |  가상 머신 '{1}'이(가) 이미지 '{2}'을(를) 사용하여 프로비전되기 때문에 작업 '{0}'이(가) 허용되지 않습니다.  |
|  OperationNotAllowed  |  가상 머신 확장 집합 '{1}'이(가) 현재 '{2}' 이미지를 사용 중이므로 '{0}' 작업이 허용되지 않습니다.  |
|  OperationNotAllowed  |  VM '{1}'이 삭제 예정으로 표시되었으므로 이 VM에서는 작업 '{0}'이(가) 허용되지 않습니다. 삭제 작업을 다시 시도(하거나 실행 중인 작업이 완료되기를 기다림)할 수 있습니다.  |
|  OperationNotAllowed  |  VM '{1}'이(가) 할당 취소되었거나 할당 취소된 것으로 표시되었으므로 '{0}' 작업이 이 VM에서 허용되지 않습니다.  |
|  OperationNotAllowed  |  VM이 실행되고 있으므로 VM '{1}'에서 작업 '{0}'은(는) 허용되지 않습니다. 게스트 운영 체제 내부에서 VM을 종료하는 경우 명시적으로 전원을 끄세요.  |
|  OperationNotAllowed  |  '{1}' VM이 할당 해제되지 않았으므로 이 VM에서 '{0}' 작업이 허용되지 않습니다.  |
|  OperationNotAllowed  |  VM의 '{2}' 확장이 실패 상태이므로 '{0}' 작업이 '{1}' VM에서 허용되지 않습니다.  |
|  OperationNotAllowed  |  다른 작업이 진행 중이므로 작업 '{0}'은(는) VM '{1}'에서 허용되지 않습니다.  |
|  OperationNotAllowed  |  작업 '{0}'은(는) 일반화될 가상 머신 '{1}'이(가) 필요합니다.  |
|  OperationNotAllowed  |  작업을 수행하려면 VM이 실행되고 있거나 실행되도록 설정되어 있어야 합니다.  |
|  OperationNotAllowed  |  디스크 크기가 {0}GB로 이미지의 해당 디스크 크기 {1}GB보다 작아 디스크가 허용되지 않습니다.  |
|  OperationNotAllowed  |  처리기 '{0}'의 VM 확장 집합 확장은 VM 확장 집합을 만들 때만 추가할 수 있습니다.  |
|  OperationNotAllowed  |  처리기 '{0}'의 VM 확장 집합 확장은 VM 확장 집합을 삭제할 때만 삭제할 수 있습니다.  |
|  OperationNotAllowed  |  '{0}' VM이 이미 Managed Disks를 사용하고 있습니다.  |
|  OperationNotAllowed  |  '{0}' VM이 '클래식' 가용성 집합 '{1}'에 속해 있습니다. 가용성 집합을 업데이트하여 '정렬됨' SKU를 사용한 다음 변환을 다시 시도하세요.  |
|  OperationNotAllowed  |  이미지에서 만든 VM은 Blob 기반 디스크를 사용할 수 없습니다. 모든 디스크는 Managed Disks여야 합니다.  |
|  OperationNotAllowed  |  VM이 생성되어 있지 않아서 캡처 작업을 완료할 수 없습니다.  |
|  OperationNotAllowed  |  VM 디스크가 Managed Disks로 변환 중이기 때문에 '{0}' VM에 대한 관리 작업이 허용되지 않습니다.  |
|  OperationNotAllowed  |  진행 중인 작업이 Virtual Machine의 전원 상태를 {0}에서 {1}(으)로 변경하고 있습니다. 일정 시간이 지난 후 {2} 작업을 수행하세요.  |
|  OperationNotAllowed  |  VM을 추가하거나 업데이트할 수 없습니다. 기존 할당 단위에서 요청된 VM 크기 {0}을(를) 사용하지 못할 수도 있습니다. https://aka.ms/azure-resizevm에서 VM 크기 조정 전략에 대해 자세히 알아봅니다.  |
|  OperationNotAllowed  |  현재 가용성 집합이 할당된 클러스터에서 요청한 크기 {0}을(를) 사용할 수 없기 때문에 VM의 크기를 조정할 수 없습니다. 사용 가능한 크기는 {1}입니다. https://aka.ms/azure-resizevm에서 VM 크기 조정 전략에 대해 자세히 알아봅니다.  |
|  OperationNotAllowed  |  현재 VM이 할당된 클러스터에서 요청한 크기 {0}을(를) 사용할 수 없기 때문에 VM의 크기를 조정할 수 없습니다. VM의 크기를 {1}(으)로 조정하려면 Azure Portal에서 중지 작업인 할당을 취소하고 크기 조정 작업을 다시 시도하세요. https://aka.ms/azure-resizevm에서 VM 크기 조정 전략에 대해 자세히 알아봅니다.  |
|  OSProvisioningClientError  |  게스트 OS가 현재 프로비전되고 있는 중이므로 '{0}' VM에 대한 OS 프로비전에 실패했습니다.  |
|  OSProvisioningClientError  |  VM '{0}'에 대한 OS를 프로비전하지 못했습니다. 오류 세부 정보: {1} 이미지가 올바르게 준비되었는지 확인합니다(일반화됨). <ul><li>Windows에 대한 지침: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH 호스트 키를 생성하지 못했습니다. 오류 세부 정보: {0} 이 문제를 해결하려면 Linux 에이전트가 올바르게 설정되었는지 확인합니다. <ul><li>https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/에서 지침을 확인할 수 있습니다. </li></ul> |
|  OSProvisioningClientError  |  VM에 지정된 사용자 이름은 이 Linux 배포판에 유효하지 않습니다. 오류 세부 정보: {0}  |
|  OSProvisioningInternalError  |  내부 오류로 인해 VM '{0}'에 대한 OS 프로비전에 실패했습니다.  |
|  OSProvisioningTimedOut  |  할당된 시간에 VM '{0}'에 대한 OS의 프로비전을 완료하지 못했습니다. VM은 성공적으로 프로비전을 완료할 수 있습니다. 나중에 프로비전 상태를 확인합니다.  |
|  OSProvisioningTimedOut  |  할당된 시간에 VM '{0}'에 대한 OS의 프로비전을 완료하지 못했습니다. VM은 성공적으로 프로비전을 완료할 수 있습니다. 나중에 프로비전 상태를 확인합니다. 또한 이미지가 올바르게 준비되었는지 확인합니다(일반화됨).   <ul><li>Windows에 대한 지침: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux에 대한 지침: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  할당된 시간에 VM '{0}'에 대한 OS의 프로비전을 완료하지 못했습니다. 그러나 VM 게스트 에이전트가 실행되는 것을 감지했습니다. 즉, 게스트 OS는 VM 이미지로 사용되도록 제대로 준비되지 않았습니다(CreateOption=FromImage를 사용하여). 이 문제를 해결하려면 CreateOption=Attach와 함께 VHD를 있는 그대로 사용하거나 이미지 형식으로 사용할 수 있도록 제대로 준비합니다.   <ul><li>Windows에 대한 지침: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux에 대한 지침: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  요청한 VM 크기를 선택한 위치에서 현재 사용할 수 없습니다.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  지금은 진행 중인 플랫폼 업데이트로 인해 리소스를 업데이트할 수 없습니다. 나중에 다시 시도하세요.  |
|  StorageAccountLimitation  |  저장소 계정 '{0}'은(는) 디스크를 만드는 데 필요한 페이지 Blob을 지원하지 않습니다.  |
|  StorageAccountLimitation  |  저장소 계정 '{0}'이(가) 할당된 할당량을 초과했습니다.  |
|  StorageAccountLocationMismatch  |  저장소 계정 {0}을(를) 확인할 수 없습니다. 이 계정이 계산 리소스와 동일한 위치에 있는 Storage 리소스 공급자를 통해 생성되었는지 확인하세요.  |
|  StorageAccountNotFound  |  저장소 계정 {0}을(를) 찾을 수 없음 저장소 계정이 삭제되지 않았고 VM과 동일한 Azure 위치에 속하는지 확인하세요.  |
|  StorageAccountNotRecognized  |  Storage 리소스 공급자가 관리하는 Storage 계정을 사용하세요. {0}의 사용은 지원되지 않습니다.  |
|  StorageAccountOperationInternalError  |  저장소 계정 {0}에 액세스하는 동안 내부 오류가 발생했습니다.  |
|  StorageAccountSubscriptionMismatch  |  저장소 계정 {0}은(는) 구독 {1}에 포함되지 않습니다.  |
|  StorageAccountTooBusy  |  저장소 계정 '{0}'은(는) 현재 사용량이 너무 많습니다. 다른 계정을 사용하는 것이 좋습니다.  |
|  StorageAccountTypeNotSupported  |  {0} 디스크는 Blob Storage 계정인 {1}을(를) 사용합니다. 범용 저장소 계정을 사용하여 다시 시도하세요.  |
|  StorageAccountTypeNotSupported  |  저장소 계정 {0}은(는) {1} 형식입니다. 부팅 진단은 저장소 계정 유형 {2}을(를) 지원합니다.  <ul><li>부팅 진단에 Premium Storage 계정을 사용하는 경우 이 오류가 발생합니다. 자세한 내용은 [부팅 진단 사용 방법](boot-diagnostics.md)을 참조하세요. </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  구독이 인증되지 않았습니다.  |
|  TargetDiskBlobAlreadyExists  |  Blob {0}이(가) 이미 있습니다. 다른 Blob URI를 제공하여 새로운 빈 데이터 디스크 '{1}'을(를) 만드세요.  |
|  TargetDiskBlobAlreadyExists  |  대상 이미지 Blob {0}이(가) 이미 있고 VHD Blob을 덮어쓰는 플래그가 설정되어 있지 않아서 캡처 작업을 완료할 수 없습니다. Blob을 삭제하거나 VHD Blob을 덮어쓰는 플래그를 설정하고 다시 시도하세요.  |
|  TargetDiskBlobAlreadyExists  |  대상 이미지 Blob {0}에 활성 임대가 있어서 캡처 작업을 계속할 수 없습니다.   |
|  TargetDiskBlobAlreadyExists  |  Blob {0}이(가) 이미 있습니다. 다른 Blob URI를 디스크 '{1}'에 대한 대상으로 제공하세요.  |
|  TooManyVMRedeploymentRequests  |  VM '{0}' 또는 이 VM이 포함된 동일한 가용성 집합의 VM에 대한 재배포 요청을 너무 많이 받았습니다. 나중에 다시 시도하십시오.  |
|  VHDSizeInvalid  |  Blob {2}을(를) 사용하는 디스크 '{1}'에 {0}(이)라는 지정된 디스크 크기 값이 잘못되었습니다. 디스크 크기는 {3}에서 {4} 사이여야 합니다.  |
|  VMAgentStatusCommunicationError  |  VM '{0}'이(가) VM 에이전트 또는 확장의 상태를 보고하지 않았습니다. VM에 실행 중인 VM 에이전트가 있고 VM이 Azure Storage에 아웃바운드 연결을 설정할 수 있는지 확인하세요.  |
|  VMArtifactRepositoryInternalError  |  VM 아티팩트 세부 정보를 검색하기 위해 아티팩트 리포지토리와 통신하는 동안 오류가 발생했습니다.  |
|  VMArtifactRepositoryInternalError  |  아티팩트 리포지토리에서 VM 아티팩트 데이터를 검색하는 동안 내부 오류가 발생했습니다.  |
|  VMExtensionHandlerNonTransientError  |  처리기 '{0}'에서 VM 확장 '{1}'에 대한 오류를 보고했습니다. 터미널 오류 코드는 '{2}'이고 오류 메시지는 다음과 같습니다. '{3}'  |
|  VMExtensionManagementInternalError  |  VM 확장 '{0}'을(를) 처리하는 동안 내부 오류가 발생했습니다.  |
|  VMExtensionManagementInternalError  |  VM 확장을 준비하는 동안 여러 오류가 발생했습니다. 자세한 내용은 VM 확장 인스턴스 보기를 참조하세요.  |
|  VMExtensionProvisioningError  |  확장 '{0}'을(를) 처리하는 동안 VM이 오류를 보고했습니다. 오류 메시지: "{1}"  |
|  VMExtensionProvisioningError  |  여러 VM 확장은 VM에서 사용자를 프로비전하지 못했습니다. 자세한 내용은 VM 확장 인스턴스 보기를 참조하세요.  |
|  VMExtensionProvisioningTimeout  |  VM 확장 '{0}'의 프로비전이 시간을 초과했습니다. 확장 설치에 시간이 오래 걸릴 수 있습니다. 또는 확장 상태를 가져오지 못할 수 있습니다.  |
|  VMMarketplaceInvalidInput  |  Marketplace가 아닌 이미지로부터 가상 컴퓨터를 만드는 데에는 계획 정보가 필요하지 않습니다. 요청에서 계획 정보를 제거하세요. OS 디스크 이름은 {0}입니다.  |
|  VMMarketplaceInvalidInput  |  구매 정보가 일치하지 않습니다. Marketplace 이미지에서 배포할 수 없습니다. OS 디스크 이름은 {0}입니다.  |
|  VMMarketplaceInvalidInput  |  Marketplace 이미지로부터 가상 머신을 만들려면 요청에 계획 정보가 있어야 합니다. OS 디스크 이름은 {0}입니다.  |
|  VMNotFound  |  VM '{0}'을(를) 찾을 수 없습니다.  |
|  VMRedeploymentFailed  |  내부 오류가 발생하여 VM '{0}' 재배포에 실패했습니다. 나중에 다시 시도하십시오.  |
|  VMRedeploymentTimedOut  |  VM '{0}'의 다시 배포는 할당된 시간 안에 완료되지 않았습니다. 얼마 뒤면 성공적으로 완료될 수 있습니다. 또한 요청을 다시 시도할 수 있습니다.  |
|  VMStartTimedOut  |  VM '{0}'이(가) 할당된 시간에 시작하지 못했습니다. VM을 성공적으로 시작할 수 있습니다. 나중에 전원 상태를 확인하세요.  |


## <a name="next-steps"></a>다음 단계
도움이 더 필요하면 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의하세요. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
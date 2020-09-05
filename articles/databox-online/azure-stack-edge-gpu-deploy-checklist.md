---
title: Azure Stack Edge GPU 장치를 배포 하기 위한 배포 전 검사 목록 | Microsoft Docs
description: 이 문서에서는 Azure Stack Edge GPU 장치를 배포 하기 전에 수집할 수 있는 정보에 대해 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 9d7b3388b7e3a31c23b34b21017f012d40e9f849
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268145"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에 대 한 배포 검사 목록  

이 문서에서는 Azure Stack Edge 장치의 실제 배포에 앞서 수집할 수 있는 정보에 대해 설명 합니다. 

다음 검사 목록을 사용 하 여 Azure Stack Edge 장치에 대 한 주문을 하 고 장치를 받기 전에이 정보가 있는지 확인 합니다. 

## <a name="deployment-checklist"></a>배포 검사 목록 

| 단계                             | 매개 변수                                                                                                                                                                                                                           | 세부 정보                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 장치 관리               | <li>Microsoft Azure 구독</li><li>Azure Active Directory Graph API</li><li>Microsoft Azure Storage 계정</li>|<li>Azure Stack Edge/Data Box Gateway, 참가자 권한 사용</li><li>관리자 또는 사용자 액세스 권한 확인</li><li>액세스 자격 증명이 필요 합니다.</li> |
| 장치 설치               | 패키지의 전원 케이블 <br>우리에 게는 125 V에 대 한 SVE 18/3 케이블과 NEMA 5-15P to C13 (입/출력) 커넥터가 포함 된 15 암페어가 제공 됩니다.                                                                                                                                                                                                          | 장치와 함께 제공 됩니다.<br>자세한 내용은 [국가별로 지원 되는 전원 코드](azure-stack-edge-technical-specifications-power-cords-regional.md) 의 목록을 참조 하세요.                                                                                        |
|                                   | <li>포트 1에 대 한 하나 이상의 X 1-GbE RJ-45 네트워크 케이블  </li><li> 포트 3, 포트 4, 포트 5 또는 포트 6의 경우 최소 1 X 25gbe SFP + 구리 케이블</li>| 고객은 이러한 케이블을 조달 해야 합니다.<br>장치 네트워크 카드에 대해 지원 되는 네트워크 케이블, 스위치 및 트랜시버의 전체 목록은 [Cavium FastlinQ 41000 시리즈 상호 운용성 매트릭스](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 및 [Mellanox 이중 포트 25g connectx-3-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)을 참조 하세요.| 
| 첫 번째 장치 연결      | 포트 1에 초기 연결을 위한 고정 IP (192.168.100.10/24)가 있습니다. <li>192.168.100.0/24 네트워크에서 IP 주소를 사용 하 여 포트 1에 직접 연결 하는 데 랩톱이 필요 합니다.</li><li> 추가 구성의 경우 장치의 로컬 UI를 사용 `https://192.168.100.10` 합니다.</li><li> 초기 설치가 완료 되 면 장치에 대해 최소 1gbe 스위치를 사용 해야 합니다. 연결 된 스위치가 1gbe 이상이 아닌 경우 로컬 웹 UI에 액세스할 수 없습니다.</li>|                                                                                                                   |
| 장치 로그온                      | 장치 관리자 암호는 8 자에서 16 자 사이 여야 합니다. <br>암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다.                                            | 기본 암호는 처음 로그인 할 때 만료 되는 *Password1* 입니다.                                                     |
| 네트워크 설정                  | 장치는 2 x 1Gbe, 4 x 25 GbE 네트워크 포트와 함께 제공 됩니다. <li>포트 1은 관리 설정만 구성 하는 데 사용 됩니다. 하나 이상의 데이터 포트를 연결 하 고 구성할 수 있습니다. </li><li> 포트 2-포트 6 사이에서 하나 이상의 데이터 네트워크 인터페이스를 인터넷에 연결 해야 합니다 (Azure에 대 한 연결 사용).</li><li> IP 설정은 DHCP/고정 IPv4 구성을 지원 합니다. | 고정 IPv4 구성에는 IP, DNS 서버 및 기본 게이트웨이가 필요 합니다.                                                                                                                  |
| 네트워크 설정 계산     | <li>Kubernetes 노드에는 2 개의 정적 공용 Ip가 필요 하 고, 하나 이상의 고정 IP는 계산 모듈에 액세스 하는 Edge 허브 서비스 Azure Stack 합니다.</li><li>Kubernetes 클러스터 외부에서 외부에서 액세스 해야 하는 각 추가 서비스 또는 컨테이너에 대해 하나의 IP가 필요 합니다.</li>                                                                                                                       | 고정 IPv4 구성만 지원 됩니다.                                                                      |
| 필드 웹 프록시 설정     | <li>웹 프록시 서버 IP/FQDN, 포트 </li><li>웹 프록시 사용자 이름, 암호</li>                                                                                                                                                                                                    | 현재 compute 설정에서 지원 되지 않습니다.                                                                     |
| 방화벽 및 포트 설정        | 장치 Ip에 허용 되는 [나열 된 url 패턴 및 포트](azure-stack-edge-system-requirements.md#networking-port-requirements) 를 사용 합니다.                                                                                                                                                  |                                                                                                                   |
| 필드 MAC 주소            | MAC 주소를 허용 목록 해야 하는 경우 장치의 로컬 UI에서 연결 된 포트의 주소를 가져옵니다. |                                                                                                                   |
| 필드 네트워크 스위치 포트    | 장치에서 계산을 위한 Hyper-v Vm을 호스팅합니다. 일부 네트워크 스위치 포트 구성은 기본적으로 이러한 설정이 적용 되지 않습니다.                                                                                                        |                                                                                                                   |
| 바람직하지 시간 설정       | 표준 시간대, 기본 NTP 서버, 보조 NTP 서버를 구성 합니다.                                                                                                                                                                    | 로컬 네트워크에서 기본 및 보조 NTP 서버를 구성 합니다.<br>로컬 서버를 사용할 수 없는 경우 공용 NTP 서버를 구성할 수 있습니다.                                                    |
| 필드 서버 설정 업데이트 | <li>로컬 네트워크의 업데이트 서버 IP 주소, WSUS 서버에 대 한 경로를 설정 해야 합니다. </li> | 기본적으로 공용 windows 업데이트 서버가 사용 됩니다.|
| 디바이스 설정                   | <li>DNS 조직 내에 등록 된 장치 이름 </li><li>DNS 도메인</li> |                                                                                                                   |
| 필드 인증서                      | 장치에서 생성 한 인증서 사용 <br><br> 사용자 고유의 인증서를 가져오는 경우 다음이 필요 합니다. <li>*.Cer* 을 사용 하 여 DER 형식의 신뢰할 수 있는 루트 서명 인증서 </li><li>*Pfx* 형식의 끝점 인증서</li>|끝점 인증서에는 Azure Resource Manager, Blob storage, 로컬 웹 UI가 포함 됩니다.                                                                                                                   |
| 활성화                        | Azure Stack Edge/Data Box Gateway 리소스의 정품 인증 키가 필요 합니다.                                                                                                                                                       | 생성 된 키는 3 일 후에 만료 됩니다.                                                                        |


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치](azure-stack-edge-gpu-deploy-prep.md)배포를 준비 합니다.




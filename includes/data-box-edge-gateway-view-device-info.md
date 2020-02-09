---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182663"
---
1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 하 여는 `Get-HcsApplianceInfo` 장치에 대한 정보를 가져올 수 있습니다.

    다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    중요 한 장치 정보 중 일부를 요약 하는 표는 다음과 같습니다.
    
    | 매개 변수                             | 설명                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 장치를 배포 하는 동안 로컬 웹 UI 통해 구성 된 장치의 이름입니다. 기본 친숙한 이름은 디바이스 일련 번호입니다.  |   |
    | SerialNumber                   | 장치 일련 번호는 공장에서 할당 된 고유 번호입니다.                                                                             |   |
    | 모델                          | 가장자리가 상자의 데이터 또는 데이터 상자 게이트웨이 장치에 대한 모델입니다. 데이터 상자 게이트웨이에 대한 가상 및 실제 데이터 상자의 가장자리에 대한 모델은.                   |   |
    | FriendlySoftwareVersion        | 디바이스 소프트웨어 버전에 해당하는 친숙한 문자열입니다. 미리 보기를 실행 하는 시스템에 대한 친숙 한 소프트웨어 버전 데이터 상자 가장자리 1902 것입니다. |   |
    | HcsVersion                     | 사용자 디바이스에서 실행되는 HCS 소프트웨어 버전입니다. 예를 들어 데이터 상자 가장자리 1902에 해당 하는 HCS 소프트웨어 버전은 1.4.771.324 합니다.            |   |
    | LocalCapacityInMb              | 메가 비트에 장치의 로컬 총 용량입니다.                                                                                                        |   |
    | IsRegistered                   | 이 값 장치 서비스를 사용 하 여 활성화 하는 경우를 나타냅니다.                                                                                         |   |



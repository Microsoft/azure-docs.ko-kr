---
title: Azure Import/Export의 상태 보기 | Microsoft Docs
description: Import/Export 작업 및 사용되는 드라이브의 상태를 확인하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478048"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure Import/Export의 상태 보기

이 아티클에서는 Azure Import/Export 작업에 대한 드라이브 및 작업 상태를 확인하는 방법을 설명합니다. Azure Import/Export 서비스를 사용하여 Azure Blob 및 Azure Files에 대량의 데이터를 안전하게 전송합니다. 이 서비스는 Azure Blob Storage에서 데이터를 내보내는 데도 사용됩니다.  

## <a name="view-job-and-drive-status"></a>작업 및 드라이브 상태 보기
Azure Portal에서 가져오기 또는 내보내기 작업의 상태를 추적할 수 있습니다. **Import/Export** 탭을 클릭합니다. 작업 목록이 페이지에 나타납니다.

![작업 상태 보기](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>작업 상태 보기

프로세스에서 드라이브 위치에 따라 다음 작업 상태 중 하나가 표시됩니다.

| 작업 상태 | 설명 |
|:--- |:--- |
| 만드는 중 | 작업이 만들어지면 작업 상태는 **만드는 중**으로 설정됩니다. 작업이 **만드는 중** 상태인 동안 Import/Export 서비스에서는 드라이브를 데이터 센터로 운송하지 않았다고 가정합니다. 직업은 최대 2주 동안 이 상태를 유지할 수 있으며 그 이후에는 서비스에서 자동으로 삭제합니다. |
| 발송 | 패키지를 운송한 후 Azure Portal에서 추적 정보를 업데이트해야 합니다.  그러면 작업을 **운송 중** 상태로 전환합니다. 작업은 최대 2주 동안 **운송 중** 상태로 유지됩니다. 
| 수신됨 | 데이터 센터에서 모든 드라이브를 수신한 후에 작업은 **수신됨**으로 설정됩니다. |
| 전송 중 | 하나 이상의 드라이브에서 처리를 시작하면 작업 상태가 **전송 중**으로 설정됩니다. 자세한 내용은 [드라이브 상태](#view-drive-status)로 이동하세요. |
| 패키징 | 모든 드라이브에서 처리를 완료한 후에 고객에게 드라이브를 반송할 때까지 작업은 **포장 중** 상태가 됩니다. |
| Completed | 사용자에게 모든 드라이브를 반송한 후에 작업이 오류 없이 완료되면 작업은 **완료됨** 상태로 설정됩니다. **완료됨** 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |
| 닫힘 | 모든 드라이브를 사용자에게 다시 반송한 후에 작업을 처리하는 동안 오류가 발생하면 작업이 **종료됨** 상태로 설정됩니다. **종료됨** 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |

## <a name="view-drive-status"></a>드라이브 상태 보기

아래 표에서는 개별 드라이브가 가져오기 또는 내보내기 작업을 통해 전환할 때의 수명 주기를 설명합니다. 이제 Azure Portal에서 작업에 포함된 각 드라이브의 현재 상태를 볼 수 있습니다.

다음 표에서는 작업에 속한 각 드라이브가 통과할 수 있는 각각의 상태를 설명합니다.

| 드라이브 상태 | 설명 |
|:--- |:--- |
| 지정됨 | 가져오기 작업의 경우 Azure Portal에서 작업을 만들 때 드라이브의 초기 상태는 **지정됨**입니다. 내보내기 작업의 경우 작업을 만들 때 드라이브가 지정되지 않으므로 초기 드라이브 상태는 **수신됨**입니다. |
| 수신됨 | Import/Export 서비스가 가져오기 작업을 위해 운송 회사로부터 받은 드라이브를 처리할 때 드라이브가 **수신됨** 상태로 전환합니다. 내보내기 작업의 경우 초기 드라이브 상태는 **수신됨** 상태입니다. |
| 수신되지 않음 | 작업 패키지가 도착했지만 패키지에 드라이브가 포함되어 있지 않으면 해당 드라이브가 **NeverReceived** 상태로 전환합니다. 또한 서비스에서 운송 정보를 받은 후에 2주가 경과했지만 패키지가 데이터 센터에 아직 도착하지 않은 경우에도 드라이브가 이 상태로 전환됩니다. |
| 전송 중 | 서비스에서 드라이브로부터 Azure Storage로 데이터를 전송하기 시작하면 드라이브가 **전송 중** 상태로 전환합니다. |
| Completed | 서비스에서 모든 데이터를 오류 없이 성공적으로 전송하면 드라이브가 **완료됨** 상태로 전환합니다.
| 완료됨(추가 정보 필요) | 데이터를 드라이브 간에 복사하는 동안 서비스에 몇 가지 문제가 발생하면 드라이브가 **CompletedMoreInfo** 상태로 전환합니다. 정보에는 Blob 덮어쓰기에 대한 오류, 경고 또는 정보 메시지가 포함될 수 있습니다.
| 반송됨 | 드라이브가 데이터 센터에서 반송 주소로 반송되면 **ShippedBack** 상태로 전환합니다. |

Azure Portal의 이 이미지는 예제 작업의 드라이브 상태를 나타냅니다.

![드라이브 상태 보기](./media/storage-import-export-service/drivestate.png)

다음 표에서는 드라이브 오류 상태 및 각 상태에 대해 수행하는 작업을 설명합니다.

| 드라이브 상태 | 행사 | 해결 방법/다음 단계 |
|:--- |:--- |:--- |
| 수신되지 않음 | 작업 운송물의 일부로 받지 못하여 **NeverReceived**으로 표시된 드라이브가 다른 운송을 통해 도착했습니다. | 작업 팀은 드라이브를 **수신됨** 상태로 전환합니다. |
| N/A | 작업에 포함되지 않은 드라이브가 다른 작업의 일부로 데이터 센터에 도착했습니다. | 드라이브가 추가 드라이브로 표시되며 원래 패키지와 관련된 작업이 완료되면 사용자에게 반환됩니다. |

## <a name="time-to-process-job"></a>작업 처리 시간
Import/Export 작업을 처리하는 데 걸리는 시간은 다음과 같은 다양한 요인에 따라 달라집니다.

-  운송 시간
-  데이터 센터의 부하
-  작업 형식 및 복사되는 데이터의 크기
-  작업에서 디스크의 수 

Import/Export 서비스에는 SLA가 없지만 이 서비스는 디스크가 수신되고 7~10일 내에 복사를 완료하려고 합니다. Azure Portal에 게시된 상태 뿐만 아니라, REST API를 사용하여 작업 진행 상황을 추적할 수 있습니다. [작업 나열](/previous-versions/azure/dn529083(v=azure.100)) 작업의 API 호출에 포함된 완료율 매개 변수는 복사 진행률을 제공합니다.


## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](storage-import-export-tool-how-to.md)
* [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](storage-use-azcopy.md)
* [Azure Import/Export REST API 샘플](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

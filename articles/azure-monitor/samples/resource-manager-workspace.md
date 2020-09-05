---
title: Log Analytics 작업 영역에 대한 Resource Manager 템플릿 샘플
description: Log Analytics 작업 영역을 배포하고 Azure Monitor에서 데이터 원본을 구성하기 위한 샘플 Azure Resource Manager 템플릿입니다.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: b0b1914755ff0435318ab970aa6dc6e5e039d581
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855674"
---
# <a name="resource-manager-template-samples-for-log-analytics-workspaces-in-azure-monitor"></a>Azure Monitor의 Log Analytics 작업 영역에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure Monitor에서 Log Analytics 작업 영역을 만들고 구성하기 위한 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md) 샘플이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>템플릿 참조

- [Microsoft.OperationalInsights workspaces](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces) 
- [Microsoft.OperationalInsights workspaces/dataSources](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces/datasources)

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
다음 샘플에서는 빈 Log Analytics 작업 영역을 새로 만듭니다.

### <a name="notes"></a>메모

- **무료** 가격 책정 계층을 지정하는 경우 **retentionInDays** 요소를 제거합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "sku": {
          "type": "string",
          "allowedValues": [
            "pergb2018",
            "Free",
            "Standalone",
            "PerNode",
            "Standard",
            "Premium"
            ],
          "defaultValue": "pergb2018",
          "metadata": {
          "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
          "australiacentral", 
          "australiaeast", 
          "australiasoutheast", 
          "brazilsouth",
          "canadacentral", 
          "centralindia", 
          "centralus", 
          "eastasia", 
          "eastus", 
          "eastus2", 
          "francecentral", 
          "japaneast", 
          "koreacentral", 
          "northcentralus", 
          "northeurope", 
          "southafricanorth", 
          "southcentralus", 
          "southeastasia",
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
      }

      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-08-01",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "sku": {
      "value": "pergb2018"
    },
    "location": {
      "value": "eastus"
    },
    "resourcePermissions": {
      "value": true
    }
  }
}
```

## <a name="collect-windows-events"></a>Windows 이벤트 수집
다음 샘플은 [Windows 이벤트](../platform/data-sources-windows-events.md) 컬렉션을 기존 작업 영역에 추가합니다.

### <a name="notes"></a>메모

- 수집할 각 이벤트 로그에 대한 **datasources** 요소를 추가합니다. 각 로그에 대해 서로 다른 이벤트 유형 세트를 지정할 수 있습니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
  {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsSystem",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "System",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsApplication",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              },
              {
                "eventType": "Information"
              }
            ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-syslog"></a>syslog 수집
다음 샘플에서는 기존 작업 영역에 [syslog 이벤트](../platform/data-sources-syslog.md) 컬렉션을 추가합니다.

### <a name="notes"></a>메모

- 수집할 각 기능에 대한 **datasources** 요소를 추가합니다. 각 기능에 대해 서로 다른 심각도 세트를 지정할 수 있습니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogKern",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "kern",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        },
                        {
                            "severity": "notice"
                        },
                        {
                            "severity": "info"
                        },
                        {
                            "severity": "debug"
                        }
                    ]
                }
            },
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogDaemon",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "daemon",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "SyslogCollection",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslogCollection",
                "properties": {
                    "state": "Enabled"
                }
            }
        ]
      }
    ]
}

```


### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-windows-performance-counters"></a>Windows 성능 카운터 수집
다음 샘플은 [Windows 성능 카운터](../platform/data-sources-performance-counters.md) 컬렉션을 기존 작업 영역에 추가합니다.

### <a name="notes"></a>메모

- 수집할 각 카운터 및 인스턴스에 대한 **datasources** 요소를 추가합니다. 각 카운터와 인스턴스 조합에 대해 서로 다른 수집 빈도를 지정할 수 있습니다.
  
### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Location of the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryAvailableBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "Available MBytes "
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryPercentageBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "% Committed Bytes in Use"
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfProcessorPercentage",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Processor",
              "instanceName": "_Total",
              "intervalSeconds": 10,
              "counterName": "% Processor Time"
            }
          }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-linux-performance-counters"></a>Linux 성능 카운터 수집
다음 샘플에서는 기존 작업 영역에 [Linux 성능 카운터](../platform/data-sources-performance-counters.md) 컬렉션을 추가합니다.

### <a name="notes"></a>메모

- 수집할 각 개체 및 인스턴스에 대한 **datasources** 요소를 추가합니다. 각 개체 및 인스턴스 조합에 대해 서로 다른 카운터 세트를 지정할 수 있지만, 모든 카운터에 대해 단일 요율만 지정할 수 있습니다.
  
### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceLogicalDisk",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Logical Disk",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Used Inodes"
                        },
                        {
                            "counterName": "Free Megabytes"
                        },
                        {
                            "counterName": "% Used Space"
                        },
                        {
                            "counterName": "Disk Transfers/sec"
                        },
                        {
                            "counterName": "Disk Reads/sec"
                        },
                        {
                            "counterName": "Disk Writes/sec"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceProcessor",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Processor",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Processor Time"
                        },
                        {
                            "counterName": "% Privileged Time"
                        }
                    ]
                }
            }
        ]
      }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-custom-logs"></a>사용자 지정 로그 수집
다음 샘플에서는 기존 작업 영역에 [사용자 지정 로그](../platform/data-sources-custom-logs.md) 컬렉션을 추가합니다.

### <a name="notes"></a>메모

- 구분 기호 및 추출 구성은 복잡할 수 있습니다. 도움말을 보려면 **-Kind**가 **CustomLog**로 설정된 [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.8.0)를 사용하는 해당 구성 검색 및 Azure Portal을 사용하여 사용자 지정 로그를 정의합니다.

  
### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Specifies the location in which to create the workspace."
        }
      }
  },
  "resources": [
  {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "apiVersion": "2020-08-01",
            "type": "dataSources",
            "name": "[concat(parameters('workspaceName'), 'armlog_timedelimited')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
            ],
            "kind": "CustomLog",
            "properties": {
                "customLogName": "arm_log_timedelimited",
                "description": "this is a description",
                "inputs": [
                  {
                      "location": {
                        "fileSystemLocations": {
                            "linuxFileTypeLogPaths": [ "/var/logs" ],
                            "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                        }
                      },
                      "recordDelimiter": {
                        "regexDelimiter": {
                          "matchIndex": 0,
                          "numberdGroup": null,
                          "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                        }
                      }
                  }
                ],
                "extractions": [
                {
                    "extractionName": "TimeGenerated",
                    "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": [
                          {
                              "matchIndex": 0,
                              "numberdGroup": null,
                              "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                          }
                        ]
                    }
                    },
                    "extractionType": "DateTime"
                }
                ]
            }
        },
        {
          "apiVersion": "2020-08-01",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), 'armlog_newline')]",
          "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
              "customLogName": "armlog_newline",
              "description": "this is a description",
              "extractions": [],
              "inputs": [
                {
                    "location": {
                      "fileSystemLocations": {
                          "linuxFileTypeLogPaths": [ "/var/logs" ],
                          "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                      }
                    },
                    "recordDelimiter": {
                      "regexDelimiter": {
                        "pattern": "\\n",
                        "matchIndex": 0,
                        "numberdGroup": null
                      }
                    }
                }
              ],
              "extractions": [
                {
                  "extractionName": "TimeGenerated",
                  "extractionType": "DateTime",
                  "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": null,
                        "joinStringRegex": null
                    }
                  }
                }
              ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-iis-log"></a>IIS 로그 수집
다음 샘플에서는 기존 작업 영역에 [IIS 로그](../platform/data-sources-iis-logs.md) 컬렉션을 추가합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "type": "Microsoft.OperationalInsights/workspaces",
        "apiVersion": "2020-08-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "IISLog",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "IISLogs",
                "properties": {
                    "state": "OnPremiseEnabled"
                }
            }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```



## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](resource-manager-samples.md).
* [Log Analytics 작업 영역에 대해 자세히 알아봅니다](../learn/quick-create-workspace.md).
* [에이전트 데이터 원본에 대해 자세히 알아봅니다](../platform/agent-data-sources.md).

## 1. API Description

This API (DescribeAlarmList) is used to query the list of alarms. You can query any alarm that is generated by calling this API.

Domain name: monitor.api.qcloud.com


## 2. Input Parameters
| Parameter Name | Required | Type | Description |
|---------|---------|---------|---------|
| namespace | No | String | Namespace, which can be queried by calling the API <a href="/doc/api/255/查询命名空间" title="Query Namespace">Query Namespace</a> (DescribeNamespace). Alarms under all namespaces will be queried if this is left empty |
| metricName | No | String | Metric name, which can be queried by calling the API <a href="/doc/api/255/查询指标" title="Query Metric">Query Metric</a> (DescribeMetric). Alarms under all metrics will be queried if this is left empty |
| dimensions.n.name | No | String | Key of the dimension group. A key and a value are used together to identify a specific object of your interest. Alarms for all objects will be queried if this is left empty. The keys can be queried by calling the API <a href="/doc/api/255/查询指标对象列表" title="Query Metric Object List">Query Metric Object List</a> (DescribeObjects) |
| dimensions.n.value | No | String | Value of the dimension group, which can be queried by calling the API <a href="/doc/api/255/查询指标对象列表" title="Query Metric Object List">Query Metric Object List</a> (DescribeObjects) |
| starttime | No | datetime | Default start time is 00:00 of the current day |
| endtime | No | datetime | Default end time is the current time |
| offset | No | Int | Offset. Default is 0 (i.e. query result is displayed from the first alarm) |
| limit | No | Int	 | Number of rows to be displayed in the result. Default is 30. Starting from offset, this number of alarms are displayed |

"namespace" is required when you enter "metricName".
dimensions.n.name and dimensions.n.value always come in pairs. metricName and namespace are required when entering dimensions.n.name and dimensions.n.value.



## 3. Output Parameters
| Parameter Name | Type | Description |
|---------|---------|---------|
| code | Int | Error code, 0:  Successful. Other values: Failed. For more information, please see <a href="/doc/api/255/错误码" title="Error Codes">Common Error Codes</a> on the Error Codes page |
| message | String | Error message description. Null value indicates success |
| data | Array | This field exists if there is additional returned information |


"data" is composed as follows:

| Parameter Name | Type | Description |
|---------|---------|---------|
| alarmList | Array | Alarm list |
| total | Int | Number of alarms |

"alarmList" contains:

| Parameter Name | Type | Description |
|---------|---------|---------|
| metricName |	String |	Name of the metric related to the alarm |
| namespace |	String	| Namespace related to the alarm |
| object |	String	| Object related to the alarm |
| occurTime | String	| Occurrence time of the alarm, which is the time when the reported data met the alarm conditions for the first time. For example, if a user specifies that alarm should be triggered when average disk utilization stays above 80% in 10 minutes, then an occurrence time of 11:00 indicates that 
maximum disk utilization during 10:50-11:00 was beyond 80%. |
| recoverTime | String | Alarm recovery time. 0000-00-00 00:00:00 indicates the alarm has not recovered when the alarm list is queried.
If the reported data no longer meets the alarm conditions, alarm recovery time will be equal to the time when data returns to normal. |
| sendStatus |	Int	| Whether the alarm has been successfully sent. 0: Yes. Other values: No |
| okStatus |	Int |	Whether the alarm has recovered. 0: Not recovered. 1: Recovered. 2: Recovered (timed out) |
| smsSendCnt |	Int |	Number of alarm SMS messages that have been sent |
| content |	String	| Content of the alarm |
| alarmRuleId	| String	| Alarm rule ID |





## 4. Example
Input
<pre>
https://monitor.api.qcloud.com/v2/index.php?Action=DescribeAlarmList
&<<a href="https://www.qcloud.com/doc/api/229/6976">Common request parameters</a>>
&namespace=cvm
&metricName=diskusage
</pre>
Output
```
{
    "code": 0,
    "message": "",
    "data": {
        "alarmList": [
            {
                "metricName": "diskusage",
                "namespace": "cvm",
                "object": "ip=172.31.58.160&diskname=disk1",
                "occurTime": "2016-02-23 11:10:00",
                "recoverTime": "0000-00-00 00:00:00",
                "sendStatus": "0",
                "okStatus": "0",
                "smsSendCnt": "1",
				"alarmRuleId": "policy-f3h1bxvcsb",
				"content": ""Machine disk utilization", statistical granularity: 300 seconds. Condition "max>=80'%'" has persisted for 300 seconds"
            }
        ],
        "total": "1"
    }
}
```
As shown in the result, the maximum disk utilization for the disk "ip=172.31.58.160&diskname=disk1" was above 80% during 11:05:00-11:10:00.



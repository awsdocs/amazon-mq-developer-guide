# Broker statuses<a name="broker-statuses"></a>

A broker's current condition is indicated by a *status*\. The following table lists the statuses of an Amazon MQ broker\.


| Console | API | Description | 
| --- | --- | --- | 
| Creation failed | CREATION\_FAILED | The broker couldn't be created\. | 
| Creation in progress | CREATION\_IN\_PROGRESS | The broker is currently being created\. | 
| Deletion in progress | DELETION\_IN\_PROGRESS | The broker is currently being deleted\. | 
| Reboot in progress | REBOOT\_IN\_PROGRESS | The broker is currently being rebooted\. | 
| Running | RUNNING | The broker is operational\. | 
| Critical action required | CRITICAL\_ACTION\_REQUIRED | The broker is running, but is in a degraded state and requires immediate action\. You can find instructions to resolve the issue by chosing the action required code from the list in [Troubleshooting: Amazon MQ action required codes](troubleshooting-action-required-codes.md)\. | 
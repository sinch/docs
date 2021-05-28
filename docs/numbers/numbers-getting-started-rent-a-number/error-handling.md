---
title: Error Handling
excerpt: >- 
  Sinch's Numbers API provides the ability for customers to assign a US longcode to their SMS service plan and register the longcode on a 10dlc campaign. In this section, we will review potential errors that can occur during this asynchronous process and how those errors can be managed.
hidden: false
---

## How to retrieve number provisioning status
Customers can view the provisioning request status for a number using the GET /v1beta1/projects/{projectId}/activeNumbers or GET /v1beta1/projects/{projectId}/activeNumbers/{phoneNumber} endpoint (see API reference for additional detail).

## Parameters
While SMS configuration provisioning is being worked on, the numbers API will return a "scheduledProvisioning" object containing information about the provisioning request.
- smsConfiguration.scheduledProvisioning.servicePlanId - the requested servicePlanId that is currently being provisioned
- smsConfiguration.scheduledProvisioning.campaignId - the requested campaignId that is currently being provisioned
- smsConfiguration.scheduledProvisioning.status - The overall status for the request smsConfiguration.scheduledProvisioning.errorCodes - Any error messages encountered during provisioning

## Provisioning statuses
| Status | Description |
| --- | --- |
| WAITING | The configuration has been scheduled |
| IN_PROGRESS | The configuration change is in progress. If multiple configuration changes are requested, the Status will remain IN_PROGRESS until both requests complete. |
| FAILED | The configuration change has failed. If multiple configuration changes are requested, the Status will be Failed if either one fails. For example, if an servicePlanId and a campaignId are both being added to a number and the servicePlanId provisioning succeeds while the campaignId provisioning fails, the status will be marked as FAILED. |

## Provisioning error codes
| Error | Description | Suggested Action |
| --- | --- | --- |
| CAMPAIGN_NOT_AVAILABLE | Campaign not available in TCR | Verify the campaignId provided. Ensure Sinch has been selected as the Direct Connection Aggregator. |
| 10DLC_LIMIT_EXCEEDED | Too many longcodes have been added to the campaign. | MNOs place restrictions on the number of longcodes that can be added to a campaign without approval. If your use case requires more than 50 longcodes on a campaign, please contact Sinch for assistance. |
| NUMBER_PROVISIONING_FAILED | This number could not be provisioned to the campaign. | In rare cases, a number cannot be assigned to a campaign without manual intervention. Please release this number and rent another number, or contact Sinch for assistance. |
| PARTNER_SERVICE_UNAVAILABLE | We are unable to complete the campaign registration at this time. | This error indicates that Sinch will not continue to retry campaign submission. Please resubmit your campaign. |
| CAMPAIGN_PENDING_ACCEPTANCE | Sinch has not yet accepted this campaign.* | Please wait up to 24 hours for Sinch to accept your campaign and try again. Contact Sinch if additional assistance is needed. |
| MNO_SHARING_ERROR | The campaign and brand's details have not been shared between partners. | Verify that the campaign was submitted to all MNOs. For CSPs, this can be checked in TCR by verifying that the campaign has MNO Metadata for both T-Mobile and AT&T.
| CAMPAIGN_PROVISIONING_FAILED | The campaign could not be provisioned. | Please contact Sinch for assistance. |
| SMS_PROVISIONING_FAILED | The number could not be added to the specified SMS Serviceplan. | Please select a different serviceplan or contact Sinch for assistance. |
| INTERNAL_ERROR | Internal error. | Contact Sinch Support |

*May not apply to all customers

## Error management and campaign resubmission
If the provisioning of a number to a campaign failed with one of the above error codes, provisioning can be requested again using the PATCH /v1beta1/projects/{projectId}/activeNumbers/{phoneNumber}

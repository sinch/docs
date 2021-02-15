---
title: JavaScript Miscellaneous Verification SDK
excerpt: >-
  Find minimum requirements and information about encryption export regulations.
---
## Phone numbers

The phone number should be specified according to the E.164 number formatting (<http://en.wikipedia.org/wiki/E.164>) recommendation and should be prefixed with a ‘+’. E.g. to call the US phone number 415 555 0101, the phone number should be specified as “+14155550101”. The ‘+’ is the required prefix and the US country code ‘1’ is prepended to the local subscriber number.

## Encryption export regulations

Please check the Summary of U.S. Export Controls Applicable to Commercial Encryption Products and ensure that the application is registered for the Encryption Regulations, if applicable. It can be found under this [link](http://www.sinch.com/).

## Statistics

The Sinch SDK client uploads statistics to the Sinch servers at the end of a call, a call failure, or similar event. The statistics are used for monitoring of network status, call quality, and other aspects regarding the general quality of the service.

Some of the information is not anonymous and may be associated with the User ID call participants.

The statistics upload is done by the client in the background.

## Third party libraries and copyright notices

All Third Party Libraries and Copyright notices can be found under this [link](http://www.sinch.com/legal/third-party-licenses/).

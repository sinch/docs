---
title: Call Detail Records
excerpt: >-
  Call Detail Records (CDRs) can be downloaded from the Sinch portal.
---
Call Detail Records (CDRs) can be downloaded from the Sinch portal. CDRs are in a semicolon-delimited file that contains the following fields

<div class="magic-block-html">
    <div class="marked-table">
        <table>
            <thead>
            <tr class="header">
                <th>Field</th>
                <th>Type</th>
                <th>Description</th>
            </tr>
            </thead>
            <tbody>
            <tr class="odd">
                <td>VerificationId</td>
                <td>int</td>
                <td><p>A unique identifier for the verification request</p></td>
            </tr>
            <tr class="even">
                <td>UserSpaceId</td>
                <td>int</td>
                <td><p>Internal identifier</p></td>
            </tr>
            <tr class="odd">
                <td>Method</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Verification method. Can be flashCall, sms, callout or seamless</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>StartTimestamp</td>
                <td>time</td>
                <td><p>Time when the request was placed</p></td>
            </tr>
            <tr class="odd">
                <td>EndTimestamp</td>
                <td>time</td>
                <td><p>Time when the request was placed</p></td>
            </tr>
            <tr class="even">
                <td>Result</td>
                <td>string</td>
                <td>
                    <p>Shows the result of the verification. It can be:</p>
                    <ul>
                        <li>"SUCCESSFUL": Number verified successfully</li>
                        <li>"FAIL": Number was not verified</li>
                        <li>"DENIED": Verification request was blocked (for reasons such as low credit,fraud etc)</li>
                        <li>"ABORTED": Verification request was aborted, by initiating a new request</li>
                        <li>"ERROR": There was an error processing the request, such as network congestions or number unreachable</li>
                    </ul>
                </td>
            </tr>
            <tr class="odd">
                <td>Reason</td>
                <td>string</td>
                <td><p>Reason for the result of a verification</p></td>
            </tr>
            <tr class="even">
                <td>CLI</td>
                <td>string</td>
                <td><p>The CLI that was used for a flashcall. Empty for SMS</p></td>
            </tr>
            <tr class="odd">
                <td>IdentityType</td>
                <td>string</td>
                <td><p>The identity type of the endpoint verified</p></td>
            </tr>
            <tr class="even">
                <td>IdentityEndpoint</td>
                <td>number</td>
                <td><p>The number verified</p></td>
            </tr>
            <tr class="odd">
                <td>Country</td>
                <td>string</td>
                <td><p>The country ID (ISO 3166-1 alpha-2) of the number verified</p></td>
            </tr>
            <tr class="even">
                <td>Reference</td>
                <td>string</td>
                <td><p>Partner reference id</p></td>
            </tr>
            <tr class="odd">
                <td>Custom</td>
                <td>object</td>
                <td><p>Free field to be used as custom information</p></td>
            </tr>
            <tr class="even">
                <td>ApplicationKey</td>
                <td>string</td>
                <td><p>Application key</p></td>
            </tr>
            <tr class="odd">
                <td>Amount</td>
                <td>decimal</td>
                <td><p>Debited amount for the verification</p></td>
            </tr>
            <tr class="even">
                <td>Currency</td>
                <td>string</td>
                <td><p>Currency</p></td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

The files are generated once daily and contain the previous days' CDRs. A day spans from 00:00:00 UTC to 23:59:59 UTC. CDRs are written when the call is ended, though there are some edge cases where an app-app call CDR may be delayed in being written, for example, if there is a network failure before the call is ended.

CDR files can be downloaded from the developer portal. Upon request, the CDR files can also be uploaded to an S3 bucket that your company provides and to which Sinch has write access.

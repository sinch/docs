---
title: Call Detail Records
excerpt: >-
  Call Detail Records (CDRs) can be downloaded from the Sinch portal. A CDR is a
  semicolon-delimited file that contains... Read more.
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
                <td><blockquote>
                    <p>A unique identifier for the verification request</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>UserSpaceId</td>
                <td><blockquote>
                    <p>int</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Internal identifier</p>
                </blockquote></td>
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
                <td><blockquote>
                    <p>time</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Time when the request was placed</p>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>EndTimestamp</td>
                <td><blockquote>
                    <p>time</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Time when the request was placed</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>Result</td>
                <td>string</td>
                <td><blockquote>
                    <p>Shows the result of the verification. It can be:</p>
                    <blockquote>
                        <ul>
                            <li>"SUCCESSFUL": Number verified successfully</li>
                            <li>"FAIL": Number was not verified</li>
                            <li>"DENIED": Verification request was blocked (for reasons such as low credit,fraud etc)</li>
                            <li>"ABORTED": Verification request was aborted, by initiating a new request</li>
                            <li>"ERROR": There was an error processing the request, such as network congestions or number unreachable</li>
                        </ul>
                    </blockquote>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>Reason</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Reason for the result of a verification</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>CLI</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>The CLI that was used for a flashcall. Empty for SMS</p>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>IdentityType</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>The identity type of the endpoint verified</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>IdentityEndpoint</td>
                <td><blockquote>
                    <p>number</p>
                </blockquote></td>
                <td><blockquote>
                    <p>The number verified</p>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>Country</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>The country ID (ISO 3166-1 alpha-2) of the number verified</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>Reference</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Partner reference id</p>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>Custom</td>
                <td><blockquote>
                    <p>object</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Free field to be used as custom information</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>ApplicationKey</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Application key</p>
                </blockquote></td>
            </tr>
            <tr class="odd">
                <td>Amount</td>
                <td>decimal</td>
                <td><blockquote>
                    <p>Debited amount for the verification</p>
                </blockquote></td>
            </tr>
            <tr class="even">
                <td>Currency</td>
                <td><blockquote>
                    <p>string</p>
                </blockquote></td>
                <td><blockquote>
                    <p>Currency</p>
                </blockquote></td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

The files are generated once daily and contain the previous days' CDRs. A day spans from 00:00:00 UTC to 23:59:59 UTC. CDRs are written when the call is ended, though there are some edge cases where an app-app call CDR may be delayed in being written, for example, if there is a network failure before the call is ended.

CDR files can be downloaded from the developer portal. Upon request, the CDR files can also be uploaded to a S3 bucket that your company provides and to which Sinch has write access.

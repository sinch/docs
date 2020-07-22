---
title: WhatsApp FAQ
excerpt: Some frequently asked questions about the Sinch WhatsApp API.
---

## Sinch WhatsApp API

### How do I register my WhatsApp Business Account (WABA) with Sinch?

The process is described [here](https://clxgroup.sharepoint.com/:b:/s/CLX_Support_Anonymous_link/EfVIyWv9QSNBmGcIjIlbGtMBVAG_9li2TERg6xSCuvo56Q?e=bktwqU).

### What is the difference between a "WhatsApp Verified Business" and a "WhatsApp Official Verified Business"?

The primary noticeable difference is that the inbound message is identified as being from the "business name" with an official account and from the "number" with a regular business account.

https://faq.whatsapp.com/en/smba/26000090/?category=5245246

A business account on WhatsApp can either be listed as an "Official business account" or a regular "Business account". A business can't request or pay to turn a "Business account" into an "Official business account".

At this time, only certain business accounts are listed as "Official business account". Listing a business as an "Official business account" is based on a variety of factors, such as whether the brand is notable.

Following a series of rejections on clients we have submitted for Official business verification, WhatsApp have committed to refine and publish their approval criteria in July. When we receive it, we'll link it here.

### What is Two-Step Verification?

https://faq.whatsapp.com/en/smba/26000021/?category=5245245

Two-step verification is an optional feature that adds more security to your account. When you have two-step verification enabled, any attempt to verify your phone number on WhatsApp must be accompanied by the six-digit PIN that you created using this feature.

Two-Step verification is a mandatory step to achieve an "Official Business" verification.

The Sinch API handles these aspects automatically so there are no tasks required of the client.

### Can I use the same number for the Business API and a regular WhatsApp account?

No.

The process of binding (associating) the number to the Business API blocks that number being used to register a regular WhatsApp account.

Similarly, if a number is already bound to a regular WhatsApp account, it cannot be used for the Business API.

### Can I use a number that I already own as the number for the Business API?

Yes!

As long as you can collect a pin code verification delivered to it by SMS or Voice and provide it to Sinch within 10 minutes of receipt. (It has a very short Expiry - TTL).

### Where is Sinch referenced on the WhatsApp (Facebook) Solution Provider directory?

https://www.facebook.com/business/partner-directory/details?id=2357596414274628

### Why must the user Opt-In to receive messages?

The process of Opt-In to receive messages is compliant both with the WhatsApp requirement and with the GDPR regulations.

Because the first message preceding Opt-in must be a template message, that message has already been approved by WhatsApp and therefore the "Block" and "Mark as Spam" options are not available.

### What Media types are supported in the rich content Session Message?

- Document: PDF, DOC(X), PPT(X), XLS(X), ODT, ODS, ODP, RTF, or any of the types below
- Image: JPG, JPEG, PNG
- Audio: AAC, M4A, AMR, MP3, OPUS
- Video: MP4
- Sticker: WEBP

A message template can include a document, an image, or a video, but not an audio file or a sticker.

### Can a customer use their own MSISDN instead of something assigned by Sinch?

Yes, but its an additional manual process. As part of the onboarding, we’ll be required to ensure that the MSISDN is not already associated with a WhatsApp account. If it is, we’ll need to deprovision. It takes longer than it would to onboard a new MSISDN.

### Can a customer port a number or Business API from another Business solution provider (Sinch Competitor) to Sinch?

There is no process available from WhatsApp for "porting" or "Migrating" a Business API from a different Provider. The only option available to Sinch is to create a new API and associate it with a new MSISDN which has never previously been used.

### Is there a sandbox environment for testing before going live?

Yes. A simple tutorial and associated sandbox to enable a developer to send their first WhatsApp business API message are available.

https://www.sinch.com/blog/whatsapp-business-api-tutorial/

### Is it possible for a customer to test the APIs in a sandbox environment while having to wait for WhatsApp's validation?

The Sandbox uses a WhatsApp Account and MSISDN owned by Sinch so this testing can happen before the WhatsApp validation of a customer's individual account.

### What would the average lead time be for WhatsApp's validation/approval? 

While WhatsApp are in Limited availability, there is a new intake process which takes place every Friday (Driven by the Prospective Candidate list), with a turnaround by the following Thursday.

### Can a conversation begin without a message template?

If the business is initiating the conversation, it must always begin with sending an approved message template. The exceptions to this are:

(a) The end customer initiated the conversation

(b) The business is within the 24 hour session window

### Where do the Delivery notifications / Receipts (DLR's) go to?

The DLRs are dispatched to the Callback URL as specified by the client during the onboarding process. If this URL begins with https:// then the content is delivered securely.

## Pricing

### Does the client select a tier on the Direct Enterprise Pricing Plan?

Yes, the client selects a tier, but they may change this as often as they wish. The new tier selection will be activated at the start of the next month's billing cycle.

### When Are notification charges Incurred?

Notification charges are incurred only when a business initiates a conversation outside of an active customer care window. In this scenario, the business MUST use a pre-defined message template. These charges are defined by WhatsApp at https://developers.facebook.com/docs/whatsapp/pricing/#usd and are dependent on the notification volume from individual APIs to individual countries in a month. Volumes do not roll-up or aggregate in any scenario.

### How is a Monthly Active User (MAU) Defined?

A unique MSISDN (Subscriber phone number) which has sent or received a WhatsApp message via the Sinch API within the calendar month.

(For ASP plans, the MAU count is across all their API's (Shared MAU). For the Direct Enterprise Plan, it's across that Enterprise's APIs.)

### How does Sinch charge for customer care messages?

There are no further charges for customer care (session) messages. Each MAU covers UNLIMITED customer care messaging.

### Why is the ASP price per API lower than the Direct Enterprise Platform Fee?

By bundling APIs at volume, Sinch is able to better predict infrastructure, hosting charges as well as offload traditional sales and marketing functions to the ASP. We are able to pass these lower charges to the ASP in terms of aggressive price points.

### How does MAU overage work?

If the client's consumption, measured in MAU, exceeds a tier boundary, but it doesn't yet reach the next tier, then each additional MAU beyond the lower tier is counted as overage. E.g., a client consumes 30K MAU. This is between the 25K and 50K tier. They pay `$550` for the 25K Tier and a MAU overage charge of `5000*$0.016 = $80`. This means that the total charge would be `$550+$80 = $630`. (Plus Notification charges etc...).

The client will always pay the lower amount of (Next Higher Tier) OR (Lower Tier + Overage).

### What does "Included Template Message Definitions" Mean?

A business can only initiate a conversation outside of an active customer care window by using a notification. Notifications can only be sent using a predefined message template. Sinch registers these message templates with WhatsApp for approval. Once approved, the client can call them within the API.

Each template can include personalisation variables.

They are defined in the onboarding worksheet.

Sinch allows 250 such templates to be created.

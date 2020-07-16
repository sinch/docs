---
title: Application authentication with expiration time
excerpt: Application authentication with the Sinch SDK.
hidden: false
next:
  pages:
    - voice-android-cloud-calling
---

**User Instance (or Instance)**: is an _Application instance_ on a particular device registered with some _user identity_ against Sinch Service. A User might be registered on several devices, effectively having several registered _User Instances_.

Sometimes it is desirable to limit the time-to-live (TTL) of the _User Instance_ by providing an expiration date and time while registering the _user identity_ against Sinch Service. When such _Instance_ is expired it is deleted from the Sinch backend, effectively making impossible to either make or receive calls from the particular device the _Instance_ was registered from.

> ❗️
>
> Since each registered _User Instance_ is tied to a particular device, the expiration of the one _User Instance_ does not automatically makes other _Instances_ tied to the same user identity to expire.

> ❗️
>
> It is also possible to extend the expiration date while the _User Instance_ is not yet expired, but it is prohibited to indefinitely extend the expiration date of the _User Instance_ that was initially registered with _non-infinite_ TTL. In other words: if the _Instance_ was initially registered with limited TTL, its expiration update should also have limited TTL.


## Authenticating With Expiration Time

To set up the expiration data of the _User Instance_ one extra claim should be added to the _JWT_ registration token:

```
sinch:rtc:instance:exp
```

With it payload should have the following claims:

The JWT must contain the following _claims_:

| Claim                    | Value / Description                                                               | Note |
| :----------------------- | :-------------------------------------------------------------------------------- | ---- |
| `iss`                    | `//rtc.sinch.com/applications/{APPLICATION_KEY}`                                  |
| `sub`                    | `//rtc.sinch.com/applications/{APPLICATION_KEY}/users/{USER_ID}`                  |
| `iat`                    | See [JWT RFC 7519 section-4.1.1](https://tools.ietf.org/html/rfc7519#4.1.1)       |
| `exp`                    | See [JWT RFC 7519 section-4.1.4](https://tools.ietf.org/html/rfc7519#4.1.4)       |
| `nonce`                  | A unique cryptographic [nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) |
| `sinch:rtc:instance:exp` | Instance Expiration Time in the same format as `iat` and `exp`                    |

### An example of a possible JWT Header and Payload

```
# JWT Header
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "hkdfv1-20181206"
}

# JWT Payload
{
  "iss": "//rtc.sinch.com/applications/a32e5a8d-f7d8-411c-9645-9038e8dd051d",
  "sub": "//rtc.sinch.com/applications/a32e5a8d-f7d8-411c-9645-9038e8dd051d/users/johndoe",
  "iat": 1544111986,
  "exp": 1544115586,
  "nonce": "6b438bda-2d5c-4e8c-92b0-39f20a94b34e",
  "sinch:rtc:instance:exp": 1544284786
}
```

> ❗️
>
> Minimal TTL of the _Instance_ is 48 hours. In other words: `sinch:rtc:instance:exp` - `iat` >= 48 \* 3600

## Updating the _User Instance_ expiry time

### Automatic Update

The _automatic update_ is triggered on the `SinchClient` start if the proximity to the expiration date is detected. Proximity is calculated depending on the original TTL of the _User Instance_ to accommodate both long live _Instances_ and short live ones.

- if the original TTL was 8 days or more (e.g. a month or a year) an automatic update of the _User Instance_ expiry will be due 7 days prior the day of the expiry.
- if the original TTL was less than 8 days (a.g. a two days, or a week) an automatic update of the _User Instance_ expiry will be due 24 hours prior the day of the expiry.

> ❗️
>
> `SinchClient` does not schedule automatic _Instance_ expiry updates. It checks the condition on each start, so if the client was not run between _automatic expiry update data_ and _expiration date_ - the _User Instance_ TTL will not be updated and it will expire.

The `SinchClient` will fire a callback `onRegistrationCredentialsRequired()` the same way it does during the initial registration (see previous topic **Application authentication**).

If the update of the User Instance expiration date is not desirable, you can meet the callback with registerFailed(); in this way the Instance will still be valid, but no update of the expiration date would be performed, and the Instance will eventually expire.

If the update is desirable, provide a new JWT token with relevant `sinch:rtc:instance:exp` in `register(token)` and the very same _Instance_ will be updated with the new expiration date.

### Manual Update

There is no API to trigger of the _Instance Expiry_ manually, but one can achieve the same result by _unregistering_ and _re-registering_ again providing new expiration date in the JWT token.

## When _Instance_ Expires

The are three ways `SinchClient` detects that the _Instance_ is expired.

- on `SinchClient` start: it'll try to register new _Instance_
- on making a call: `SinchClient` will stop and fire relevant callback.
- on receiving a call: `SinchClient` will stop and fire relevant callback.

The last two situations might happen when `SinchClient` was last time started before the due time of the automatic expiration update, and was not stopped since then. Consequently, when `SinchClient` is started again it'll try to register new _Instance_.

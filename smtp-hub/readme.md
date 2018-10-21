# SMTP / SMTPS mail catcher docker image (based on postfix)
====

This is a simple Docker image with a postfix smtpd server configured to
catch all incoming mails, and "delivery" them to a local mailbox.

It supports PLAIN / LOGIN Auth via unix user logins.

This was created to test specific mail sending features from an application.
*IT MUST NOT BE USED IN PRODUCTION*.

The image contains:

* postfix
* rsyslog (required by postfix)
* sasl2 (cyrus) for authentication
* mutt (to view emails inside the container)
* runit (to manage all those processes)


Usage
---

```
# build the image (relpace <imagename>)
docker build -t <imagename> .

# choose a <password> to the mail user 'peter' (used for smtp auth), and start the container
docker run -d -e EMAIL_PASSWORD=<password> -p 127.0.0.1:1465:465 -p 127.0.0.1:1025:25 --name <container-name> <imagename>

# read stdout of the container
docker logs -f <container-name>
```

Open a second terminal, and send an email:

```
openssl s_client -crlf -connect localhost:1465
```

then, interactively talk to the server. To authenticate, you need the base64-encoded
username `base64_encode('peter') == 'cGV0ZXI='` and your choosen <password>

```
> 220 ESMTP Postfix
ehlo localhost
> ...
> 250-AUTH PLAIN LOGIN
> ...
AUTH LOGIN
> 334 VXNlcm5hbWU6
cGV0ZXI=
> 334 UGFzc3dvcmQ6
<base64(<password>)>
> 235 2.7.0 Authentication successful
mail from: <sender@example.com>
> 250 2.1.0 Ok
rcpt to: <recipient@example.com>
> 250 2.1.5 Ok
data
> 354 End data with <CR><LF>.<CR><LF>
subject: test

lorem ipsum
.
250 2.0.0 Ok: queued as A28F512A4641
QUIT
```

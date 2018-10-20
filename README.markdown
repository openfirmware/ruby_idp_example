# Ruby Identity Provider Example

A sample application that acts as a rudimentary SAML 2.0 Identity Provider. Intended for testing the SAML 2.0 Browser SSO Profile workflow for the OGC Secure Client Test Suite. It implements [HTTP Basic][] authentication challenge for the client.

[HTTP Basic]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#Basic_authentication_scheme

## Usage

This application requires Ruby 2.5 or newer. After installing Ruby, install the `bundler` gem, which will manage the other Ruby library dependencies.

```terminal
$ gem install bundler
```

Then use bundler to install this app's dependencies.

```terminal
$ bundle install
```

To start up the server:

```terminal
$ bundle exec script/server -o localhost -p 7000
```

The SAML 2.0 SSO callback url is `/saml/sso`, case sensitive. Service Providers should redirect clients to this URL for authentication.

When the client makes a GET request to the callback URL with the SAML Authentication Request query parameters, this Identity Provider will send back a `401 Unauthorized` response:

```
HTTP/1.1 401 Unauthorized
Date: <date string>
Access-Control-Access-Origin: *
WWW-Authenticate: Basic realm="SAML 2.0 IdP", charset="utf-8"
```

This includes a [header that requests HTTP Basic authentication][www-authenticate]. The client must then re-send the request with a valid HTTP Basic header. In the case of this Identity Provider, *any* username and password will be accepted.

```
GET /saml/sso?SAMLRequest=<DATA>&RelayState=<TOKEN> HTTP/1.1
Accept: */*
Host: localhost:7000
Authorization: Basic <base64 encoded credentials>
```

Please see the [HTTP Basic][authorization] documentation for how to format and encode the credentials.

Once the Identity Provider re-receives the request with the `Authorization` header set, it will parse the header to verify its format, and respond with the SAML 2.0 Authentication Response document.

```
HTTP/1.1 200 OK
Content-Type: text/xml

<base64 encoded SAML Authentication Response>
```

The client can then POST this document to the Service Provider SAML audience URL (parsed from the SAML Authentication Response) and continue the SAML workflow.

[authorization]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization
[www-authenticate]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/WWW-Authenticate

## License

Apache-2.0

## Authors

James Badger (<james@jamesbadger.ca>)

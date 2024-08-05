# Configuration reference

### ``RDM_CITATION_STYLES``

InvenioRDM has a configuration option called ``RDM_CITATION_STYLES`` which controls which citation styles will show up on the landing page:

``` python
RDM_CITATION_STYLES = [
     ('chicago-annotated-bibliography', _('Chicago')),
     ('ieee', _('IEEE')),
     ('science', _('Science')),
     ('apa', _('APA')),
     ('cell', _('Cell')),
]
```

### ``RDM_DEFAULT_CITATION_STYLE``

This option called ``RDM_DEFAULT_CITATION_STYLE`` controls which citation style will be the default one showing up on the landing page:

```
RDM_DEFAULT_CITATION_STYLE = 'chicago-annotated-bibliography'
```

Note that the default citation style must be one of the available configured above.

### ``APP_ALLOWED_HOSTS``

Invenio has a configuration option called ``APP_ALLOWED_HOSTS`` which controls which hosts/domain names can be served. A client request to a web server usually includes the domain name in the Host HTTP header:

```
GET /
Host: example.org
...
```

The web server uses that for instance to host several websites on the same domain. Also, the host header is usually used in a load balanced environment to generate links with the right domain name.

An attacker has full control of the host header and can thus change it to whatever they like, and for instance have the application generate links to a completely different domain.

Normally your load balancer/web server should only route requests with a white-listed set of hosts to your application. It is however very easy to misconfigure this in your web server, and thus Invenio includes a protective measure.

Simply set APP_ALLOWED_HOSTS to a list of allowed hosts/domain names:

```
APP_ALLOWED_HOSTS = ['www.example.org']
```

Failing to properly configure this variable will cause the error `Bad Request Host x.x.x.x is not trusted.` when starting the web app.

---
### ``WSGI_PROXIES``

Invenio is commonly used with both a load balancer and a web server in front of the application server. The load balancer and web server both work as proxies, which means that the clients remote address usually gets added in the X-Forwarded-For HTTP header. Invenio will automatically extract the clients IP address from the HTTP header, however to prevent clients from doing IP spoofing you need to specify exactly how many proxies you have in front of you application server:

```
WSGI_PROXIES = 2
```

---
### Secret keys

Probably the most important security measure is to have strong random secret keys set for you InvenioRDM instance.

Good practices:

- Never commit your secrets in the source code repository (or any other password for that sake).
- Use different secrets for different deployments (testing, staging, production).

List of secrets:

- `SECRET_KEY`: The secret key is used for instance to sign user session ids and encrypt certain database fields. The secret key must be kept secret. If the key is leaked or stolen somehow, you should immediately change it to a new key.
- `SECURITY_LOGIN_SALT`: The salt value is used when generating login links/tokens.
- `CSRF_SECRET_SALT`: The salt value is used when generating login CSRF tokens.

```python
SECRET_KEY = '..put a long random value here..'
SECURITY_LOGIN_SALT = '..put a long random value here..'
CSRF_SECRET_SALT = '..put a long random value here..'
```

---
### Content Security Policy (CSP)

Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks, including Cross-Site Scripting (XSS) and data injection attacks.
To configure such headers, change the var `APP_DEFAULT_SECURE_HEADERS` in ``invenio.cfg``:

```python
APP_DEFAULT_SECURE_HEADERS = {
    ...
}
```

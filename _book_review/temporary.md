---
stars: 1
---

임시 페이지

```apache
AddDefaultCharset UTF-8

RewriteEngine On

# Serve gzipped version if available and accepted
AddEncoding x-gzip .gz
RewriteCond %{HTTP:Accept-Encoding} gzip
RewriteCond %{REQUEST_FILENAME}.gz -f
RewriteRule ^(.*)$ $1.gz [QSA,L]
<FilesMatch \.css\.gz$>
  ForceType text/css
  Header append Vary Accept-Encoding
</FilesMatch>
<FilesMatch \.js\.gz$>
  ForceType application/javascript
  Header append Vary Accept-Encoding
</FilesMatch>
<FilesMatch \.html\.gz$>
  ForceType text/html
  Header append Vary Accept-Encoding
</FilesMatch>
```

```nginx
server {
  listen          80;
  server_name     example.com *.example.com;
  rewrite ^       http://www.domain.com$request_uri? permanent;
}
```

```python
def fib(n):    # write Fibonacci series up to n
    """Print a Fibonacci series up to n."""
    a, b = 0, 1
    while a < n:
        print a,
        a, b = b, a+b
```
```java
public class java {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

```php
<?php
  print("Hello {$world}");
?>
```

```yaml
one: Mark McGwire
two: Sammy Sosa
three: Ken Griffey
```

```html
<html>
<head>
</head>
<body>
<h1>hello</h1>
</body>
</html>
```

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
```
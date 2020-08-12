---
stars: 1
---

임시 페이지
# 😃Heading #1
## 🐶Heading #2
### 🍎Heading #3
#### ⚽️Heading #4
##### 🚗Heading #5
###### 👍Heading #6

> Lorem ipsum dolor sit amet, consectetur adipiscing elit.
Curabitur eros lorem, laoreet in enim at, sodales tempor quam. Etiam sit amet mattis ligula.
>
> Etiam sit amet mattis ligula.  - someone

링크<br>
[Dooray!](https://dooray.com/)

---
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
<br>
```nginx
server {
  listen          80;
  server_name     example.com *.example.com;
  rewrite ^       http://www.domain.com$request_uri? permanent;
}
```
<br>
```python
def fib(n):    # write Fibonacci series up to n
    """Print a Fibonacci series up to n."""
    a, b = 0, 1
    while a < n:
        print a,
        a, b = b, a+b
```
<br>
```java
public class java {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```
<br>
```php
<?php
  print("Hello {$world}");
?>
```
<br>
```yaml
one: Mark McGwire
two: Sammy Sosa
three: Ken Griffey
```
<br>
```html
<html>
<head>
</head>
<body>
<h1>hello</h1>
</body>
</html>
```
<br>
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
<br>
```https://media.vlpt.us/images/kidow/post/d0b13054-2feb-4352-941e-1af78b11093d/49d537ae09031c5f5eba68ac194d4ed8.jpeghttps://media.vlpt.us/images/kidow/post/d0b13054-2feb-4352-941e-1af78b11093d/49d537ae09031c5f5eba68ac194d4ed8.jpeg
# If not running interactively, don't do anything
[[ -z "$PS1" ]] && return
```
<br>
```html
POST /demo/submit/ HTTP/1.1
Host: rouge.jneen.net
Cache-Control: max-age=0
Origin: http://rouge.jayferd.us
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_2)
    AppleWebKit/535.7 (KHTML, like Gecko) Chrome/16.0.912.63 Safari/535.7
Content-Type: application/json
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Referer: http://pygments.org/
Accept-Encoding: gzip,deflate,sdch
Accept-Language: en-US,en;q=0.8
Accept-Charset: windows-949,utf-8;q=0.7,*;q=0.3

{"name":"test","lang":"text","boring":true}
```
<br>

```sql
SELECT * FROM `users` WHERE `user`.`id` = 1
```
```bash
[root@ip-10-0-0-253 ~]# mkdir -p $HOME/.kube
[root@ip-10-0-0-253 ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@ip-10-0-0-253 ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config
[root@ip-10-0-0-253 ~]# kubectl get node
NAME                                            STATUS     ROLES    AGE   VERSION
ip-10-0-0-253.ap-northeast-2.compute.internal   NotReady   master   27s   v1.18.6
```

```uml
Alice -> Bob: Authentication Request
Bob --> Alice: Authentication Response

Alice -> Bob: Another authentication Request
Alice <-- Bob: another authentication Response
```


---
* [두레이 마크다운](https://nhnent.dooray.com/htmls/guides/markdown_ko_KR.html)
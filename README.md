# spring-security-client

## 로그인 동작 순서

`http://auth-server:9393/articles` 접속하게되면 SecurityConfig 에 설정한 코드 덕분에 `anyRequet()` 모든 요청을 `authenticated()` 인증 한다.

```java
http
    .authorizeRequests(authorizeRequests ->
    authorizeRequests.anyRequest().authenticated()
    )
```

권한이 없으므로 다음 세팅한 바와 같이 로그인을 위해 `http://auth-server:9393/oauth2/authorization/articles-client-oidc` 로 리다이렉션 된다.

```java
.oauth2Login(oauth2Login ->
    oauth2Login.loginPage("/oauth2/authorization/articles-client-oidc"))
    .oauth2Client(withDefaults());
```

해당 URL 이동시 yml에 세팅한 곳으로 이동한다. `http://auth-server:9191/oauth2/authorize?response_type=code&client_id=articles-client&scope=openid&state=_zu9UmVfwZBzohmR2bLy_RL49VJyMJNziVv1NXgkXGs%3D&redirect_uri=http://auth-server:9393/login/oauth2/code/articles-client-oidc&nonce=w0i3OpC7qhNE-YPsXF12zU_96P0O_OoPbxW8G9r4Wys`

```yml
articles-client-oidc:
    provider: spring
    client-id: articles-client
    client-secret: secret
    authorization-grant-type: authorization_code
    redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"
    scope: openid
    client-name: articles-client-oidc
```
key|value|yml setting|내용
:---:|:---:|:---:|---
response_type|code|authorization-grant-type: authorization_code|
client_id|articles-client|client-id: articles-client|
scope|openid|scope: openid|
redirect_uri|http://auth-server:9393/login/oauth2/code/articles-client-oidc|redirect-uri:...
state|_zu9UmVfwZBzohmR2bLy_RL49VJyMJNziVv1NXgkXGs%3D|
nonce|w0i3OpC7qhNE-YPsXF12zU_96P0O_OoPbxW8G9r4Wys|
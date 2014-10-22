
Nebula 登入
================

目前 Nebula 2.0 支援的登入驗證是 [OAuth 2.0](http://oauth.net/2/)，你可以簡單的透過一些設定，使用 [CAMP](http://www.quanta-camp.com) 網站的帳號提供使用者作登入驗證  

登入驗證
----------------

如果需要與 [CAMP](http://www.quanta-camp.com) 網站登入帳號結合，可透過以下方式作設定  
這裡提供 [Spring security OAuth2](http://projects.spring.io/spring-security-oauth/docs/Home.html) 與 [Scribe-java](https://github.com/fernandezpablo85/scribe-java) 兩個 framework 範例  

Spring Security OAuth2
----------------

以下的設定為參考範例。

需於spring-config.xml寫入以下設定

```xml
<http auto-config="true" xmlns="http://www.springframework.org/schema/security">
	<intercept-url pattern="/**" access="IS_AUTHENTICATED_ANONYMOUSLY" />
	<custom-filter ref="oauth2ClientFilter" after="EXCEPTION_TRANSLATION_FILTER" />
</http>

<authentication-manager xmlns="http://www.springframework.org/schema/security">
	<authentication-provider>
		<user-service>
			<user name="test" password="test" authorities="ROLE_USER" />
		</user-service>
	</authentication-provider>
</authentication-manager>

<!--apply the oauth client context -->
<oauth:client id="oauth2ClientFilter" />

<bean id="quanta"
	class="com.quanta.nebula.security.ExtendedBaseOAuth2ProtectedResourceDetails">
	<property name="clientId" value="***"></property>
	<property name="clientSecret" value="***"></property>
	<property name="accessTokenUri" value="http://www.quanta-camp-com/OpenId/OAuth/Token"></property>
	<property name="userAuthorizationUri" value="http://www.quanta-camp-com/OpenId/OAuth/Authorize"></property>
	<property name="scope"
		value="http://www.quanta-camp-com/OpenId/Profile/UserIdentity">
	</property>
</bean>

<bean id="quantaController" class="com.quanta.nebula.security.QuantaController">
	<property name="quantaRestTemplate">
		<oauth:rest-template resource="quanta"/>
	</property>
</bean>
```

為避免進入spring-security的認證頁面, 需改寫 isClientOnly 屬性, 範例如下  

ExtendedBaseOAuth2ProtectedResourceDetails.java  

```java
public class ExtendedBaseOAuth2ProtectedResourceDetails extends AuthorizationCodeResourceDetails {

	public boolean isClientOnly() {
		return true;
	}

}
```

### 參考

- [Spring Security OAuth](http://docs.spring.io/spring-security/oauth/) 文件首頁
- [Github](https://github.com/spring-projects/spring-security-oauth)

Scribe-java
----------------

先透過Authorize url取得code

```html
<a href="http://www.quanta-camp.com/OpenId/OAuth/Authorize?client_id=＊&redirect_uri=http%3A%2F%2Flocalhost%3A8090%2Fquantacallback.jsp&response_type=code&scope=http%3A%2F%2Fwww.quanta-camp.com%2FOpenId%2FProfile%2FUserIdentity&state=cLzMFn">以Quanta 帳戶登入</a>
```

登入驗證並取得resource
```java
String _sCode = request.getParameter("code");

Verifier verifier = new Verifier(_sCode);
String apiKey = "**請填入 ProductId **";
String apiSecret = "** 請填入 AuthorityCode **";
String redirect_uri = "http://localhost:8090/quantacallback.jsp";

OAuthService service = new ServiceBuilder()
    .provider(QuantaApi20.class)
    .apiKey(apiKey)
    .apiSecret(apiSecret)
    .callback(redirect_uri)
    .scope("http://www.quanta-camp.com/OpenId/Profile/UserIdentity")
    .build();

Token accessToken = service.getAccessToken(null, verifier);

// Now let's go and ask for a protected resource!
OAuthRequest req = new OAuthRequest(Verb.GET, "http://www.quanta-camp.com/OpenId/Profile/UserIdentity");
service.signRequest(accessToken, req);
Response resp = req.send();
// Got it! Lets see what we found...
String _sBody = resp.getBody();
```

QuantaApi20 範例

```java
public class QuantaApi20 extends DefaultApi20 {

	// TODO: 建議 state 參數傳遞 Session Id, 避免各 User 取到的 Access Token 發生衝突
	private static final String QUANTA_CAMP_OAUTH_PROVIDER_HOST = "http://www.quanta-camp.com/OpenId";
	private static final String AUTHORIZE_URL = QUANTA_CAMP_OAUTH_PROVIDER_HOST + "/OAuth/Authorize?response_type=code&client_id=%s&redirect_uri=%s&scope=%s&state=cLzMFn";

	@Override
	public String getAccessTokenEndpoint() {
		return QUANTA_CAMP_OAUTH_PROVIDER_HOST + "/OAuth/Token";
	}

	@Override
	public String getAuthorizationUrl(OAuthConfig config) {
		Preconditions.checkValidUrl(config.getCallback(),
				"Must provide a valid url as callback. Google does not support OOB");
		Preconditions.checkEmptyString(config.getScope(),
				"Must provide a valid value as scope. Google does not support no scope");

		String _sResult = "";
		try {
			_sResult = String.format(AUTHORIZE_URL, config.getApiKey(),
					URLEncoder.encode(config.getCallback(), "UTF-8"),
					URLEncoder.encode(config.getScope(), "UTF-8"));
		} catch (UnsupportedEncodingException uee) {
			throw new IllegalStateException("Cannot find specified encoding: UTF-8");
		}

		return _sResult;
	}

	@Override
	public Verb getAccessTokenVerb() {
		return Verb.POST;
	}

	@Override
	public OAuthService createService(OAuthConfig config) {
		return new QuantaOAuthService(this, config);
	}

	@Override
	public AccessTokenExtractor getAccessTokenExtractor() {
		return new QuantaJsonTokenExtractor();
	}

	private static final class QuantaOAuthService implements OAuthService {
		private static final String VERSION = "2.0";

		private static final String GRANT_TYPE = "grant_type";
		private static final String GRANT_TYPE_VALUE = "authorization_code";

		private final DefaultApi20 api;
		private final OAuthConfig config;

		/**
		 * Default constructor
		 *
		 * @param api
		 *                OAuth2.0 api information
		 * @param config
		 *            OAuth 2.0 configuration param object
		 */
		public QuantaOAuthService(DefaultApi20 api, OAuthConfig config) {
			this.api = api;
			this.config = config;
		}

		/**
		 * {@inheritDoc}
		 */
		public Token getAccessToken(Token requestToken, Verifier verifier) {
			OAuthRequest request = new OAuthRequest(api.getAccessTokenVerb(), api.getAccessTokenEndpoint());
			request.addBodyParameter(OAuthConstants.CLIENT_ID, config.getApiKey());
			request.addBodyParameter(OAuthConstants.CLIENT_SECRET, config.getApiSecret());
			request.addBodyParameter(OAuthConstants.CODE, verifier.getValue());
			request.addBodyParameter(OAuthConstants.REDIRECT_URI, config.getCallback());
			if (config.hasScope())
				request.addBodyParameter(OAuthConstants.SCOPE, config.getScope());
			request.addBodyParameter(GRANT_TYPE, GRANT_TYPE_VALUE);
			Response response = request.send();
			return api.getAccessTokenExtractor().extract(response.getBody());
		}

		/**
		 * {@inheritDoc}
		 */
		public Token getRequestToken() {
			throw new UnsupportedOperationException(
					"Unsupported operation, please use 'getAuthorizationUrl' and redirect your users there");
		}

		/**
		 * {@inheritDoc}
		 */
		public String getVersion() {
			return VERSION;
		}

		/**
		 * {@inheritDoc}
		 */
		public void signRequest(Token accessToken, OAuthRequest request) {
			request.addHeader("Authorization", "Bearer " + accessToken.getToken());
		}

		/**
		 * {@inheritDoc}
		 */
		public String getAuthorizationUrl(Token requestToken) {
			return api.getAuthorizationUrl(config);
		}
	}

	private static final class QuantaJsonTokenExtractor implements AccessTokenExtractor {
		private Pattern accessTokenPattern = Pattern.compile("\"access_token\"\\s*:\\s*\"(\\S*?)\"");

		public Token extract(String response) {
			Preconditions.checkEmptyString(response, "Cannot extract a token from a null or empty String");
			Matcher matcher = accessTokenPattern.matcher(response);
			if (matcher.find()) {
				return new Token(matcher.group(1), "", response);
			} else {
				throw new OAuthException("Cannot extract an acces token. Response was: " + response);
			}
		}
	}
}
```

### 參考

- [Github](https://github.com/fernandezpablo85/scribe-java)

Nebula Login
================

The current login authentication Nebula 2.0 supports is [OAuth 2.0](http://oauth.net/2/). With it and some simple settings, you can easily use [CAMP](http://www.quanta-camp.com) portal accounts to provide user login authentication.

## Login Authentication
----------------

If you want to integrate with [CAMP](http://www.quanta-camp.com) portal login accounts,  you can use the following steps to setup.
Here are two seperate framework examples to use [Spring security OAuth2](http://projects.spring.io/spring-security-oauth/docs/Home.html) and [Scribe-java](https://github.com/fernandezpablo85/scribe-java).

Spring Security OAuth2
----------------

Below is an example:

spring-config.xml is required in the following setting

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

The following example is to avoid entering spring-security authentication page; this requires to modify the `isClientOnly` property.

ExtendedBaseOAuth2ProtectedResourceDetails.java  

```java
public class ExtendedBaseOAuth2ProtectedResourceDetails extends AuthorizationCodeResourceDetails {

	public boolean isClientOnly() {
		return true;
	}

}
```

### Reference

- [Spring Security OAuth](http://docs.spring.io/spring-security/oauth/) document homepage
- [Github](https://github.com/spring-projects/spring-security-oauth)

Scribe-java
----------------

Obtain the code through Authorize url

```html
<a href="http://www.quanta-camp.com/OpenId/OAuth/Authorize?client_id=＊&redirect_uri=http%3A%2F%2Flocalhost%3A8090%2Fquantacallback.jsp&response_type=code&scope=http%3A%2F%2Fwww.quanta-camp.com%2FOpenId%2FProfile%2FUserIdentity&state=cLzMFn">Login with Quanta account</a>
```

Login authication and obtain the resource
```java
String _sCode = request.getParameter("code");

Verifier verifier = new Verifier(_sCode);
String apiKey = "** Please fill in ProductId **";
String apiSecret = "** Please fill in AuthorityCode **";
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

QuantaApi20 Sample

```java
public class QuantaApi20 extends DefaultApi20 {

	// TODO: Recommendations state parameter passing Session Id, User taken to avoid all conflicts of Access Token
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

### Reference

- [Github](https://github.com/fernandezpablo85/scribe-java)

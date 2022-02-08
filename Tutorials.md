# Partner API
# Samples and Tutorials
The code snippet provided on this page are only for exemplification purpose, as tutorial, it makes sense to keep it simple for easy understanding 
and making easy to display code snippet, so code quality, security, libraries, etc. should not be taken for granted.
# Postman collection 
you can find the postman collection [here](https://www.getpostman.com/collections/45d35533417147f71979).
# Java Apache HttpClient full implementation of a client
Below you find am example of a complete client for partner API, adding certificate for client side, using OAuth2 authorization, and subscription key in the header
```
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.databind.ObjectMapper;
import lombok.Data;
import org.apache.http.ssl.SSLContexts;

import javax.net.ssl.SSLContext;
import java.io.File;
import java.net.URI;
import java.net.URLEncoder;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.nio.charset.StandardCharsets;
import java.security.*;
import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;

public class PartnerAPIClient {

    private static final String KEYSTORE_PASSWORD = "change for your keystore password";
    private static final String KEYSTORE_PATH = "changed to your certificate path, file e.g certificate.p12"; 
    private static final String CLIENT_ID = "change for you client id";
    private static final String CLIENT_SECRET = "change for you client secret"; 
    private static final String PARTNER_API_BASE_URL = "https://api.sandbox.lendico.de/partner-api"; 
    private static final String TOKEN_URL = PARTNER_API_BASE_URL + "/oauth2/token";
    private static final String SUBSCRIPTION_KEY = "change to your subscription key"; 


    public static void main(String[] args) throws IOException, GeneralSecurityException, InterruptedException {
        pingRequest();
    }


    public static void pingRequest() {

        var partnerPingRequest = HttpRequest.newBuilder()
                .uri(URI.create(PARTNER_API_BASE_URL + "/ping"))
                .header("Authorization", "Bearer " + getToken())// OAuth2 authentication
                .header("Subscription-Key", SUBSCRIPTION_KEY)// Add Subscription-Key header
                .build();

        var httpClient = HttpClient.newBuilder().sslContext(generateSSLContext()).build();
        var response = httpClient.send(partnerPingRequest, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());

    }

    private static SSLContext generateSSLContext(){
        // Load keystore file
        var keyStoreFile = new File(KEYSTORE_PATH);
        return SSLContexts.custom().loadKeyMaterial(keyStoreFile,
                KEYSTORE_PASSWORD.toCharArray(), KEYSTORE_PASSWORD.toCharArray(),
                (aliases, socket) -> aliases.keySet().iterator().next()
        ).build();
    }

    public static String getToken(){
        Map<String, String> parameters = new HashMap<>();
        parameters.put("client_id", CLIENT_ID);
        parameters.put("client_secret", CLIENT_SECRET);

        var formData = parameters.keySet().stream()
                .map(key -> key + "=" + URLEncoder.encode(parameters.get(key), StandardCharsets.UTF_8))
                .collect(Collectors.joining("&"));
        var httpClient = HttpClient.newBuilder().sslContext(generateSSLContext()).build();
        var request = HttpRequest.newBuilder().uri(URI.create(TOKEN_URL))
                .header("Content-Type", "application/x-www-form-urlencoded")
                .header("Subscription-Key", SUBSCRIPTION_KEY)// Add Subscription-Key header
                .POST(HttpRequest.BodyPublishers.ofString(formData)).build();

        var response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
        var token = new ObjectMapper().readValue(response.body(), Token.class);
        return token.getAccessToken();
    }
}

@Data
@JsonIgnoreProperties(ignoreUnknown = true)
class Token {
    @JsonProperty("token_type")
    private String token_type;

    @JsonProperty("expires_in")
    private long expiresIn;

    @JsonProperty("ext_expires_in")
    private long extExpiresIn;

    @JsonProperty("access_token")
    private String accessToken;
}
    
```


# Generating Self-Signed certificate with openssl


#1. Generate a new RSA private key. 
#The password of the private key can be entered during execution of the command(due arg stdin), avoiding it to be listed in the shell history.
```openssl genrsa -out server.key -passout stdin 2048```

#2. Generate the X.509 Certificate Signing Request
```openssl req -sha256 -new -key server.key -out server.csr```

#3. Sign the X.509 certificate with your own private key
```openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server_public.crt```

#4. Pack the key and the crt file into a pfx file to send to us.
```openssl pkcs12 -export -out server.pfx -inkey server.key -in server_public.crt```
NOTE: In the step 4 will ask you for a password for packing the files into PFX file, in case you use a password here you will need to send us the password.

# In case using java as in the tutorial example, you can generate a pkcs12 file for the key store as below
```openssl pkcs12 -export -in server_public.crt -inkey server.key -out server-cert.p12```

# Generating pfx file from key and certificate
For generating pfx file from key and certificate you can follow the following steps using `openssl`
```openssl pkcs12 -export -out domain.name.pfx -inkey domain.name.key -in domain.name.crt```

If you have a root CA and intermediate certs, then include them as well using multiple -in params
```openssl pkcs12 -export -out domain.name.pfx -inkey domain.name.key -in domain.name.crt -in intermediate.crt -in rootca.crt```

Example:
```openssl pkcs12 -export -out client-certificate.pfx -inkey client-key.key -in client-cert.crt```
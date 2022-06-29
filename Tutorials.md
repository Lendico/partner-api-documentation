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
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URI;
import java.net.URLEncoder;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.nio.charset.StandardCharsets;
import java.security.*;
import java.security.cert.CertificateException;
import java.time.ZoneOffset;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Base64;
import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;

public class PartnerAPIClient {

    private static final String KEYSTORE_PASSWORD = "change for your keystore password";
    private static final String KEYSTORE_PATH = "changed to your keystore path(with both, message signing and tls certificate), file e.g keystore.jks"; 
    private static final String CLIENT_ID = "change for you client id";
    private static final String CLIENT_SECRET = "change for you client secret"; 
    private static final String PARTNER_API_BASE_URL = "https://api.sandbox.lendico.de/partner-api"; 
    private static final String TOKEN_URL = PARTNER_API_BASE_URL + "/oauth2/token";
    private static final String SUBSCRIPTION_KEY = "change to your subscription key"; 
    private static final DateTimeFormatter HEADER_DATE_FORMAT = DateTimeFormatter.ofPattern("EEE, dd MMM yyyy HH:mm:ss z");
    private static KeyStore keyStore;

    public static void main(String[] args) throws IOException, GeneralSecurityException, InterruptedException {
        keyStore = KeyStore.getInstance("JKS");
        keyStore.load(new FileInputStream(KEYSTORE_PATH), KEYSTORE_PASSWORD.toCharArray());
        pingRequest();
    }


    private static void pingRequest() throws GeneralSecurityException, IOException, InterruptedException {

        var body = "";
        var currentDate = getCurrentGMTDateAsString(HEADER_DATE_FORMAT);
        var digest = calculateDigest(body);
        var messageToSign = buildMessage(digest, currentDate, "/ping", "get");
        var signedMessage = calculateSignature(messageToSign);
        var signatureHeader = buildSignatureHeader(signedMessage, CLIENT_ID);

        var partnerPingRequest = HttpRequest.newBuilder()
                .uri(URI.create(PARTNER_API_BASE_URL + "/ping"))
                .header("Authorization", "Bearer " + getToken())// OAuth2 authentication
                .header("Subscription-Key", SUBSCRIPTION_KEY)// Add Subscription-Key header
                .header("Date", currentDate)// Add date header
                .header("Digest", digest)// Add digest header
                .header("Signature", signatureHeader) // add signature header
                .build();

        var httpClient = HttpClient.newBuilder().sslContext(getSSLContext()).build();
        var response = httpClient.send(partnerPingRequest, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());

    }

    private static SSLContext getSSLContext() throws UnrecoverableKeyException, CertificateException, NoSuchAlgorithmException, KeyStoreException, IOException, KeyManagementException {
        return SSLContexts.custom().loadKeyMaterial(keyStore, KEYSTORE_PASSWORD.toCharArray()).build();
    }

    private static String getToken() throws IOException, InterruptedException, UnrecoverableKeyException, CertificateException, NoSuchAlgorithmException, KeyStoreException, KeyManagementException {
        Map<String, String> parameters = new HashMap<>();
        parameters.put("client_id", CLIENT_ID);
        parameters.put("client_secret", CLIENT_SECRET);
        var body = parameters.keySet().stream()
                .map(key -> key + "=" + URLEncoder.encode(parameters.get(key), StandardCharsets.UTF_8))
                .collect(Collectors.joining("&"));
        var httpClient = HttpClient.newBuilder().sslContext(getSSLContext()).build();
        var currentDate = getCurrentGMTDateAsString(HEADER_DATE_FORMAT);
        var digest = calculateDigest(body);
        var messageToSign = buildMessage(digest, currentDate, "/oauth2/token", "post");
        var signedMessage = calculateSignature(messageToSign);
        var signatureHeader = buildSignatureHeader(signedMessage, CLIENT_ID);

        var request = HttpRequest.newBuilder().uri(URI.create(TOKEN_PATH))
                .header("Content-Type", "application/x-www-form-urlencoded")
                .header("Subscription-Key", SUBSCRIPTION_KEY)// Add Subscription-Key header
                .header("Date", currentDate)// Add date header
                .header("Digest", digest)// Add digest header
                .header("Authorization", "Signature " + signatureHeader) // add signature header
                .POST(HttpRequest.BodyPublishers.ofString(body)).build();

        var response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
        var token = new ObjectMapper().readValue(response.body(), Token.class);
        return token.getAccessToken();
    }

    private static String calculateSignature(String messageToSign) {
        try {
            var dsa = Signature.getInstance("SHA256withRSA");
            dsa.initSign(getMessageSigningPrivateKey());
            dsa.update(messageToSign.getBytes(StandardCharsets.UTF_8));
            return base64Encode(dsa.sign());
        } catch (Exception e) {
        }
        return "";
    }

    private static String buildMessage(String digest, String date, String uri, String httpMethod) {
        try {
            var message = new StringBuilder()
                    .append("(request-target): ").append(httpMethod.toLowerCase()).append(" ").append(uri)
                    .append("\ndate: ").append(date)
                    .append("\ndigest: ").append(digest)
                    .toString();
            return message;
        } catch (Exception e) {
            return "";
        }
    }

    private static String base64Encode(byte[] value) {
        return Base64.getEncoder().encodeToString(value);
    }

    private static String calculateDigest(String payload) {
        try {
            MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
            byte[] digest = messageDigest.digest(payload.getBytes(StandardCharsets.UTF_8));
            return "SHA-256=" + base64Encode(digest);
        } catch (Exception e) {
            return "";
        }
    }

    private static String buildSignatureHeader(String signedMessage, String clientId) {
        return new StringBuilder()
                .append("keyId=\"").append(clientId).append("\"")
                .append(",algorithm=\"rsa-sha256\"")
                .append(",headers=\"(request-target) date digest\",signature=\"")
                .append(signedMessage).append("\"")
                .toString();
    }

    private static String getCurrentGMTDateAsString(DateTimeFormatter formatter) {
        ZonedDateTime gmtDateTime = ZonedDateTime.now().withZoneSameInstant(ZoneOffset.UTC);
        return gmtDateTime.format(formatter);
    }

    private static PrivateKey getMessageSigningPrivateKey() throws UnrecoverableKeyException, KeyStoreException, NoSuchAlgorithmException {
        return (PrivateKey) keyStore.getKey("message-signing", KEYSTORE_PASSWORD.toCharArray());
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

# Postman script for signing request
Bear in mind 3 things Ffor this code to work :
1. You need to install the `pmlib` for this refer this link https://joolfe.github.io/postman-util-lib/
basically you just need to import this collection https://github.com/joolfe/postman-util-lib/blob/master/postman/PostmanUtilityLibv21.postman_collection.json, 
and run the method install.
2. You need to add the env variables private_key - for filling the variable you can open the private key(any text editor) of your message signing certificate and copy and paste the content.
3. And fill out apim_client_id env variable with your client id.

```
eval( pm.globals.get('pmlib_code') );
var CryptoJS = require("crypto-js");
var moment = require("moment");

const privateKey = pm.environment.get("private_key")
const clientId = pm.environment.get("apim_client_id")

signRequest(pm.request, privateKey);

function signRequest(request, privateKey) {
    const method = request.method.toLowerCase();
    const path = request.url.getPathWithQuery().replace("/partner-api","");
    const bodyPostman = JSON.stringify((request.body || ""));
    var body = JSON.parse(bodyPostman).raw;
    const date = moment.utc().format("ddd, DD MMM yyyy HH:mm:ss Z");
    const bodyEncoded = CryptoJS.enc.Base64.stringify(CryptoJS.SHA256(body));
    const digest = `SHA-256=${bodyEncoded}`;
    const message = `(request-target): ${method} ${path}\ndate: ${date}\ndigest: ${digest}`;
    var sig = new pmlib.rs.KJUR.crypto.Signature({"alg": "SHA256withRSA"});
    sig.init(privateKey);
    var hash = sig.signString(message);
    const signedEncoded = CryptoJS.enc.Base64.stringify(CryptoJS.enc.Hex.parse(hash));
    var signatureHeader = `keyId=\"${clientId}\",algorithm=\"rsa-sha256\",headers=\"(request-target) date digest\",signature=\"${signedEncoded}\"`;

   if(path.includes("oauth2/token")){
        var authSignHeader = `Signature ${signatureHeader}`
        pm.request.headers.add({
            key: 'Authorization',
            value: authSignHeader
        });
    }else {
        pm.request.headers.add({
            key: 'Signature',
            value: signatureHeader
        });
    }

    pm.request.headers.add({
        key: 'date',
        value: date
    });

    pm.request.headers.add({
        key: 'digest',
        value: digest
    });
}

```


# Curl example for calling the API

## Token Request
```
#!bin/bash

###############################################################################
#                             REQUEST APPLICATIION ACCESS TOKEN               #
###############################################################################
# This script requests application access token for the Partner API           #
###############################################################################


keyId="CLIENT_ID" # client_id as provided 
subscriptionKey="SUBSCRIPTION_KEY" # Subscrption key
certPath="certPath/" # path of the certificates and keys

httpHost="https://api.sandbox.lendico.de/partner-api"

# httpMethod value must be in lower case
httpMethod="post"
reqPath="/oauth2/token"

# CALCULATE DIGEST
# You can also provide scope parameter in the body E.g. "grant_type=client_credentials&scope=greetings%3Aview" 
# scope is an optional parameter. The downloaded certificate contains all available scopes. If you don't provide a scope, the accessToken is returned for all scopes available in certificate
payload="client_id=CLIENT_ID&client_secret=SECRET_ID"
payloadDigest=`echo -n "$payload" | openssl dgst -binary -sha256 | openssl base64`
digest=SHA-256=$payloadDigest

# CALCULATE DATE
reqDate=$(LC_TIME=en_US.UTF-8 date -u "+%a, %d %b %Y %H:%M:%S GMT")


# signingString must be declared exactly as shown below in separate lines
signingString="(request-target): $httpMethod $reqPath
date: $reqDate
digest: $digest"

signature=`printf %s "$signingString" | openssl dgst -sha256 -sign "${certPath}message-signing.key" -passin "pass:changeit" | openssl base64 -A`

# Curl request method must be in uppercase e.g "POST", "GET"
 curl -i -X POST "${httpHost}${reqPath}" \
-H 'Accept: application/json' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-H "Subscription-Key: ${subscriptionKey}" \
-H "Digest: ${digest}" \
-H "Date: ${reqDate}" \
-H "authorization: Signature keyId=\"$keyId\",algorithm=\"rsa-sha256\",headers=\"(request-target) date digest\",signature=\"$signature\"" \
-d "${payload}" \
--cert "${certPath}client-tls.crt" \
--key "${certPath}client-tls.key"

```

## Ping Request 

```
#!/bin/bash

###############################################################################
# # You must request an application access token to run this script.          #
# Please update the variables "keyId", ""accessToken", "certPath".			  #
###############################################################################

keyId="2e91ff74-65c9-4590-a355-399998878d4f" # keyId is the Client ID we sent to you
subscriptionKey="171bda0be8914b5fb99ad7faa14a860d" # Subscrption key

certPath="/Users/claudioaugustodepauloresende/Documents/lendico/dev/certs/script/apim/" # path of the two pair of self-signed X.509 certificates and keys that you generated  and sent to us the pfx file

httpHost="https://api.sandbox.lendico.de/partner-api"

# httpMethod must be in lower code
httpMethod="get"
reqPath="/ping"

# Since the message body (payload) is empty, the digest is defaulted to the hash256 of an empty body.
digest="SHA-256=47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU="

# Generated value of application access token. Please note that the access token expires 
accessToken="eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwia2lkIjoicHJkLThlZTk0NTIzLTAxNjMtNDMyZi1hYjgwLWFhNjU4MTFhNDBmYiIsImN0eSI6IkpXVCJ9..H8kRkDjRnGi6HIQqw2HRTA.kBvpRHNJzZnMVHmm_N275GGwfQ8ju1optAj_FeZmsXOL2pRbvaQHhC09nmr75cG3FifNST_CfeNhLqf-0maO5p2_AEZzlPIeSW6crK4xnMLz2BWuF83AoSNcepvbHgOj4tOileHoLR5n4ZQNjAT7F-SiwOHMfMgTzpJiqAsDvDWK3gWJfmFezW1kmsTXc5q0v8oxo9o2V25gRripyMpNu3Ow6uN5AOH_vA3IP5yTGpNhvxL720t23cCdnDGQogcjth0v-ntd7o66ibqp66PLEqXYxLW-f05C2OYsKrKp9jIZ3VvHoPUXZojMxmt1AUBmzifIiCxD-ENDjugDjh9zEewDBSHeyrTuzapBzJSABKxA67JVNXzuSVbFykywL8jD1Begz9Zs_GN4UdpHojbGrmB-Zyoq-HAhopQNTi-6LQTDEZj2UYt2NJjpHVQX63ye2jVOgE1zV1Jul8YHi7qFWp9I3uTuhj09BxIPWCFWQSIPj8dRW23SUap4xtib46QIZlsKEYa4zR9xIHDbHKZdQcwCf8LxFKYxCjEuwXotOQgzGtB3CqA2J_F5eMmKC9_SV8UkflQb4a5AKrW8Dm9qpdN93ErKhjRIUEx24AsSDEK9-MZUI9gUsSUAoFY05XVidZ7tuNpKandxf1NjsixLxljODUav1poKqboPGW4OaxwFYLZXY0jEtFx4Ib8Qpwg26b2rugPkFQgkDne7RWz94ESnLTSBoz8-8_wp0yVMTQsZgNcbsEs2OIueaaEGHLEmVacc_L-kT8P2NjXUfuzNJbaoSxnVcni2WeP8NAbLFIMRKPtEKQUc8nopl7c0Sh50K8quPprfuVLdZOMa19TshIBlDTP98sc6AgaQ56k_Pbuj60fbk-c7zY7zxHApHY2Uo2cJD5dl-W2ucUgwuFV86DhMrX9q_eTesVrWQ7pXvrobC2_7t6O_IBsKu4yJQzi_VBRfmf3nMStEe-Xz0L_aefMOtnX5Z9WQoOVcNMpDr-Ttpm9LSefurZUD-xprvz1idpyDbv_YMfzMz1Kv481EUToqKwXDgUdAOtOoD50XLbLEdAHxqlfymMUYv3MPH-iVBoDirhOQxJFrWOt1t5_PQCz73Xwffv05796fQ3wAnndKB2jm4_J-9i5UpiWTTId7sLHdArmXFRxibuga_326DNZlyk913fe6gXKbXboYRb7SuCasFcZ3WFK5NoqodzaLZXLBTZhCRg3cQciOWHzIji-mFXHPabqcKacpU_pM7X3c96424AObvfuLCEvAYmHz06OmKKK9ACV9CUPXxue3gCtbGBmOYV6erWUcvt06CXOlxwgiLVkgXfuscz7Fn1FwamU-qYfimYtRXPt_qzFi3I9qBqzbKxPvOHm8mFWHhoBK1DFKEzL0q9sYAYqkzOHlS9dGB9wJ5l4AQIIwhHPK47Wjf7DN1E01rG5ZYp45H7DMs6fXwtO_4KyLI2NkI9YWNdbXr84h3zcU4pqfClhQyBGlyF4nh7Y08wG3NZ3DD3mzQdT9ryYffCZEFcDpVcfhraLpk2qXlYRRDh6vky2ESjKZEGmBcRe3c_3Fx66eGNBk5WIGOzud6BkrYX_kyHTOYsh4A4xZpF794LZo9yPkekAGEkt2SkEkpJAQol8WWnHo2DK98TGX4OndtdQKttPFlsVYbCktCQYGbH3Zx9AEYhdwO-xl1kj0mjfrpi8Q16CslDl1u8rHeQIdLlTN2R274EKXG_KWAAa3R4WX8ZQgNnmgdeUL7AwUSd9JG8v6VNS6BY0HXbD4iT_mq__VHa1D0d49JdayP-cjiuCRgJMlBvz7WOKrNbrFMaVfrPRDMtFzDq75TzHtGuuHXqt9KNhrKkhJFf_Y_0xx2L6eBF_EO40ETC3Nu1lstPQpnGQxGiZeBtHpX5dRaYQ0mFrzvZ4TJz0Ss0hKRM2H6TqsTrNSMS_q2uf1fRW48P5ElKBTfnHSBALh4iwV3Zwq8-Zih48kj1TGsYKcxJmsiGOV1vI9Z3xEsiLKcGeS0HMw3ToVHCeLvG1X1aCqbrbXxURR9x15ZXPy_kJDPHDNqDieF7j8sHchxpSbwCGHvtXSPs80v-qQC9jyxFcB_mXO4MtwxxcMcSlkPOrZxpUk1U0d0GkfZzss5xKTMEFCHdcpHFTU5_MZoCmTv809jpnz_AOnWB4N1eA0iwXC3ZFIfT_GjKOPpyjYiCjr63oaUiahJHl-oW4EQHh2V-ZgFkN2A9oe7DmcruFWHWca6etKrSv5xOKTPfJycCH318EckzrclUJviRG7fkY5izcjeiktW0xFPvEC0U0o5Rs39bbeOd0NmrConMcbDHhU2CpQrxs4K2elNzQVcO7dBLuvJmzVXw4XKGtmMTvVYsORoYauOR5b4krhYENI0MzoO3dY_PvSayIGwww4PNXYl4Ail9iELTbA06uS.vxbf2aRnZfa9fLwA7TOwpqzJIcPK8T10X95bPyEns3I"

reqDate=$(LC_TIME=en_US.UTF-8 date -u "+%a, %d %b %Y %H:%M:%S GMT")

# signingString must be declared exactly as shown below in separate lines
signingString="(request-target): $httpMethod $reqPath
date: $reqDate
digest: $digest"

# signingString must be declared exactly as shown below in separate lines
signature=`printf %s "$signingString" | openssl dgst -sha256 -sign "${certPath}message-signing.key" | openssl base64 -A`

# Curl request method must be in uppercase e.g "POST", "GET"
curl -i -X GET "${httpHost}${reqPath}" \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H "Subscription-Key: ${subscriptionKey}" \
-H "Date: ${reqDate}" \
-H "Authorization: Bearer ${accessToken}" \
-H "Signature: keyId=\"$keyId\",algorithm=\"rsa-sha256\",headers=\"(request-target) date digest\",signature=\"$signature\"" \
--cert "${certPath}client-tls.crt" \
--key "${certPath}client-tls.key"
```
# Generating Self-Signed certificate with openssl


#1. Generate a new RSA private key. 
#The password of the private key can be entered during execution of the command(due arg stdin), avoiding it to be listed in the shell history.

```
openssl genrsa -out server.key -passout stdin 2048
```

#2. Generate the X.509 Certificate Signing Request

```
openssl req -sha256 -new -key server.key -out server.csr
```

#3. Sign the X.509 certificate with your own private key

```
openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server_public.crt
```

#4. Pack the key and the crt file into a pfx file to send to us.

```
openssl pkcs12 -export -out server.pfx -inkey server.key -in server_public.crt
```

NOTE: In the step 4 will ask you for a password for packing the files into PFX file, in case you use a password here you will need to send us the password.

# In case using java as in the tutorial example, you can generate a pkcs12 file for the key store as below

```
openssl pkcs12 -export -in server_public.crt -inkey server.key -out server-cert.p12
```

# Generating pfx file from key and certificate
For generating pfx file from key and certificate you can follow the following steps using `openssl`

```
openssl pkcs12 -export -out domain.name.pfx -inkey domain.name.key -in domain.name.crt
```

If you have a root CA and intermediate certs, then include them as well using multiple -in params

```
openssl pkcs12 -export -out domain.name.pfx -inkey domain.name.key -in domain.name.crt -in intermediate.crt -in rootca.crt
```

Example:

```
openssl pkcs12 -export -out client-certificate.pfx -inkey client-key.key -in client-cert.crt
```
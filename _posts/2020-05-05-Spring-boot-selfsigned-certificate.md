---
title: Setup Self Signed certificate for Spring boot application
---

# Spring Boot - HTTPs setup - Self Signed

## Tools required
1. openssl

## Setup Local Certificate Authority
#### Step 1. Create Private key for Local Certificate Authority <br> 
```openssl genrsa -des3 -out localCA.key 2048```
above command ask for password, keep it safe, it will be required in next steps

#### Step 2: Create Local Auth
```openssl req -x509 -new -nodes -key localCA.key -sha256 -days 365 -out localCA.pem``` <br>

## Create Certs using the Certificate Authority created in previous steps
#### Step 1. Generate private key
```openssl genrsa -out myweb.key 2048```

#### Step 2. Generate certificate signing request
```openssl req -new -key myweb.key -out myweb.csr```<br>
note: when asked for Common Name use your domain name, in my case its localhost

#### Step 3. Create a config file
create config file localhost.ext with following information <br>
```bash
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = localhost
```
<br>

One line command<br>
``` hostname=localhost ``` 
<br>

```bash
printf "authorityKeyIdentifier=keyid,issuer\nbasicConstraints=CA:FALSE\nkeyUsage
= digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment\nsubjectAltName = @alt_names\n\n[alt_names]\nDNS.1
 = $hostname" > $hostname.ext
 ```

### Step 4: Generate Certificate for signing
``` openssl x509 -req -in myweb.csr -CA localCA.pem -CAkey localCA.key -CAcreateserial -out localhost.crt -days 360 -sha256 -extfile localhost.ext```

#### Step 5: Create PKCS#12 bundle to be used in springboot application
```openssl pkcs12 -inkey myweb.key -in localhost.crt -export -out localhost.p12```


## Testing 
#### Java Program
```java
		CertificateFactory cf = CertificateFactory.getInstance("X.509");
		InputStream caInput = new BufferedInputStream(new FileInputStream("\\path\\to\\localCA.pem"));
		Certificate ca;
		try {
		    ca = cf.generateCertificate(caInput);
		    System.out.println("ca=" + ((X509Certificate) ca).getSubjectDN());
		} finally {
		    caInput.close();
		}

		String keyStoreType = KeyStore.getDefaultType();
		KeyStore keyStore = KeyStore.getInstance(keyStoreType);
		keyStore.load(null, null);
		
		keyStore.setCertificateEntry("ca", ca);

		String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
		TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
		tmf.init(keyStore);

		SSLContext context = SSLContext.getInstance("TLS");
		context.init(null, tmf.getTrustManagers(), null);
		
		URL url = new URL("https://fullurl");
		HttpsURLConnection  urlConnection = (HttpsURLConnection ) url.openConnection();
		urlConnection.setSSLSocketFactory(context.getSocketFactory());
		InputStream in = urlConnection.getInputStream();
		BufferedReader reader = new  BufferedReader(new InputStreamReader(in));
		String str;
		while((str=reader.readLine())!=null) {
			System.out.println(str);
		}
```


## Without Root CA
```openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem``` <br>
```openssl pkcs12 -inkey key.pem -in certificate.pem -export -out certificate.p12```

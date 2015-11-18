Product: Integration tests for WSO2 ESB Shopify connector

Pre-requisites:

 - Maven 3.x
 - Java 1.6 or above
 - The org.wso2.esb.integration.integration-base project is required. The test suite has been configured to download this project automatically. If the automatic download fails, download the following project and compile it using the mvn clean install command to update your local repository:
   https://github.com/wso2/esb-connectors/tree/master/integration-base-1.0.1

Tested Platform: 

 - Microsoft WINDOWS V-7
 - UBUNTU 13.04
 - WSO2 ESB 4.9.0
 - Java 1.7

Steps to follow in setting integration test.

 1. Download ESB 4.9.0 by navigating to the following URL: http://wso2.com/products/enterprise-service-bus/#
 
 2. Deploy relevant patches, if applicable. Place the patch files into location <ESB_HOME>/repository/components/patches. 
 
 3. Please make sure that the below mentioned Axis configurations are enabled (\repository\conf\axis2\axis2.xml) in ESB.
 
   <messageFormatter contentType="text/html" 
            class="org.wso2.carbon.relay.ExpandingMessageFormatter"/> 
   <messageBuilder contentType="text/html" 
            class="org.wso2.carbon.relay.BinaryRelayBuilder"/>

   <messageFormatter contentType=”application/json”
                       class=”org.apache.synapse.commons.json.JsonStreamFormatter”/>
   <messageBuilder contentType=”application/json”
                     class=”org.apache.synapse.commons.json.JsonStreamBuilder”/>

   <messageFormatter contentType="application/xml"
                       class="org.apache.axis2.transport.http.ApplicationXMLFormatter"/>
   <messageBuilder contentType="application/xml"
                     class="org.apache.axis2.builder.ApplicationXMLBuilder"/>

 4. Create a Shopify account using URL https://app.shopify.com/services/partners/signup by giving required values for relevant fields in the "Shopify Partners" page.
   
   Note : This is a full featured 14-day free trial account.
   
 5. Follow the bellow mentioned steps to generate the access token:
      
   i)  Create an application on Shopify using your account by navigating to https://app.shopify.com/services/partners/api_clients and you will need to give an application name and callback URL for token requests. 
   ii) After successfully creating an application, obtain the API Key(client_id), Credential sets(client_secret) and Callback URL(redirect_uri).
   iii)Create a developer shop using "development shop" section in left menu panel by giving name(shop_name) for the shop.
   iv) Follow the steps in http://docs.shopify.com/api/authentication/oauth#get-the-client-redentials and obtain the API access token that can be used to access the shop’s data as long as the client is installed.
      
      NOTE : At the point of development, we have observed that the request made in Step3 of this document, should send client_id, client_secret and code parameters via form-data, requests send in json format was unsuccessful.
 
 6. Follow the below mentioned steps for adding valid certificate to access Shopify API over https.

   i)  Extract the certificate from browser(Mozilla Firefox) by navigating to https://{shop_name}.myshopify.com
   ii) Go to new ESB 4.9.0 folder and place the downloaded certificate in both "<ESB_HOME>/repository/resources/security/" and "{SHOPIFY_CONNECTOR_HOME}/shopify-connector/shopify-connector-1.0.0/org.wso2.carbon.connector/src/test/resources/keystores/products/" folders.
   iii)Navigate to "<ESB_HOME>/repository/resources/security/" using command prompt and execute keytool -importcert -file CERT_FILE_NAME -keystore client-truststore.jks -alias "CERT_NAME" in command line to import Shopify certificate in to keystore. 
       Give "wso2carbon" as password. Press "Y" to complete certificate import process.
       
      NOTE : CERT_FILE_NAME is the file name which was extracted from Shopify. (e.g. *.myshopify.com)
             CERT_NAME is an arbitrary name for the certificate. (e.g. Shopify)
            
   iv) Navigate to "{SHOPIFY_CONNECTOR_HOME}/shopify-connector/shopify-connector-1.0.0/org.wso2.carbon.connector/src/test/resources/keystores/products/" using command prompt and execute keytool -importcert -file CERT_FILE_NAME -keystore wso2carbon.jks -alias "CERT_NAME" in command line to import Shopify certificate in to keystore.
      Give "wso2carbon" as password.
      
      NOTE : CERT_FILE_NAME is the file name which was extracted from Shopify, change it accordingly. (e.g. *.myshopify.com)
            CERT_NAME is an arbitrary name for the certificate. (e.g. Shopify)

 7. Compress modified ESB as wso2esb-4.9.0.zip and copy that zip file in to location "{ESB_CONNECTOR_HOME}/repository/".
 
 8. Make sure that Shopify is specified as a module in ESB Connector Parent pom.

    <module>shopify/shopify-connector/shopify-connector-1.0.0/org.wso2.carbon.connector</module>
 
 9. Update the Shopify properties file at location "{SHOPIFY_CONNECTOR_HOME}/shopify-connector/shopify-connector-1.0.0/org.wso2.carbon.connector/src/test/resources/artifacts/ESB/connector/config" as below.
   
   i)    apiUrl               - Use the API URL as "https://{shop_name}.myshopify.com".
   ii)   accessToken          - Access Token obtained by following the steps in 4.
   iii)  customerFirstName    - Use a valid string value as the first name of the customer.
   iv)   customerEmail1       - Use a valid email address. 
   v)    customerEmail2       - Use a valid email address.
   vi)   productTitle         - Use a valid string as the name of the product.
   vii)  productType          - Use a valid string as the type of the product.
   viii) vendor               - Use a valid string as the name of the vendor of the product.
   ix)   tags                 - Use a valid string of comma separated string categorizations that a product can be tagged with.
   x)    variantOpt1          - Use a valid string as the title of the product variant.
   xi)   variantOpt2          - Use a valid string as the title of the product variant.
   xii)  trackingNumber       - Use a valid string as the tracking number.
   xiii) updateOrderTag       - Use a valid string as the order tag.
   xiv)  updateOrderNote      - Use a valid string as the order note.
   xv)   updateProductTitle   - Use a valid string as the product title.
   xvi)  updateProductType    - Use a valid string as the product type.
   
      NOTE : customerEmail1 and customerEmail2 should be an email which is not associated with an existing customer in your Shopify account. Also change this email before running the integration test each time. 

 10. Navigate to "<ESB_CONNECTOR_HOME>/" and run the following command.
 
    $ mvn clean install

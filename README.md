# `aio-aem-events-sample` project 

This sample project acts as a guide 
to help you get started with [`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events)

### Start with AEM project Archetype

This project first was generated using [AEM Project Archetype](https://experienceleague.adobe.com/docs/experience-manager-core-components/using/developing/archetype/overview.html?lang=en
), with the following command line :

    mvn -B archetype:generate \    
    -D archetypeGroupId=com.adobe.aem \
    -D archetypeArtifactId=aem-project-archetype \
    -D archetypeVersion=36 \
    -D appTitle="My Site" \
    -D appId="mysite" \
    -D groupId="com.mysite"

To keep the sample easy to manage and understand, we removed the unnecessary modules
* removed all modules but `all` and `ui.apps.structure`
* removed these modules from the main parent/root `pom.xml` file
* removed the reference to the deleted packages from the main `all/pom.xml` file in the `filevault-package-maven-plugin` `embedded` `configuration` section
But obviously, the above removals are optional, you may keep these modules, if you need them. 

### Add `aio.config`: a `container` package holding `aio-aem-events` osgi workspace configuration

Next, we need to add [`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events) 
[`https://github.com/adobe/aio-lib-java/blob/main/aem/core_aem/src/main/java/com/adobe/aio/aem/workspace/ocd/WorkspaceConfig.java`](Workspace OSGI configuration),
for this we added a new `container` package : `aio.config` 

Here in this sample we used a [configuration](aio.config/src/main/content/jcr_root/apps/mysite/osgiconfig/config/com.adobe.aio.aem.workspace.internal.WorkspaceSupplierImpl.cfg.json) 
leveraging only system environment variables,
however if your target AEM as a Cloud Service, we strongly advise use to leverage AEM Cloud Manager `secret` environment variables
(see [Configuring OSGi for Adobe Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/deploying/configuring-osgi.html%3Flang%3Den#secret-configuration-values)).
You should at least use secret configuration to store `aio.client.secret` and `aio.encoded.pkcs8`.
Your OSGI configuration file would then look like this:

    {
    "aio.consumer.org.id": "$[env:aio_consumer_org_id]",
    "aio.ims.org.id": "$[env:aio_ims_org_id]",
    "aio.meta.scopes": "$[env:aio_meta_scopes]",
    "aio.project.id": "$[env:projectid]",
    "aio.workspace.id": "$[env:aio_workspace_id]",
    "aio.api.key": "$[env:aio_api_key]",
    "aio.credential.id": "$[env:aio_credential_id]",
    "aio.technical.account.id": "$[env:aio_technical_account_id]",
    "aio.client.secret": "$[secret:aio_client_secret]",
    "aio.encoded.pkcs8": "$[secret:aio_encoded_pkcs8]"
    }

### Add `aio-aem-events` and `aio.config` as an Embedded/Sub packages

Next we edited `all/pom.xml` file, and did:
* set `aio-aem-events.version` in the `properties` section to use the latest [version from maven central](https://repo1.maven.org/maven2/com/adobe/aio/aem/aio-aem-events)
* add `aio-aem-events` and `aio.config` in the `dependencies` section

      
      <dependency>
         <groupId>com.mysite</groupId>
         <artifactId>mysite.aio.config</artifactId>
         <version>${project.version}</version>
         <type>zip</type>
      </dependency>
      <dependency>
          <groupId>com.adobe.aio.aem</groupId>
          <artifactId>aio-aem-events</artifactId>
          <version>${aio-aem-events.version}</version>
          <type>zip</type>
      </dependency>

* add `aio-aem-events` and `aio.config` as an Embedded/Sub packages, in the `filevault-package-maven-plugin` `embedded` `configuration` section:


      <embedded>
           <groupId>com.mysite</groupId>
           <artifactId>mysite.aio.config</artifactId>
           <type>zip</type>
           <target>/apps/mysite-packages/application/install</target>
      </embedded>
      <embedded>
           <groupId>com.adobe.aio.aem</groupId>
           <artifactId>aio-aem-events</artifactId>
           <type>zip</type>
           <target>/apps/mysite-packages/application/install</target>
      </embedded>
 


## How to build

To build all the modules run in the project root directory the following command with Maven 3:

    mvn clean install

To build all the modules and deploy the `all` package to a local instance of AEM, run in the project root directory the following command:

    mvn clean install -PautoInstallSinglePackage

Or to deploy it to a publish instance, run

    mvn clean install -PautoInstallSinglePackagePublish

Or alternatively

    mvn clean install -PautoInstallSinglePackage -Daem.port=4503


## Maven settings

The project comes with the auto-public repository configured. To setup the repository in your Maven settings, refer to:

    http://helpx.adobe.com/experience-manager/kb/SetUpTheAdobeMavenRepository.html

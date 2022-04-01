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

Note the above removals are optional, you may keep these modules, if you need them. 

### Add `aio.config`: a `container` package holding `aio-aem-events` `Workspace` configuration

[`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events) 
expects a [`Workspace Configuration`](https://github.com/adobe/aio-lib-java/blob/main/aem/core_aem/src/main/java/com/adobe/aio/aem/workspace/ocd/WorkspaceConfig.java)
defined as an OSGI configuration; we added a new `container` package : `aio.config` to hold this configuration.

Our sample [`Workspace` OSGI configuration](aio.config/src/main/content/jcr_root/apps/mysite/osgiconfig/config/com.adobe.aio.aem.workspace.internal.WorkspaceSupplierImpl.cfg.json) 
is leveraging system environment variables only, however if your target AEM as a Cloud Service, 
we strongly advise use to leverage AEM Cloud Manager `secret` environment variables
(see [Configuring OSGi for Adobe Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/deploying/configuring-osgi.html%3Flang%3Den#secret-configuration-values)).
to store `aio.client.secret` and `aio.encoded.pkcs8`.

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

For `aio-aem-events` to be enabled for our `mysite` sample, the only remaining build configuration is to
Add `aio-aem-events` and `aio.config` as an Embedded/Sub packages.

For this we edited `all/pom.xml` file, and did:
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
 
## Environment Variables

As detailed above, the sample `aio.config` [`Workspace` OSGI configuration](aio.config/src/main/content/jcr_root/apps/mysite/osgiconfig/config/com.adobe.aio.aem.workspace.internal.WorkspaceSupplierImpl.cfg.json)
is leveraging environment variables, you will need to define these in your system.

### Local AEM configuration

For local configuration, you may use the `export` bash command to load the system environment variables, for
your aem child process to inherit.

    export aio_project_id="<your Adobe Developer Console project id (project.id)>"
    export aio_consumer_org_id="<your Adobe Developer Console consumer orgnaization id (project.org.id)>"
    export aio_ims_org_id="<your Adobe Developer Console IMS Organization ID (project.org.ims_org_id)>"
    export aio_workspace_id="<your Adobe Developer Console workspace Id (project.workspace.id)>"
    export aio_credential_id="<your Adobe Developer Console jwt credential id (project.workspace.details.credentials[i].id)>"
    export aio_client_secret="<your Adobe Developer Console jwt credential client secret (project.workspace.details.credentials[i].jwt.client_secret)>"
    export aio_api_key="<your Adobe Developer Console jwt credential API Key (or Client ID) (project.workspace.details.credentials[i].jwt.client_id>"
    export aio_meta_scopes="<your Adobe Developer Console jwt credential metascopes (project.workspace.details.credentials[i].jwt.meta_scopes)>"
    export aio_technical_account_id="<your Adobe Developer Console jwt credential technical account id (project.workspace.details.credentials[i].jwt.technical_account_id)>"
    export aio_encoded_pkcs8="<your base64 encoded PKCS8 private key associated with the public key you added in your Adobe Developer Console>
    
### AEM as a Cloud Service configuration

For more details read the [Configuring OSGi for Adobe Experience Manager as a Cloud Service guide](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/deploying/configuring-osgi.html%3Flang%3Den#secret-configuration-values)).


## How to build and deploy

To build all the modules run in the project root directory the following command with Maven 3:

    mvn clean install

To build all the modules and deploy the `all` package to a local instance of AEM, run in the project root directory the following command:

    mvn clean install -PautoInstallSinglePackage

Or to deploy it to a publish instance, run

    mvn clean install -PautoInstallSinglePackagePublish

Or alternatively

    mvn clean install -PautoInstallSinglePackage -Daem.port=4503

To deploy it on your AEM as a Cloud Service, confer [AEM Cloud Manager deployment guide](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/deploy-code.html?lang=en)

## Test Drive



## Maven settings

The project comes with the auto-public repository configured. To setup the repository in your Maven settings, refer to:

    http://helpx.adobe.com/experience-manager/kb/SetUpTheAdobeMavenRepository.html

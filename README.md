# `aio-aem-events-sample` project 

This sample (working) project acts as a guide 
to help you get started with [`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events)

Note that to run AEMaaCS locally you can download the sdk at
https://experience.adobe.com/#/downloads/content/software-distribution/en/aemcloud.html?fulltext=aem*+sdk*

## Making-of

Here are the few steps to redo it from scratch:

### Start with AEM project Archetype

Generate your project using [AEM Project Archetype](https://experienceleague.adobe.com/docs/experience-manager-core-components/using/developing/archetype/overview.html?lang=en
), with the following command line :

    mvn -B archetype:generate \    
    -D archetypeGroupId=com.adobe.aem \
    -D archetypeArtifactId=aem-project-archetype \
    -D archetypeVersion=36 \
    -D appTitle="My Site" \
    -D appId="mysite" \
    -D groupId="com.mysite"

Optionally (we did it to keep the sample easy to manage and understand) remove the unnecessary modules
* remove all modules but `all` and `ui.apps.structure`
* remove their reference from the root `pom.xml` and `all/pom.xml` files

### Add a new module holding `aio-aem-events` Workspace configuration : `aio.config`

[`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events) 
expects a [workspace OSGI configuration](https://github.com/adobe/aio-lib-java/tree/main/aem/core_aem#expected-workspace-osgi-configuration)
defined: for this add a new `container` package named `aio.config` to hold this configuration.

If you target AEM as a Cloud Service, we strongly encourage the use of AEM Cloud Manager `secret` and `env` environment variables
(see [Configuring OSGi for Adobe Experience Manager as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/deploying/configuring-osgi.html%3Flang%3Den#secret-configuration-values)).

Your OSGI configuration could look like this:

    {
        "aio.consumer.org.id": "$[env:aio_consumer_org_id]",
        "aio.ims.org.id": "$[env:aio_ims_org_id]",
        "aio.meta.scopes": "$[env:aio_meta_scopes]",
        "aio.project.id": "$[env:aio_project_id]",
        "aio.workspace.id": "$[env:aio_workspace_id]",
        "aio.api.key": "$[env:aio_api_key]",
        "aio.credential.id": "$[env:aio_credential_id]",
        "aio.technical.account.id": "$[env:aio_technical_account_id]",
        "aio.client.secret": "$[env:aio_client_secret]",
        "aio.encoded.pkcs8": "$[env:aio_encoded_pkcs8]"
    }

Confer our [sample Workspace OSGI configuration](aio.config/src/main/content/jcr_root/apps/mysite/osgiconfig/config/com.adobe.aio.aem.workspace.internal.WorkspaceSupplierImpl.cfg.json)

### Add `aio-aem-events` and `aio.config` as embedded packages

Now we have the configuration ready, the last step is to
add `aio-aem-events` and `aio.config` as an Embedded/Sub packages.

For this edit `all/pom.xml` file, and
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
 
## Set your Environment Variables

As detailed above, this sample is leveraging environment variables, you will need to define these in your system.
* for local configuration, you may use the `export` bash command to load the system environment variables, for
your aem child process to inherit.
* for AEM as a Cloud Service configuration read the [Cloud Manager environment variables guide](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/environment-variables.html?lang=en#add-variables).

## Build and deploy

To build all the modules run in the project root directory the following command with Maven 3:

    mvn clean install

To build all the modules and deploy the `all` package to a local instance of AEM, run in the project root directory the following command:

    mvn clean install -PautoInstallSinglePackage

Or to deploy it to a publish instance, run

    mvn clean install -PautoInstallSinglePackagePublish

Or alternatively

    mvn clean install -PautoInstallSinglePackage -Daem.port=4503

To deploy it on your AEM as a Cloud Service, read the [AEM Cloud Manager deployment guide](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/deploy-code.html?lang=en)


## Maven settings

The project comes with the auto-public repository configured. To setup the repository in your Maven settings, refer to:

    http://helpx.adobe.com/experience-manager/kb/SetUpTheAdobeMavenRepository.html

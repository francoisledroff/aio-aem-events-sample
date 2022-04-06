# `aio-aem-events-sample` project 

This sample (working) project acts as a guide 
to help you get started with [`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events)
on AEM 6.5.

You can also decide to manually install the `aio-aem-events` `zip` package, don't forget to
* pick `aem65` classifier when downloading it from maven central
* once the package deployed, add the necessary OSGI configurations (see below)

## Making-of

Here are the few steps to build this repo from scratch:

### Start with AEM project Archetype

Generate your project using [AEM Project Archetype](https://experienceleague.adobe.com/docs/experience-manager-core-components/using/developing/archetype/overview.html?lang=en
), with the following command line :

    mvn -B archetype:generate -D archetypeGroupId=com.adobe.aem -D archetypeArtifactId=aem-project-archetype -D aemVersion="6.5.0" -D archetypeVersion=36 -D appTitle="My Site" -D appId="mysite" -D groupId="com.mysite"

Optionally (we did it to keep the sample easy to manage and understand) remove the unnecessary modules
* remove all modules but `all` and `ui.apps.structure`
* remove their reference from the root `pom.xml` and `all/pom.xml` files

### Add `aio-aem-events` as embedded package

Now we have the configuration ready, the last step is to
add `aio-aem-events` and `aio.config` as an Embedded/Sub packages.

For this edit `all/pom.xml` file, and
* set `aio-aem-events.version` in the `properties` section to use the latest [version from maven central](https://repo1.maven.org/maven2/com/adobe/aio/aem/aio-aem-events)
* add `aio-aem-events` in the `dependencies` section


      <dependency>
          <groupId>com.adobe.aio.aem</groupId>
          <artifactId>aio-aem-events</artifactId>
          <version>${aio-aem-events.version}</version>
          <classifier>aem65</classifier>
          <type>zip</type>
      </dependency>

* add `aio-aem-events` as an Embedded/Sub package, in the `filevault-package-maven-plugin` `embedded` `configuration` section:


      <embedded>
           <groupId>com.adobe.aio.aem</groupId>
           <artifactId>aio-aem-events</artifactId>
           <type>zip</type>
           <target>/apps/mysite-packages/application/install</target>
      </embedded>

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

## Add OSGI configurations 

[`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events)
* expects a `Workspace` OSGI configuration defined see [aio-aem-core](https://github.com/adobe/aio-lib-java/tree/main/aem/core_aem) docs more more details.
* leverages `AEM Link Externalizer` Configuration see [aio-aem-events-mgmt](https://github.com/adobe/aio-lib-java/tree/main/aem/events_mgmt_aem) docs more more details.

## Status Checks

Verify your set up using [aio_aem_events Status Check endpoints](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events#status-checks)
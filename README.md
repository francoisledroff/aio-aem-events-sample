# `aio-aem-events-sample` project 

This sample project acts as a guide 
to help you get started with [`aio-aem-events`](https://github.com/adobe/aio-lib-java/tree/main/aem/aio_aem_events)

This project first was generated using [AEM Project Archetype](https://experienceleague.adobe.com/docs/experience-manager-core-components/using/developing/archetype/overview.html?lang=en
), with the following command line :

    mvn -B archetype:generate \    
    -D archetypeGroupId=com.adobe.aem \
    -D archetypeArtifactId=aem-project-archetype \
    -D archetypeVersion=36 \
    -D appTitle="My Site" \
    -D appId="mysite" \
    -D groupId="com.mysite"

To keep the sample easy to manage and understand, I removed the unnecessary modules
* removed all modules but `all` 
* removed these modules from the main parent/root `pom.xml` file
* removed the reference to the deleted packages from the main `all/pom.xml` file in the `filevault-package-maven-plugin` `embedded` `configuration` section
But obviously, the above removals are optional, you may keep these modules, if you need them. 

I then edited `all/pom.xml` file, and did:
* set `aio-aem-events.version` in the `properties` section to use the latest [version from maven central](https://repo1.maven.org/maven2/com/adobe/aio/aem/aio-aem-events)
* add `aio-aem-events` in the `dependencies` section


      <dependency>
          <groupId>com.adobe.aio.aem</groupId>
          <artifactId>aio-aem-events</artifactId>
          <version>${aio-aem-events.version}</version>
          <type>zip</type>
      </dependency>

* add `aio-aem-events` as an Embed/Sub package, in the `filevault-package-maven-plugin` `embedded` `configuration` section:


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

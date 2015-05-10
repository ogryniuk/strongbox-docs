## What is stored in the Maven metadata?

The `maven-metadata.xml` file is a place where Maven stores basic information about artifacts. It can contain useful data such as, for example:

- Which timestamped artifact file represents the current `SNAPSHOT` artifact
- What the latest deployed version of an artifact is
- What the most recent released version of an artifact is
- What plugins can be found under a `groupId`
- What other artifacts (apart from the main one) have been deployed to the repository (along with their extensions)

## What types of metadata are there and where are they stored?

- `artifactId` level `maven-metadata.xml` (located under, for example: `org/carlspring/strongbox/metadata-example/maven-metadata.xml`). This is used for storing the top-level versions of artifacts. For example, if an artifact has versions `1.0`, `1.1`, `1.2`, this `maven-metadata.xml` will only contain version information about them. Consider the following `maven-metadata.xml`:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
        <groupId>org.carlspring.strongbox</groupId>
        <artifactId>metadata-example</artifactId>
        <versioning>
            <latest>1.2</latest>
            <release>1.2</release>
            <versions>
                <version>1.0</version>
                <version>1.1</version>
                <version>1.2</version>
            </versions>
            <lastUpdated>20150509185437</lastUpdated>
        </versioning>
     </metadata>
     ```
- Artifact `version`-level `maven-metadata.xml` (located under, for example: `org/carlspring/strongbox/strongbox-metadata/2.0-SNAPSHOT/maven-metadata.xml`). This is used only for timestamped `SNAPSHOT` versioned artifaccts. The purpose of this file is to contain a list of the existing timestamped artifacts, while at the same time specifying which one of them is the latest deployed one that should be used as the actual `SNAPSHOT` artifact to be resolved by Maven. The following is a brief example which illustrates the case where two timestamped `SNAPSHOT` artifacts have been deployed. Each deployment of an artifact increments the `<buildNumber/>` and updates the `<timestamp/>` fields.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
        <groupId>org.carlspring.strongbox</groupId>
        <artifactId>strongbox-metadata</artifactId>
        <version>2.0-SNAPSHOT</version>
        <versioning>
            <snapshot>
                <timestamp>20150508.221712</timestamp>
                <buildNumber>2</buildNumber>
            </snapshot>
            <lastUpdated>20150508221310</lastUpdated>
            <snapshotVersions>
                <snapshotVersion>
                    <classifier>javadoc</classifier>
                    <extension>jar</extension>
                    <value>2.0-20150508.220658-1</value>
                    <updated>20150508220658</updated>
                </snapshotVersion>
                <snapshotVersion>
                    <extension>jar</extension>
                    <value>2.0-20150508.220658-1</value>
                    <updated>20150508220658</updated>
                </snapshotVersion>
                <snapshotVersion>
                    <extension>pom</extension>
                    <value>2.0-20150508.220658-1</value>
                    <updated>20150508220658</updated>
                </snapshotVersion>
                <snapshotVersion>
                    <classifier>source-release</classifier>
                    <extension>jar</extension>
                    <value>2.0-20150508.221205-2</value>
                    <updated>20150508221205</updated>
                </snapshotVersion>
                <snapshotVersion>
                    <classifier>javadoc</classifier>
                    <extension>jar</extension>
                    <value>2.0-20150508.221205-2</value>
                    <updated>20150508221205</updated>
                </snapshotVersion>
                <snapshotVersion>
                    <extension>pom</extension>
                    <value>2.0-20150508.221205-2</value>
                    <updated>20150508221205</updated>
                </snapshotVersion>
            </snapshotVersions>
        </versioning>
    </metadata>
    ```

- `groupId`-level plugin information for plugins (located under, for example: `org/carlspring/maven/maven-metadata.xml`). This is a top-level `maven-metadata.xml` file containing a list of the available plugins under this `groupId`. This type of `maven-metadata.xml` file contains only `<plugins/>` and provides no version information.

   ```xml
    <metadata>
        <plugins>
            <plugin>
                <name>Maven Derby Plugin</name>
                <prefix>derby</prefix>
                <artifactId>derby-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <name>LittleProxy Maven Plugin</name>
                <prefix>little-proxy</prefix>
                <artifactId>little-proxy-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <name>Maven Artifact Relocation Plugin</name>
                <prefix>relocation</prefix>
                <artifactId>relocation-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <name>system-properties-maven-plugin</name>
                <prefix>system-properties</prefix>
                <artifactId>system-properties-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </metadata>
    ```


##What's the difference between `maven-metadata.xml` and `maven-metadata-xyz.xml`?
- In remote repositories, the metadata generated by Maven (and/or managed by the respective artifact repository manager) is stored in a `maven-metadata.xml` file.
- In local repositories, Maven stores the collected metadata from the remote server into a `maven-metadata-${repositoryServerId}.xml` file where `${repositoryServerId}` is the server id stored in your Maven [`settings.xml`](https://maven.apache.org/settings.html#Servers) (for example `<server><id>development-snapshots</id></server>`).

## What are the official resources on Maven metadata?
- [Introduction](http://maven.apache.org/ref/3.3.3/maven-repository-metadata/index.html)
- [maven-metadata.xml reference](http://maven.apache.org/ref/3.3.3/maven-repository-metadata/repository-metadata.html)
- [Javadocs](http://maven.apache.org/ref/3.3.3/maven-repository-metadata/apidocs/index.html)
# Introduction

We have created the Artifact Query Language (AQL) in order to be able to search for artifacts across the different layouts in an easy and consistent manner.

## AQL Synax

Search queries are constructed using _**tokens**_, where each token is a pair of `<key>:<value>` separated with `:`.

#### Keys

The full list of possible `keys` will vary depending on the enabled layout providers, but the following common list needs to work the same across all layouts:

| Key        | Description     | 
| ---------- |---------------- |
| storage    | Search for artifacts in a specific **storage id** |
| repository | Search for artifacts in a specific **repository id** |
| layout     | Search for artifacts in a specific **repository layout** |
| version    | Search an artifact by **version** |
| tag        | Search for artifacts with available **tag name** |
| from       | Search for uploaded artifacts starting **date** (unicode format, results include the date) |
| to         | Search for uploaded artifacts before **date** (unicode format, results include the date) |
| age        | Constant: `day`, `month`, `year`, etc. |
| asc        | Order results ascending |
| desc       | Order results descending |

Each layout provider exposes keys for each of it's the fields of it's implementation of the [`ArtifactCoordinates`](https://github.com/strongbox/strongbox/wiki/Artifact-Coordinates).

For example:

* For Maven, the artifact coordinates would be :
  * `groupId`
  * `artifactId`
  * `version`
  * `classifier`
  * `extension`
* For NuGet 
  * `Id`
  * `Version`
* For NPM
  * `scope`
  * `name`
  * `version`

#### Values

* _**Values**_ can be strings:
  * Quoted with single quotes `'` when the value is more than one word (for example: `storage: storage0`, `layout: 'Maven 2'`)
  * Separated with comma `,` for multiple values; you can consider this the same as `IN` operator in SQL  (for example: `repository: releases, snapshots`, `layout: 'Maven 2', NuGet`)
  * Wildcards are supported `*` (for example: `group: org.carlspring.*`)

* _**Values**_ can be dates in Unicode format: `2018-03-21 13:00:00`, `2018-03-21` (for example: `updated: 2018-03-21`, `updated: '2018-03-21 13:00'`)

* _**Values**_ can be keywords/constants: `day`, `month`, `year`, etc. (for example: `age: >= 30d`)

## Query expression

* Queries are composed using `Tokens` to create an expression:
    ```
    storage:storage0 repository:releases
    ```
&nbsp;    

* Expression parts can be surrounded by round brackets for more advanced queries. The following examples are equal: 
    ```
    storage:storage0 repository:releases
    ``` 
    ```
    (storage:storage0)(repository:releases)
    ```
    ```
    ((storage:storage0) (repository:releases))
    ```
&nbsp;    
* Expression parts can be joined by logical operators:
    * `AND` is implied by default and means logical conjunction (equivalent synonymous: `&`,`&&`)
    * `OR` means logical disjunction (equivalent synonymous: `|`,`||`)
    * The following examples are equal:
        ```
        storage:storage0 repository:releases
        ```
        ```
        (storage:storage0)(repository:releases)
        ``` 
        ```
        (storage:storage0) AND (repository:releases)
        ``` 
        ```
        storage:storage0 && repository:releases
        ```
&nbsp;    
* Expression parts can be prefixed with `+` and `-` for `inclusion` or `negation`. 
    * `+` is implied by default and means no negation (by and large does not mean anything, you can use it simply for clarity)
    * `-` means logical negation
    * The following examples are equal:
        ```
        storage:storage0 repository:releases NOT groupId: 'org.carlspring'
        ```
        ```
        storage:storage0 AND +repository:releases AND NOT groupId: 'org.carlspring'
        ``` 
        ```
        +(storage:storage0)+(repository:releases)-(groupId: 'org.carlspring')
        ``` 

# How to use

You can use AQL with UI search bar, which also provide autocomplete, or directly with REST API Endpoint (`curl` example below).
```
$ curl http://localhost:48080/api/aql?query=storage:storage-common-proxies+repository:carlspring+groupId:com.google*
{
  "artifact" : [ {
    "artifactCoordinates" : {
      "groupId" : "com.google.android",
      "artifactId" : "android",
      "version" : "4.1.1.4",
      "classifier" : null,
      "extension" : "jar"
    },
    "storageId" : "storage-common-proxies",
    "repositoryId" : "carlspring",
    "url" : "http://localhost:48080/storages/storage-common-proxies/carlspring/com/google/android/android/4.1.1.4/android-4.1.1.4.jar",
    "snippets" : [ {
      "name" : "Maven 2",
      "code" : "<dependency>\n    <groupId>com.google.android</groupId>\n    <artifactId>android</artifactId>\n    <version>4.1.1.4</version>\n    <type>jar</type>\n    <scope>compile</scope>\n</dependency>\n"
    }, {
      "name" : "Gradle",
      "code" : "compile \"com.google.android:android:4.1.1.4\"\n"
    }, {
      "name" : "Ivy",
      "code" : "<dependency org=\"com.google.android\" name=\"android\" rev=\"4.1.1.4\" />\n"
    }, {
      "name" : "Leiningen",
      "code" : "[com.google.android/android \"4.1.1.4\"]\n"
    }, {
      "name" : "SBT",
      "code" : "libraryDependencies += \"com.google.android\" % \"android\" % \"4.1.1.4\"\n"
    } ],
    "path" : "com/google/android/android/4.1.1.4/android-4.1.1.4.jar"
  } ]
}
```
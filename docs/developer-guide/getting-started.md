# Getting started

## Pre-requisites

* `Maven 3.5`
* `OpenJDK 1.8` (or `OracleJDK 1.8`)
* `Git` installed and in your `PATH` variable

## Before you continue

Please, place this [settings.xml]({{resources}}/maven/settings.xml) file under your `~/.m2` directory.
We have dependencies which are only available through our repository and if you skip this it will cause build failure.

```
curl -o ~/.m2/settings.xml \ 
     {{resources}}/maven/settings.xml
```

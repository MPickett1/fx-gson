# FX Gson

[![Bintray Download](https://img.shields.io/github/release/joffrey-bion/fx-gson.svg?label=bintray)](https://bintray.com/joffrey-bion/maven/fx-gson/_latestVersion)
[![Maven central version](https://img.shields.io/maven-central/v/org.hildan.fxgson/fx-gson.svg)](http://mvnrepository.com/artifact/org.hildan.fxgson/fx-gson)
[![Build Status](https://travis-ci.org/joffrey-bion/fx-gson.svg?branch=master)](https://travis-ci.org/joffrey-bion/fx-gson)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/joffrey-bion/fx-gson/blob/master/LICENSE)

This fork removes the `serializeNulls()` option that comes enabled on FxGson. I had to do this to be compatible with java-cloudant and CouchDB, since CouchDB expects some fields to be omitted when working with documents, namely the `_rev`and `_attachments` if they are not populated. I suggest cloning and building this via gradle to get a usable jar, if you want the `serializeNulls()` option off. Otherwise include it via Maven or Gradle.

FX Gson is a set of type adapters for [Google Gson](https://github.com/google/gson) to serialize JavaFX properties as 
their values, and deserialize values into properties.

FX Gson simply removes the property "wrapping" and delegates the serialization of the value to the Gson. This means that 
any configuration you add to Gson regarding a type will be taken into account when serializing a property of that type. 
This is true for objects and primitives.

## Why use FX Gson?

In JavaFX, POJOs usually contain `Property` objects instead of primitives. When serialized, we usually don't want to
see the internals of such `Property` objects in the produced JSON, but rather the actual value held by the property.

For instance, suppose the `Person` class is defined like this:

```java
public class Person {
    private final StringProperty firstName;
    private final StringProperty lastName;

    public Person(String firstName, String lastName) {
        this.firstName = new SimpleStringProperty(firstName);
        this.lastName = new SimpleStringProperty(lastName);
    }
    
    // getters, setters, and property getters are omitted for brevity
}
```
    
Here is how `new Person("Hans", "Muster")` is serialized:

<table>
    <tr>
        <th>With vanilla Gson</th>
        <th>With FxGson-configured Gson</th>
    </tr>
    <tr>
        <td>
        <pre>{
    "firstName": {
        "name": "",
        "value": "Hans",
        "valid": true,
        "helper": {
            "observable": {}
        }
    },
    "lastName": {
        "name": "",
        "value": "Muster",
        "valid": true,
        "helper": {
            "observable": {}
        }
    }
}</pre>
        </td>
        <td>
            <pre>{
    "firstName": "Hans",
    "lastName": "Muster"
}</pre>
        </td>
    </tr>
</table>

This produces a more human-readable output, and make manual modifications of the JSON easier.

## Usage

All you need to know is [in the wiki](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage), but here is a 
quick overview.

You can use FX Gson in multiple ways depending on the degree of customization you need:
- directly [create a ready-to-go `Gson`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#simple-ways-matter) able to serialize JavaFX properties

    ```java
    // to handle only Properties and Observable collections
    Gson fxGson = FxGson.create();
    
    // to also handle the Color & Font classes
    Gson fxGsonWithExtras = FxGson.createWithExtras();
    ```

- [create a pre-configured `GsonBuilder`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#using-pre-configured-gsonbuilders) that you can further configure yourself

    ```java
    Gson fxGson = FxGson.coreBuilder()
                        .registerTypeAdapterFactory(new MyFactory())
                        .disableHtmlEscaping()
                        .create();
    
    Gson fxGsonWithExtras = FxGson.fullBuilder()
                                  .registerTypeAdapter(Pattern.class, new PatternSerializer())
                                  .setPrettyPrinting()
                                  .create();
    ```

- [add JavaFX configuration to an existing `GsonBuilder`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#adding-javafx-support-to-an-existing-gsonbuilder)

    ```java
    GsonBuilder builder = MyLib.getBuilder();
    Gson gson = FxGson.addFxSupport(builder).create();
    ```

- [cherry-pick some pieces of FX Gson configuration](https://github.com/joffrey-bion/fx-gson/wiki/Customize-FX-Gson) and customize it to fit your needs

## Setup - adding the dependency

### Manual download
 
You may directly download the JAR from 
[FX Gson's Bintray Repository](https://bintray.com/joffrey-bion/maven/fx-gson/_latestVersion), although I recommend
using a build tool such as [Gradle](https://gradle.org/).
 
### Gradle

```groovy
compile 'org.hildan.fxgson:fx-gson:3.1.0'
```

### Maven

```xml
<dependency>
   <groupId>org.hildan.fxgson</groupId>
   <artifactId>fx-gson</artifactId>
   <version>3.1.0</version> <!-- replace with latest version -->
   <type>pom</type>
</dependency>
```    
## License

Code released under [the MIT license](https://github.com/joffrey-bion/io-utils/blob/master/LICENSE)

# Overview

Module that adds support for serialization/deserialization of [Kotlin](http://kotlinlang.org) classes and data classes.  Previously a default constructor must have existed on the Kotlin object for Jackson to deserialize into the object.  With this module, the primary non-default constructor can be used.

# Status

A release candidate is available on Maven Central:

* Release 2.4.2-rc1 (compatible with Kotlin 0.8.11 [M8 release] and Jackson 2.4.x)
* Master branch (unreleased, compatible with Kotlin 0.9.66 [M9 release] and Jackson 2.4.x)

Gradle:
```
compile 'com.fasterxml.jackson.module:jackson-module-kotlin:2.4.2-rc1'
```

Maven:
```xml
<dependency>
    <groupId>com.fasterxml.jackson.module</groupId>
    <artifactId>jackson-module-kotlin</artifactId>
    <version>2.4.2-rc1</version>
</dependency>
```


# Usage

For any Kotlin class or data class constructor, the JSON property names will be inferred from the parameters using Kotlin runtime type information.

To use, just register the Kotlin module with your ObjectMapper instance:

```kotlin
val mapper = ObjectMapper().registerModule(KotlinModule())
```

or with the extension functions imported from `import com.fasterxml.jackson.module.kotlin.*`, one of:

```kotlin
val mapper = jacksonObjectMapper()
```

```kotlin
val mapper = ObjectMapper().registerKotlinModule()
```

A data class example:
```kotlin
data class MyStateObject(val name: String, val age: Int)

fun jsonToMyStateObject(json: String): MyStateObject {
    val mapper = ObjectMapper().registerModule(KotlinModule())
    return mapper.readValue(json, javaClass<MyStateObject>())
}
```

You can intermix non-field values in the constructor and [JsonProperty] annotation in the constructor.  Any fields not present in the constructor will be set after the constructor call and therefore must be nullable with default value.  An example of these concepts:

```kotlin
   class StateObjectWithPartialFieldsInConstructor(val name: String, JsonProperty("age") val years: Int)    {
        JsonProperty("address") var primaryAddress: String? = null
        var createdDt: DateTime by Delegates.notNull()
    }
```

Note that using Delegates.notNull() will ensure that the value is never null when read, while letting it be instantiated after the construction of the class.

# Caveats

* The [JsonCreator] annotation is optional for the constructor unless Kotlin introduces secondary constructors in the future.  So this could change.
* Currently runtime type information in Kotlin is compatible with Kotlin 0.8.11, and in the future will use the upcoming Kotlin runtime type information which may require an update to this library.
 
# Support for Kotlin Built-in classes

These Kotlin classes are supported with the following fields for serialization/deserialization:

* Pair _(first, second)_
* Triple _(first, second, third)_
* IntRange _(start, end)_
* DoubleRange _(start, end)_
* CharRange _(start, end)_
* ByteRange _(start, end)_
* ShortRange _(start, end)_
* LongRange _(start, end)_
* FloatRange _(start, end)_

(others are likely to work, but may not be tuned for Jackson)
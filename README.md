## What is Easy Properties?

[![Join the chat at https://gitter.im/benas/easy-properties](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/benas/easy-properties?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Easy Properties is a Java library that allows you to inject configuration properties in Java objects in a declarative way using annotations.

The idea is to implement the _"Inversion Of Control"_ principle : Instead of having objects looking **actively** for configuration properties,
 these objects simply declare configuration properties they need, and these properties will be provided to them by a tool, Easy Properties for instance!

It is a kind of dependency injection, but for properties. Let's call it _"property injection"_.

## Why Easy Properties?

Dependency injection frameworks allow you to inject properties in your Java objects and they do it very well.

But in order to benefit from this feature, your code should run inside a DI container, or at least, the object in which your are trying to inject properties must be managed by a DI container.

What if your code does **not** run inside a DI container? This is where Easy Properties comes to play, to allow you to benefit from dependency injection without requiring your code to run inside a DI container.

That said, **Easy Properties is a library, not a DI framework**.

## Quick example

With Easy Properties, you declare properties you need on your object's fields using a set of intuitive annotations and instruct the library to inject these properties. Let's see an example:

Suppose you have a Java object of type `Bean` which should be configured with:

* An Integer property `threshold` from a system property passed to the JVM with `-Dthreshold=100`

* A String property `bean.name` from a properties file named `myProperties.properties`

To load these properties in your `Bean` object using Easy Properties, you annotate fields as follows:

```java
public class Bean {

    @Property(source = "myProperties.properties", key = "bean.name")
    private String beanName;

    @SystemProperty(value = "threshold", defaultValue = "50")
    private int threshold;

    //getters and setters omitted

}
```

and instructs Easy Properties to inject these properties in the annotated fields:

```java
//Instantiate your object
Bean bean = new Bean();

//Instantiate a properties injector
PropertiesInjector propertiesInjector = new PropertiesInjectorBuilder().build();

//Inject properties in your object
propertiesInjector.injectProperties(bean);
```

That's it! Easy Properties will:
 
* introspect the `Bean` instance looking for fields annotated with `@Property` and `@SystemProperty`
* convert each property value to the field's type
* and inject that value into the annotated field

**Without** Easy Properties, you would write something like this:

```java
public class Bean {

    private int threshold;

    private String beanName;

    public Bean() {

        //Load 'threshold' property from system properties
        String thresholdProperty = System.getProperty("threshold");
        if ( thresholdProperty != null ) {
            try {
                threshold = Integer.parseInt(thresholdProperty);
            } catch (NumberFormatException e) {
                // log exception
                threshold = 50; //default threshold value;
            }
        }

        //Load 'bean.name' property from properties file
        Properties properties = new Properties();
        try {
            InputStream inputStream = this.getClass().getClassLoader()
                        .getResourceAsStream("myProperties.properties");
            if (inputStream != null) {
                properties.load(inputStream);
                beanName = properties.getProperty("bean.name");
            }
        } catch (IOException ex) {
            // log exception
            beanName = "FOO"; // default bean name value
        }

    }

    //getters and setters omitted

}
```

As you can see, a lot of boilerplate code is written to load two properties, convert them to the target field type, etc.
Easy Properties takes care of all this boilerplate for you, which makes your code cleaner, more readable and maintainable.

In this quick example, you have seen two types of properties sources (system and resource bundle). 
Easy Properties allows you to inject properties from many other sources like databases, JNDI contexts, and more.

Even better, Easy Properties allows you write your own annotations and inject properties from a custom configuration source.

Checkout the complete reference in the project's [wiki](https://github.com/benas/easy-properties/wiki).

## Current version

* The current stable version is `1.2.0` : [![Maven Central](https://maven-badges.herokuapp.com/maven-central/io.github.benas/easy-properties/badge.svg?style=flat)](http://search.maven.org/#artifactdetails|io.github.benas|easy-properties|1.2.0|) 
* The current development version is `1.2.1-SNAPSHOT` : [![Build Status](https://travis-ci.org/benas/easy-properties.svg?branch=master)](https://travis-ci.org/benas/easy-properties)

In order to use snapshot versions, you need to add the following maven repository in your `pom.xml`:

```xml
<repository>
    <id>ossrh</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots</url>
</repository>
```

## Contribution

You are welcome to contribute to the project with pull requests on GitHub.

If you found a bug or want to request a feature, please use the [issue tracker](https://github.com/benas/easy-properties/issues).

For any further question, you can use the Gitter channel of the project: [![Join the chat](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/benas/easy-properties)

## Documentation

Easy Properties documentation can be found here : [https://github.com/benas/easy-properties/wiki](https://github.com/benas/easy-properties/wiki)

## Awesome contributors

* [natlantisprog](https://github.com/natlantisprog)

## License
Easy Properties is released under the [![MIT license](http://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat)](http://opensource.org/licenses/MIT).

```
The MIT License (MIT)

Copyright (c) 2016 Mahmoud Ben Hassine (mahmoud.benhassine@icloud.com)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```

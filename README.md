# scala-logging [![Build Status](https://travis-ci.org/typesafehub/scala-logging.svg?branch=master)](https://travis-ci.org/typesafehub/scala-logging)

Scala Logging is a **convenient** and **performant** logging library wrapping [SLF4J](http://www.slf4j.org).

It's convenient, because you can simply call log methods, *without* checking whether the respective log level is enabled:

```scala
logger.debug(s"Some $expensive message!")
```

It's performant, because thanks to Scala macros the *check-enabled-idiom* is applied and the following code is generated:

```scala
if (logger.isDebugEnabled) logger.debug(s"Some $expensive message!")
```

## Prerequisites ##

* Java 6 or higher
* Scala 2.11 or 2.12
* Logging backend compatible with SLF4J

A compatible logging backend is [Logback](http://logback.qos.ch), add it to your sbt build definition:

```scala
libraryDependencies += "ch.qos.logback" % "logback-classic" % "1.2.3"
```

If you are looking for a version compatible with Scala 2.10, check out Scala Logging 2.x.

## Getting Scala Logging ##

Scala Logging is published to Sonatype OSS and Maven Central:

- Group id / organization: *com.typesafe.scala-logging*
- Artifact id / name: *scala-logging*
- Latest version is 3.6.0

Usage with SBT, adding a dependency to the latest version of Scala Logging to your sbt build definition file:

```scala
libraryDependencies += "com.typesafe.scala-logging" %% "scala-logging" % "3.6.0"
```

## Using Scala Logging ##

The `Logger` class from the `com.typesafe.scalalogging` package wraps an underlying SLF4J logger.
In order to create a `Logger`, you pass a name to the `apply` factory method defined in the `Logger` companion object:

```scala
val logger = Logger("name")
```

Or, you pass in a SLF4J logger instance:

```scala
val logger = Logger(LoggerFactory.getLogger("name"))
```

Or, you pass in a class:

```scala
val logger = Logger(classOf[MyClass])
```

Or, using the runtime class wrapped by the implicit class tag parameter:

```scala
val logger = Logger[MyClass]
```

The `LazyLogging` and `StrictLogging` traits from the `com.typesafe.scalalogging` package define the `logger` member as
a lazy or strict value respectively. In both cases the underlying SLF4J logger is named according to the class into which
these traits are mixed:

```scala
class MyClass extends LazyLogging {
  logger.debug("This is very convenient ;-)")
}
```

### What's new?

##### 3.6.0
 - Deconstruct Scala's string interpolation into SLF4J string interpolation.

##### 3.5.0
 - More Logger factory methods, bugfixes and upgrades, published for Scala 2.12.0-M5, 2.12.0-RC1, 2.12.0-RC2 and 2.12.0.

##### 3.4.0
 - Fixes #38 - Logger.info() cannot be used with primitive types.

##### 3.3.0
 - Fixes #42 - Request: Add Logger(class). README changes.

##### 3.2.0
 - SLF4J loggers and our Logger now survive serialization. By survive serialization, we mean that the
   deserialized logger instances are fully functional.

## String Interpolation
It is idiomatic to use Scala's string interpolation `logger.error(s"log $value")` instead of SLF4J string interpolation `logger.error("log {}", value)`.
However there are some tools (such as [Sentry](https://sentry.io)) that use the log message format as grouping key. Therefore they do not work well with
Scala's string interpolation.

Scala Logging replaces simple string interpolations with their SLF4J counterparts like this:

```scala
logger.error(s"my log message: $arg1 $arg2 $arg3")
```

```scala
logger.error("my log message: {} {} {}", arg1, arg2, arg3)
```

This has no effect on behavior and performace should be comparable (depends on the underlying logging library).

### Limitations
 - Works only when string interpolation is directly used inside the logging statement. That is when the log message is static (available at compile time).
 - Works only for the `logger.<level>(message)` and `logger.<level>(marker, message)` logging methods. It does not work if you want to log an exception and
 use string interpolation too (this is a limitation of the SLF4J API).

## Line numbers in log message?

Using the [sourcecode](https://github.com/lihaoyi/sourcecode#logging) library, it's possible to add line number
information (especially useful for debugging):

```scala
def foo(arg: String)(implicit line: sourcecode.Line, file: sourcecode.File) = {
  ... do something with arg ...
  ... do something with file.value ...
}

foo("hello") // the implicit sourcecode.File is filled in automatically
```

## Debugging Scala in IntelliJ

Check out [scala-trace-debug](https://github.com/JohnReedLOL/scala-trace-debug) to make multithreaded bug tracing and
prevention easier than ever. Provides user-friendly prints, traces, assertions,
container printing, and source code printing.

## Logstash

Check out [logstash-logback-encoder](https://github.com/logstash/logstash-logback-encoder) if you're using [Logstash](https://www.elastic.co/products/logstash).
Provides logback encoders, layouts, and appenders to log in JSON format.

## Other noteworthy tooling

 - https://github.com/godaddy/godaddy-logger

## Maintainer ##

The original author [Heiko Seeberger](https://twitter.com/hseeberger) stepped down Q1 2015, starting a new adventure at codecentric. Future maintenance is taken
over by [Mathias Bogaert](http://twitter.com/analytically).

## Contribution policy ##

Contributions via GitHub pull requests are gladly accepted from their original author. Before we can accept pull requests, you will need to agree to the [Typesafe Contributor License Agreement](http://www.typesafe.com/contribute/cla) online, using your GitHub account.

## License ##

This code is open source software licensed under the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0.html).

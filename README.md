
Retrolambda: Use Lambdas on Java 7
==================================

Just as there was [Retroweaver](http://retroweaver.sourceforge.net/) et al.
for running Java 5 code with generics on Java 1.4, **Retrolambda** lets you
run Java 8 code with lambda expressions on Java 7 or lower. It does this by
transforming your Java 8 compiled bytecode so that it can run on a Java 7
runtime. After the transformation they are just a bunch of normal .class
files, without adding any runtime dependencies.

Adventurous developers may use Retrolambda to backport lambda expressions
even to Java 6 or Java 5. And if you reach Java 5, there are [other
backporting tools](http://en.wikipedia.org/wiki/Java_backporting_tools)
that may let you go down to Java 1.4.

Android developers may also use Retrolambda. [There are reports](http://blog.orfjackal.net/2013/07/lambda-expressions-backported-to-java-7.html?showComment=1374655919855#c222763220984063335)
of it working on the Dalvik VM.


User Guide
----------

### Getting Started

[Download](http://repo.maven.apache.org/maven2/net/orfjackal/retrolambda/retrolambda/)
the latest `retrolambda.jar` from Maven Central.

Use JDK 8 to compile your source code.

Run Retrolambda, using Java 8, on the class files produced by JDK 8. Run
`java -jar retrolambda.jar` without any additional options to see the
instructions.

Your class files should now run on Java 7. Be sure to run comprehensive tests
on Java 7, in case the code accidentally uses Java 8 APIs or language features
that Retrolambda doesn't backport.


### Tips

For an example of how to run Retrolambda using Maven, see how
maven-dependency-plugin and maven-antrun-plugin are used in
[end-to-end-tests/pom.xml](https://github.com/orfjackal/retrolambda/blob/master/end-to-end-tests/pom.xml)
There isn't yet a Maven plugin for doing that with less boilerplate, but maybe
later.

During development, inside an IDE, it's the easiest to use Java 8, without
Retrolamba, to compile and run tests. But in your continuous integration build
you should run tests using the target Java version. For example, you can
configure Maven Surefire Plugin to run tests
[using a different JVM](http://maven.apache.org/surefire/maven-surefire-plugin/test-mojo.html#jvm).

I recommend setting up environment variables JAVA8_HOME, JAVA7_HOME etc. and
referring to those variables in the build configuration, instead of relying on
what happens to be the default Java version in JAVA_HOME.

You will need Java 8 for compiling and also for generating Javadocs.


### Third Party Tools

- [Gradle Retrolamba Plugin](https://github.com/evant/gradle-retrolambda)

If you have things to add to this list, [create a pull request](https://github.com/orfjackal/retrolambda/pulls).


Known Limitations
-----------------

Does not backport the use of Java 8 APIs.

Does not backport Java 8 language features other than lambda expressions.

Does not support serializable lambda expressions. Implementing support for
them would technically be possible, but it would require projects to have a
runtime dependency on a library which would contain a backported copy of
the `java.lang.invoke.SerializedLambda` class. If you really need it, make
a feature request. ;-)

May break if a future JDK 8 build stops generating a new class for each
`invokedynamic` call. Retrolambda works so that it captures the bytecode
that `java.lang.invoke.LambdaMetafactory` generates dynamically, so
optimizations to that mechanism may break Retrolambda.


Version History
---------------

### Retrolambda 1.1.0 (2013-07-25)

- Create only one instance of lambdas which do not capture arguments; i.e.
  the same optimization as what JDK 8 does
- Start the sequence number of lambda classes from one (e.g.
  `com.example.Foo$$Lambda$1`) for each enclosing class

### Retrolambda 1.0.0 (2013-07-23)

- Backports lambda expressions and method handles to Java 7 and older
- Tested to work with JDK 8 Early Access Build b99 (2013-07-19)

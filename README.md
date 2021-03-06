dokka  [![official JetBrains project](https://jb.gg/badges/official.svg)](https://confluence.jetbrains.com/display/ALL/JetBrains+on+GitHub)
[![TeamCity (build status)](https://img.shields.io/teamcity/http/teamcity.jetbrains.com/s/Kotlin_Dokka_DokkaAntMavenGradle.svg)](https://teamcity.jetbrains.com/viewType.html?buildTypeId=Kotlin_Dokka_DokkaAntMavenGradle&branch_KotlinTools_Dokka=%3Cdefault%3E&tab=buildTypeStatusDiv) [ ![Download](https://api.bintray.com/packages/kotlin/dokka/dokka/images/download.svg) ](https://bintray.com/kotlin/dokka/dokka/_latestVersion)
=====

Dokka is a documentation engine for Kotlin, performing the same function as javadoc for Java.
Just like Kotlin itself, Dokka fully supports mixed-language Java/Kotlin projects. It understands
standard Javadoc comments in Java files and [KDoc comments](https://kotlinlang.org/docs/reference/kotlin-doc.html) in Kotlin files,
and can generate documentation in multiple formats including standard Javadoc, HTML and Markdown.

## Using Dokka

### Using the Gradle plugin

```groovy
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "org.jetbrains.dokka:dokka-gradle-plugin:${dokka_version}"
    }
}
repositories {
    jcenter() // or maven { url 'https://dl.bintray.com/kotlin/dokka' }
}

apply plugin: 'org.jetbrains.dokka'
```

or using the new plugins block:

```groovy
plugins {
    id 'org.jetbrains.dokka' version '0.9.18'
}
repositories {
    jcenter() // or maven { url 'https://dl.bintray.com/kotlin/dokka' }
}
```

The plugin adds a task named "dokka" to the project.

If you encounter any problems when migrating from older versions of Dokka, please see the [FAQ](https://github.com/Kotlin/dokka/wiki/faq).

Minimal dokka configuration:

```groovy
dokka {
    outputFormat = 'html' 
    outputDirectory = "$buildDir/javadoc"
}
```

[Output formats](#output_formats)

The available configuration options are shown below:

```groovy
dokka {
    moduleName = 'data'
    outputFormat = 'html'
    outputDirectory = "$buildDir/javadoc"
    
    // These tasks will be used to determine source directories and classpath 
    kotlinTasks {
        defaultKotlinTasks() + [':some:otherCompileKotlin', project("another").compileKotlin]
    }
    
    // List of files with module and package documentation
    // https://kotlinlang.org/docs/reference/kotlin-doc.html#module-and-package-documentation
    includes = ['packages.md', 'extra.md']
    
    // The list of files or directories containing sample code (referenced with @sample tags)
    samples = ['samples/basic.kt', 'samples/advanced.kt']
    
    jdkVersion = 6 // Used for linking to JDK

    // Use default or set to custom path to cache directory
    // to enable package-list caching
    // When set to default, caches stored in $USER_HOME/.cache/dokka
    cacheRoot = 'default' 
    
    // Use to include or exclude non public members.
    includeNonPublic = false
    
    // Do not output deprecated members. Applies globally, can be overridden by packageOptions
    skipDeprecated = false 
   
    // Emit warnings about not documented members. Applies globally, also can be overridden by packageOptions
    reportUndocumented = true 
    
    skipEmptyPackages = true // Do not create index pages for empty packages
 
    impliedPlatforms = ["JVM"] // See platforms section of documentation 
    
    // Manual adding files to classpath
    // This property doesn't override classpath collected from kotlinTasks but appends to it
    classpath = [new File("$buildDir/other.jar")]

    // By default, sourceRoots is taken from kotlinTasks, following roots will be appended to it
    // Short form sourceRoots
    sourceDirs = files('src/main/kotlin')
    
    // By default, sourceRoots is taken from kotlinTasks, following roots will be appended to it
    // Full form sourceRoot declaration
    // Repeat for multiple sourceRoots
    sourceRoot {
        // Path to source root
        path = "src" 
        // See platforms section of documentation 
        platforms = ["JVM"] 
    }
    
    // Specifies the location of the project source code on the Web.
    // If provided, Dokka generates "source" links for each declaration.
    // Repeat for multiple mappings
    linkMapping {
        // Unix based directory relative path to the root of the project (where you execute gradle respectively). 
        dir = "src/main/kotlin" // or simply "./"
         
        // URL showing where the source code can be accessed through the web browser
        url = "https://github.com/cy6erGn0m/vertx3-lang-kotlin/blob/master/src/main/kotlin" //remove src/main/kotlin if you use "./" above
        
        // Suffix which is used to append the line number to the URL. Use #L for GitHub
        suffix = "#L"
    }
    
    // Disable linking to online kotlin-stdlib documentation
    noStdlibLink = false
    
    // Disable linking to online JDK documentation
    noJdkLink = false 
    
    // Allows linking to documentation of the project's dependencies (generated with Javadoc or Dokka)
    // Repeat for multiple links
    externalDocumentationLink {
        // Root URL of the generated documentation to link with. The trailing slash is required!
        url = new URL("https://example.com/docs/")
        
        // If package-list file located in non-standard location
        // packageListUrl = new URL("file:///home/user/localdocs/package-list") 
    }
    
    // Allows to customize documentation generation options on a per-package basis
    // Repeat for multiple packageOptions
    packageOptions {
        prefix = "kotlin" // will match kotlin and all sub-packages of it
        // All options are optional, default values are below:
        skipDeprecated = false
        reportUndocumented = true // Emit warnings about not documented members 
        includeNonPublic = false
    }
    // Suppress a package
    packageOptions {
        prefix = "kotlin.internal" // will match kotlin.internal and all sub-packages of it
        suppress = true
    }
}
```

To generate the documentation, use the `dokka` Gradle task:

```bash
./gradlew dokka
```

More dokka tasks can be added to a project like this:

```groovy
task dokkaJavadoc(type: org.jetbrains.dokka.gradle.DokkaTask) {
    outputFormat = 'javadoc'
    outputDirectory = "$buildDir/javadoc"
}
```

Please see the [Dokka Gradle example project](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/dokka-gradle-example) for an example.

#### Dokka Runtime
If you are using Gradle plugin and you want to change the version of Dokka, you can do it by setting `dokkaRuntime`:

```groovy
buildscript {
    ...
}

apply plugin: 'org.jetbrains.dokka'

repositories {
    jcenter()
}

dependencies {
    dokkaRuntime "org.jetbrains.dokka:dokka-fatjar:0.9.18"
}
```

#### FAQ
If you encounter any problems, please see the [FAQ](https://github.com/Kotlin/dokka/wiki/faq).

#### Android

If you are using Android there is a separate Gradle plugin. Just make sure you apply the plugin after
`com.android.library` and `kotlin-android`.

```groovy
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "org.jetbrains.dokka:dokka-android-gradle-plugin:${dokka_version}"
    }
}
repositories {
    jcenter()
}
apply plugin: 'com.android.library'
apply plugin: 'kotlin-android'
apply plugin: 'org.jetbrains.dokka-android'
```

### Using the Maven plugin

The Maven plugin is available in JCenter. You need to add the JCenter repository to the list of plugin repositories if it's not there:

```xml
<pluginRepositories>
    <pluginRepository>
        <id>jcenter</id>
        <name>JCenter</name>
        <url>https://jcenter.bintray.com/</url>
    </pluginRepository>
</pluginRepositories>
```

Minimal Maven configuration is

```xml
<plugin>
    <groupId>org.jetbrains.dokka</groupId>
    <artifactId>dokka-maven-plugin</artifactId>
    <version>${dokka.version}</version>
    <executions>
        <execution>
            <phase>pre-site</phase>
            <goals>
                <goal>dokka</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

By default files will be generated in `target/dokka`.

The following goals are provided by the plugin:

  * `dokka:dokka` - generate HTML documentation in Dokka format (showing declarations in Kotlin syntax);
  * `dokka:javadoc` - generate HTML documentation in JavaDoc format (showing declarations in Java syntax);
  * `dokka:javadocJar` - generate a .jar file with JavaDoc format documentation.

The available configuration options are shown below:

```xml
<plugin>
    <groupId>org.jetbrains.dokka</groupId>
    <artifactId>dokka-maven-plugin</artifactId>
    <version>${dokka.version}</version>
    <executions>
        <execution>
            <phase>pre-site</phase>
            <goals>
                <goal>dokka</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
    
        <!-- Set to true to skip dokka task, default: false -->
        <skip>false</skip>
    
        <!-- Default: ${project.artifactId} -->
        <moduleName>data</moduleName>
        <!-- See list of possible formats below -->
        <outputFormat>html</outputFormat>
        <!-- Default: ${project.basedir}/target/dokka -->
        <outputDir>some/out/dir</outputDir>
        
        <!-- Use default or set to custom path to cache directory to enable package-list caching. -->
        <!-- When set to default, caches stored in $USER_HOME/.cache/dokka -->
        <cacheRoot>default</cacheRoot>

        <!-- List of '.md' files with package and module docs -->
        <!-- https://kotlinlang.org/docs/reference/kotlin-doc.html#module-and-package-documentation -->
        <includes>
            <include>packages.md</include>
            <include>extra.md</include>
        </includes>
        
        <!-- List of sample roots -->
        <samplesDirs>
            <dir>src/test/samples</dir>
        </samplesDirs>
        
        <!-- Used for linking to JDK, default: 6 -->
        <jdkVersion>6</jdkVersion>

        <!-- Do not output deprecated members, applies globally, can be overridden by packageOptions -->
        <skipDeprecated>false</skipDeprecated> 
        <!-- Emit warnings about not documented members, applies globally, also can be overridden by packageOptions -->
        <reportNotDocumented>true</reportNotDocumented>             
        <!-- Do not create index pages for empty packages -->
        <skipEmptyPackages>true</skipEmptyPackages> 
        
        <!-- See platforms section of documentation -->
        <impliedPlatforms>
            <platform>JVM</platform>
        </impliedPlatforms>
        
        <!-- Short form list of sourceRoots, by default, set to ${project.compileSourceRoots} -->
        <sourceDirectories>
            <dir>src/main/kotlin</dir>
        </sourceDirectories>
        
        <!-- Full form list of sourceRoots -->
        <sourceRoots>
            <root>
                <path>src/main/kotlin</path>
                <!-- See platforms section of documentation -->
                <platforms>JVM</platforms>
            </root>
        </sourceRoots>
        
        <!-- Specifies the location of the project source code on the Web. If provided, Dokka generates "source" links
             for each declaration. -->
        <sourceLinks>
            <link>
                <!-- Source directory -->
                <dir>${project.basedir}/src/main/kotlin</dir>
                <!-- URL showing where the source code can be accessed through the web browser -->
                <url>https://github.com/cy6erGn0m/vertx3-lang-kotlin/blob/master/src/main/kotlin</url> <!-- //remove src/main/kotlin if you use "./" above -->
                <!--Suffix which is used to append the line number to the URL. Use #L for GitHub -->
                <urlSuffix>#L</urlSuffix>
            </link>
        </sourceLinks>
        
        <!-- Disable linking to online kotlin-stdlib documentation  -->
        <noStdlibLink>false</noStdlibLink>
        
        <!-- Disable linking to online JDK documentation -->
        <noJdkLink>false</noJdkLink>
        
        <!-- Allows linking to documentation of the project's dependencies (generated with Javadoc or Dokka) -->
        <externalDocumentationLinks>
            <link>
                <!-- Root URL of the generated documentation to link with. The trailing slash is required! -->
                <url>https://example.com/docs/</url>
                <!-- If package-list file located in non-standard location -->
                <!-- <packageListUrl>file:///home/user/localdocs/package-list</packageListUrl> -->
            </link>
        </externalDocumentationLinks>

        <!-- Allows to customize documentation generation options on a per-package basis -->
        <perPackageOptions>
            <packageOptions>
                <!-- Will match kotlin and all sub-packages of it -->
                <prefix>kotlin</prefix>
                
                <!-- All options are optional, default values are below: -->
                <skipDeprecated>false</skipDeprecated>
                <!-- Emit warnings about not documented members  -->
                <reportUndocumented>true</reportUndocumented>
                <includeNonPublic>false</includeNonPublic>
            </packageOptions>
        </perPackageOptions>
    </configuration>
</plugin>
```

Please see the [Dokka Maven example project](https://github.com/JetBrains/kotlin-examples/tree/master/maven/dokka-maven-example) for an example.

[Output formats](#output_formats)

### Using the Ant task

The Ant task definition is also contained in the dokka-fatjar.jar referenced above. Here's an example of using it:

```xml
<project name="Dokka" default="document">
    <typedef resource="dokka-antlib.xml" classpath="dokka-fatjar.jar"/>

    <target name="document">
        <dokka src="src" outputdir="doc" modulename="myproject"/>
    </target>
</project>
```

The Ant task supports the following attributes:

  * `outputDir` - the output directory where the documentation is generated
  * `outputFormat` - the output format (see the list of supported formats above)
  * `classpath` - list of directories or .jar files to include in the classpath (used for resolving references)
  * `samples` - list of directories containing sample code (documentation for those directories is not generated but declarations from them can be referenced using the `@sample` tag)
  * `moduleName` - the name of the module being documented (used as the root directory of the generated documentation)
  * `include` - names of files containing the documentation for the module and individual packages
  * `skipDeprecated` - if set, deprecated elements are not included in the generated documentation
  * `jdkVersion` - version for linking to JDK
  * `impliedPlatforms` - See [platforms](#platforms) section
  * `<sourceRoot path="src" platforms="JVM" />` - analogue of src, but allows to specify [platforms](#platforms) 
  * `<packageOptions prefix="kotlin" includeNonPublic="false" reportUndocumented="true" skipDeprecated="false"/>` - 
    Per package options for package `kotlin` and sub-packages of it
  * `noStdlibLink` - disable linking to online kotlin-stdlib documentation
  * `noJdkLink` - disable linking to online JDK documentation
  * `<externalDocumentationLink url="https://example.com/docs/" packageListUrl="file:///home/user/localdocs/package-list"/>` -
    linking to external documentation, packageListUrl should be used if package-list located not in standard location
  * `cacheRoot` - Use `default` or set to custom path to cache directory to enable package-list caching. When set to `default`, caches stored in $USER_HOME/.cache/dokka


### Using the Command Line

To run Dokka from the command line, download the [Dokka jar](https://github.com/Kotlin/dokka/releases/download/0.9.10/dokka-fatjar.jar).
To generate documentation, run the following command:

    java -jar dokka-fatjar.jar <source directories> <arguments>

Dokka supports the following command line arguments:

  * `-output` - the output directory where the documentation is generated
  * `-format` - the [output format](#output-formats):
  * `-classpath` - list of directories or .jar files to include in the classpath (used for resolving references)
  * `-samples` - list of directories containing sample code (documentation for those directories is not generated but declarations from them can be referenced using the `@sample` tag)
  * `-module` - the name of the module being documented (used as the root directory of the generated documentation)
  * `-include` - names of files containing the documentation for the module and individual packages
  * `-nodeprecated` - if set, deprecated elements are not included in the generated documentation
  * `-impliedPlatforms` - list of implied platforms (comma-separated)
  * `-packageOptions` - list of package options in format `prefix,-deprecated,-privateApi,+warnUndocumented;...` 
  * `-links` - external documentation links in format `url^packageListUrl^^url2...`
  * `-noStdlibLink` - disable linking to online kotlin-stdlib documentation
  * `-noJdkLink` - disable linking to online JDK documentation
  * `-cacheRoot` - use `default` or set to custom path to cache directory to enable package-list caching. When set to `default`, caches stored in $USER_HOME/.cache/dokka


### Output formats<a name="output_formats"></a>

  * `html` - minimalistic html format used by default, Java classes are translated to Kotlin
  * `javadoc` - looks like normal Javadoc, Kotlin classes are translated to Java
  * `html-as-java` - looks like `html`, but Kotlin classes are translated to Java
  * `markdown` - markdown structured as `html`, Java classes are translated to Kotlin
    * `gfm` - GitHub flavored markdown
    * `jekyll` - Jekyll compatible markdown 
  * `kotlin-website*` - internal format used for documentation on [kotlinlang.org](https://kotlinlang.org)

### Platforms<a name="platforms"></a>

Dokka can annotate elements with special `platform` block with platform requirements 

Example of usage can be found on [kotlinlang.org](https://kotlinlang.org/api/latest/jvm/stdlib/)

Each source root has a list of platforms for which members are suitable. 
Also, the list of 'implied' platforms is passed to Dokka.
If a member is not available for all platforms in the implied platforms set, its documentation will show
the list of platforms for which it's available.

## Dokka Internals

### Documentation Model

Dokka uses Kotlin-as-a-service technology to build `code model`, then processes it into `documentation model`.
`Documentation model` is graph of items describing code elements such as classes, packages, functions, etc.

Each node has semantic attached, e.g. Value:name -> Type:String means that some value `name` is of type `String`.

Each reference between nodes also has semantic attached, and there are three of them:

1. Member - reference means that target is member of the source, form tree.
2. Detail - reference means that target describes source in more details, form tree.
3. Link - any link to any other node, free form.

Member & Detail has reverse Owner reference, while Link's back reference is also Link.

Nodes that are Details of other nodes cannot have Members.

### Rendering Docs

When we have documentation model, we can render docs in various formats, languages and layouts. We have some core services:

* FormatService -- represents output format
* LocationService -- represents folder and file layout
* SignatureGenerator -- represents target language by generating class/function/package signatures from model

Basically, given the `documentation` as a model, we do this:

```kotlin
    val signatureGenerator = KotlinSignatureGenerator()
    val locationService = FoldersLocationService(arguments.outputDir)
    val markdown = JekyllFormatService(locationService, signatureGenerator)
    val generator = FileGenerator(signatureGenerator, locationService, markdown)
    generator.generate(documentation)
```

## Building Dokka

Dokka is built with Gradle. To build it, use `./gradlew build`.
Alternatively, open the project directory in IntelliJ IDEA and use the IDE to build and run Dokka.

Here's how to import and configure Dokka in IntelliJ IDEA:
 * Select "Open" from the IDEA welcome screen, or File > Open if a project is
  already open
* Select the directory with your clone of Dokka
  * Note: IDEA may have an error after the project is initally opened; it is OK
    to ignore this as the next step will address this error
* After IDEA opens the project, select File > New > Module from existing sources
  and select the `build.gradle` file from the root directory of your Dokka clone
* Use the default options and select "OK"
* After Dokka is loaded into IDEA, open the Gradle tool window (View > Tool
  Windows > Gradle) and click on the top left "Refresh all Gradle projects"
  button
* Verify the following project settings.  In File > Settings > Build, Execution,
  Deployment > Build Tools > Gradle > Runner:
  * Ensure "Delegate IDE build/run actions to gradle" is checked
  * "Gradle Test Runner" should be selected in the "Run tests using" drop-down
    menu
* Note: After closing and re-opening the project, IDEA may give an error
  message: "Error Loading Project: Cannot load 3 modules".  Open up the details
  of the error, and click "Remove Selected", as these module `.iml` files are
  safe to remove.

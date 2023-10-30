# Gazelle extension for java demo
This demo uses the gazelle extension from rules_jvm to generate rules for the maven hello world example generated with the following command.
```
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false

```
## External dependencies.
The following dependency was added to the pom.xml file to demonstrate the use of external dependencies.

```
    <dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.15.3</version>
    </dependency>
```

This way, we are able to use the jackson library in the [App.java](src/main/java/com/mycompany/app/App.java) file.
```
import com.fasterxml.jackson.databind.ObjectMapper;
```

To be able to use this dependency when building with Bazel, we will use `rules_jvm_external` and add it
 to the `maven_install` rule of the `WORKSPACE` file.
```
maven_install(
    artifacts = [
        "com.fasterxml.jackson.core:jackson-databind:2.15.3"
    ],
	...
)
```

### The maven_install.json Lockfile
As explained [here](https://github.com/bazelbuild/rules_jvm_external#pinning-artifacts-and-integration-with-bazels-downloader) Bazel can pin the external dependencies to the [maven_install.json](maven_install.json) file.
This is also important to let gazelle know about these external dependencies for the BUILD files generateion.

**To generate the `maven_install.json` file, follow the instructions from the above link.**

## Setup gazelle
Setup gazelle as explained here : https://github.com/bazel-contrib/rules_jvm/tree/main/java/gazelle
`WORKSPACE` file and top level `BUILD` files.

- Some more dependencies needed to be added in the [WORKSPACE](WORKSPACE) file.
- Because of the following [issue](https://github.com/bazel-contrib/rules_jvm/issues/211) this [patch](parser_path.patch) is added to the `http_archive` command that downloads the `rules_jvm` dependency in the [WORKSPACE](WORKSPACE) file.

## Configure project specific settings for gazelle in the top level BUILD file
Importantly: tell gazelle about the `maven_install.json` lockfile so that it can automatically add the external dependencies.
```
# gazelle:prefix com.mycompany.app
# gazelle:java_maven_install_file maven_install.json
```

## Run gazelle to generate the BUILD files.
```
bazel run //:gazelle
```

This will generate Bazel build file


### [src/main/java/com/mycompany/app/BUILD.bazel](src/main/java/com/mycompany/app/BUILD.bazel)

Which contains the `java_library` that depends on the external jackson one, as well as a `java_binary`.

### [src/test/java/com/mycompany/app/BUILD.bazel](src/test/java/com/mycompany/app/BUILD.bazel)

Which contains a java_test_suite target.
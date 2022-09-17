# dependabot-clone-with-maven
If you cannot use GitHub Dependabot for whatever reason, here is a Maven profile which works like a Dependabot clone to help you accomplish the same goal: keep your `pom.xml` dependencies always up-to-date


## Quick start guide
### 1. Add profile below to the `<profiles>` section of your pom.xml
```xml
    <profile>
      <id>update-pom</id>
      <build>
        <plugins>
          <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
              <execution>
                <id>update-pom</id>
                <!-- bind to validate phase to update pom.xml asap -->
                <phase>validate</phase>
                <goals>
                  <goal>exec</goal>
                </goals>
                <configuration>
                  <executable>mvn</executable>
                  <!-- IMPORTANT: plugins can only be updated if their versions are managed using properties -->
                  <commandlineArgs>versions:update-parent versions:update-properties versions:use-latest-releases</commandlineArgs>
                </configuration>
              </execution>
              <execution>
                <id>commit-pom</id>
                <phase>validate</phase>
                <goals>
                  <goal>exec</goal>
                </goals>
                <configuration>
                  <executable>git</executable>
                  <!-- IMPORTANT: you can customize the commit message -->
                  <commandlineArgs>commit -am "bump pom.xml versions"</commandlineArgs>
                  <!-- avoids build break when there is no update -->
                  <successCodes>0,1</successCodes>
                </configuration>
              </execution>
              <execution>
                <id>push-pom</id>
                <phase>validate</phase>
                <goals>
                  <goal>exec</goal>
                </goals>
                <configuration>
                  <executable>git</executable>
                  <commandlineArgs>push</commandlineArgs>
                </configuration>
              </execution>
            </executions>
          </plugin>
        </plugins>
      </build>
    </profile>
```


### 2. Run Maven
```
mvn clean compile -Pupdate-pom
```

#### Tips
- You can setup your CICD pipeline to build using this profile whenever e.g. a `feature` branch is created
- You can also add "Optional Parameters" like `-DallowMajorUpdates=false` to the `commandlineArgs` in the `pom.xml` e.g.:
```xml
...
<commandlineArgs>versions:update-parent versions:update-properties versions:use-latest-releases -DallowMajorUpdates=false</commandlineArgs>
...
```
- Check the dodumentation of some "Optional Parameters": https://www.mojohaus.org/versions-maven-plugin/use-latest-releases-mojo.html


## License
Permission to modify and redistribute is granted under the terms of the Apache 2.0 license. See the [LICENSE] file for the full license.

[LICENSE]: https://github.com/remisbaima/dependabot-clone-with-maven/blob/main/LICENSE


## To contribute
Just create a PR :-)

Useful resources:
- https://www.mojohaus.org/versions-maven-plugin/usage.html
- https://www.mojohaus.org/exec-maven-plugin/usage.html
- https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
- https://maven.apache.org/pom.html

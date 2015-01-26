# ikvm-maven-plugin

# This plugin is a fork from samskivert/ikvm-maven-plugin.

This Maven plugin runs IKVM on a collection of Java jar files (defined by the
dependencies in the POM that includes this plugin).

The primary itch it scratches is to generate DLLs for use by any Windows System. This branch was changed to work fine without Mono (Open Source Framework for .NET applications: http://www.mono-project.com/).

It defines a `dll` packaging type and generates a `dll` artifact.

## Usage

One must configure their IKVM installation location in Maven's global settings
(`~/.m2/settings.xml`). For example:

    <profiles>
      <profile>
        <id>ikvm</id>
        <properties>
          <ikvm.path>${user.home}/projects/ikvm-monotouch</ikvm.path>
          <!-- ikvmc.path specifies where to find ikvmc.exe. It defaults to:
               ${ikvm.path}/bin/ikvmc.exe -->
          <!-- <ikvmc.path>/path/to/ikvmc.exe</ikvmc.path> -->
          <!-- dll.path specifies where to find the libraries.
               You can customize it to use a specific dll source path, for e.g. a Mono installation, or
               leave it pointing to a non-existent directory to locate standard
               libraries via the built-in library search path. -->
          <!-- <dll.path>/path/to/mono/usr/lib/x.x</dll.path> -->
        </properties>
      </profile>
    </profiles>
    <activeProfiles>
      <activeProfile>ikvm</activeProfile>
    </activeProfiles>

Once that's done, the following POM fragment demonstrates the use of this plugin:

    <?xml version="1.0" encoding="UTF-8"?>
    <project ...>
      <modelVersion>4.0.0</modelVersion>
      <groupId>foo</groupId>
      <artifactId>bar-ios</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>dll</packaging>

      <dependencies>
        <dependency>
          <groupId>foo</groupId>
          <artifactId>bar-core</artifactId>
          <version>${project.version}</version>
        </dependency>

        <dependency>
          <groupId>baz</groupId>
          <artifactId>bif</artifactId>
          <version>1.2</version>
        </dependency>
      </dependencies>

      <build>
      <!-- other stuff like: -->
      <directory>${project.basedir}/lib/ext/</directory>
      <finalName>MyNewIKVMGenerated.dll</finalName>
      <!-- end of other stuff -->
        <plugins>
          <plugin>
            <groupId>net.sblock</groupId>
            <artifactId>ikvm-maven-plugin</artifactId>
            <version>1.0</version>
            <!-- this lets Maven know that we define 'packaging: dll' -->
            <extensions>true</extensions>
            <configuration>
              <ikvmArgs>
                <ikvmArg>-debug</ikvmArg>
                <!-- versionnumber of the DLL-file to generate -->
                <ikvmArg>-version:1.1.5</ikvmArg>
              </ikvmArgs>
              <!-- these are additional referenced DLLs in special paths, etc. 
              Each of them is added with ikvmcs "-r" argument.-->
              <dlls>
                <dll>MyAdditionalDLL.dll</dll>
              </dlls>
            </configuration>
          </plugin>
        </plugins>
      </build>
    </project>

On Windows, the plugin will execute `ikvmc.exe` directly. On non-Windows
platforms you should use samskiverts plugin. 

## License

ikvm-maven-plugin is released under the New BSD License, which can be found in
the [LICENSE] file.

[LICENSE]: https://github.com/samskivert/ikvm-maven-plugin/blob/master/LICENSE

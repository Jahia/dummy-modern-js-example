## Dummy Modern JS Example

This is an example module used by the Jahia Academy

The following will guide you to the result of a basic setup which is this module.

Depending on your knowledge it might be good to read a bit about [Yarn](https://yarnpkg.com/en/docs/getting-started) to understand how to use it, as well as installing it on your env.

To start, add the [frontend-maven-plugin](https://github.com/eirslett/frontend-maven-plugin) (read the doc to know more) to your pom like this:
```xml
<plugin>
    <groupId>com.github.eirslett</groupId>
    <artifactId>frontend-maven-plugin</artifactId>
    <version>1.7.5</version>
    <executions>
        <execution>
            <id>npm install node and yarn</id>
            <phase>generate-resources</phase>
            <goals>
                <goal>install-node-and-yarn</goal>
            </goals>
            <configuration>
                <nodeVersion>v10.15.3</nodeVersion>
                <yarnVersion>v1.15.2</yarnVersion>
            </configuration>
        </execution>
        <execution>
            <id>yarn install</id>
            <phase>generate-resources</phase>
            <goals>
                <goal>yarn</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

In your `.gitignore` file (or VCS ignore file) add those line:
```
# JS
node
node_modules
```

From here, using a command line you can execute `mvn clean install`, the frontend-maven-plugin will be executed and will do a `yarn install` which will create a `yarn.lock` file.

Then execute `yarn init` (you might need to do `node\yarn\dist\bin\yarn init` if you did not install yarn globally on your environment) and answer the questions it will create a `package.json` file

The `package.json` and the `yarn.lock` must be committed to your VCS.

Add this execution to the plugin in the pom (at the end after the two other executions):
```xml
<execution>
    <id>yarn post-install</id>
    <phase>generate-resources</phase>
    <goals>
        <goal>yarn</goal>
    </goals>
    <configuration>
        <arguments>${yarn.arguments}</arguments>
    </configuration>
</execution>
```

In the properties of your pom, add something like that:
```xml
<properties>
    <yarn.arguments>build:production</yarn.arguments>
</properties>
```

The value of the property might change based on the name you will give it, but this is quite standard.

Now in the `package.json` add the script that will generate your files based on your need, in this example, I will use [UgilfyJS2](https://github.com/mishoo/UglifyJS2) to concatenate and compress the javascript files of my module but you could be using Webpack, Gulp, ...

So to add [UgilfyJS2](https://github.com/mishoo/UglifyJS2) as a `devDependencies` in the `package.json`:
`yarn add uglify-js -D`

And the script to add in the `package.json`:
```json
{
  "scripts": {
    "build": "npx uglifyjs src/javascript/app-utils.js src/javascript/app.js -o src/main/resources/javascript/app/index.js -b",
    "build:production": "npx uglifyjs src/javascript/app-utils.js src/javascript/app.js -o src/main/resources/javascript/app/index.js"
  }
}
```

You likely don't want to commit the result file in your module so you can add this to your VCS ignore file:
```
src/main/resources/javascript/app
```


With this basic setup, your files will be concatenated into one and the result file will be uglified if you are running `mvn clean install` so good for production and in dev you can run `mvn clean install -Dyarn.arguments=build` which will concatenate the files but not uglify them.


Little bonus you can add the following profile to your pom so in dev you can just run `mvn clean install -P dev`:
```xml
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <yarn.arguments>build</yarn.arguments>
        </properties>
    </profile>
</profiles>
```

Of course, this is a simple example, you can do so much more with Modern JS but it's not part of this topic and you can find all the tutorial and information you will need online.

在Maven项目中合并Cucumber JSON报告，有几种常用的方法。以下是两种最实用的解决方案：

方案一：使用cucumber-reporting插件（推荐）

1. 添加Maven插件配置

在pom.xml中添加：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>net.masterthought</groupId>
            <artifactId>maven-cucumber-reporting</artifactId>
            <version>5.8.0</version>
            <executions>
                <execution>
                    <id>generate-cucumber-reports</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <configuration>
                        <projectName>Your Project Name</projectName>
                        <outputDirectory>${project.build.directory}/cucumber-reports</outputDirectory>
                        <cucumberOutput>${project.build.directory}/cucumber-reports</cucumberOutput>
                        <jsonFiles>
                            <param>**/*.json</param>
                        </jsonFiles>
                        <mergeFeaturesById>false</mergeFeaturesById>
                        <mergeFeaturesWithRetest>false</mergeFeaturesWithRetest>
                        <parallelTesting>false</parallelTesting>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

2. 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>net.masterthought</groupId>
        <artifactId>cucumber-reporting</artifactId>
        <version>5.8.0</version>
    </dependency>
</dependencies>
```

方案二：自定义Java代码合并JSON报告

1. 创建JSON合并工具类

```java
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ArrayNode;
import com.fasterxml.jackson.databind.node.ObjectNode;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;

public class CucumberJsonMerger {
    
    private static final ObjectMapper mapper = new ObjectMapper();
    
    public static void main(String[] args) throws IOException {
        String reportsDirectory = "target/cucumber-reports";
        String outputFile = "target/merged-cucumber-report.json";
        
        mergeCucumberJsonReports(reportsDirectory, outputFile);
    }
    
    public static void mergeCucumberJsonReports(String inputDir, String outputFile) throws IOException {
        List<JsonNode> allReports = new ArrayList<>();
        
        // 读取所有JSON报告文件
        Files.walk(Paths.get(inputDir))
                .filter(path -> path.toString().endsWith(".json"))
                .forEach(path -> {
                    try {
                        JsonNode jsonNode = mapper.readTree(path.toFile());
                        if (jsonNode.isArray()) {
                            jsonNode.forEach(allReports::add);
                        } else {
                            allReports.add(jsonNode);
                        }
                    } catch (IOException e) {
                        System.err.println("Error reading file: " + path + ", " + e.getMessage());
                    }
                });
        
        // 合并报告
        ArrayNode mergedReport = mapper.createArrayNode();
        for (JsonNode report : allReports) {
            mergedReport.add(report);
        }
        
        // 写入合并后的文件
        try (FileOutputStream out = new FileOutputStream(outputFile)) {
            mapper.writerWithDefaultPrettyPrinter().writeValue(out, mergedReport);
        }
        
        System.out.println("Merged " + allReports.size() + " feature results into " + outputFile);
    }
}
```

2. 在Maven中配置执行

在pom.xml中添加exec-maven-plugin：

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>3.1.0</version>
    <executions>
        <execution>
            <id>merge-cucumber-reports</id>
            <phase>post-integration-test</phase>
            <goals>
                <goal>java</goal>
            </goals>
            <configuration>
                <mainClass>com.yourpackage.CucumberJsonMerger</mainClass>
            </configuration>
        </execution>
    </executions>
</plugin>
```

方案三：使用cucumber-reporting生成统一HTML报告

如果你主要需要查看测试报告，可以生成统一的HTML报告：

```xml
<plugin>
    <groupId>net.masterthought</groupId>
    <artifactId>maven-cucumber-reporting</artifactId>
    <version>5.8.0</version>
    <executions>
        <execution>
            <id>generate-cucumber-report</id>
            <phase>verify</phase>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <projectName>cucumber-jvm-example</projectName>
                <outputDirectory>${project.build.directory}/cucumber-reports/advanced-reports</outputDirectory>
                <jsonFiles>
                    <param>${project.build.directory}/cucumber-reports/*.json</param>
                </jsonFiles>
                <classificationFiles>
                    <param>sample.properties</param>
                </classificationFiles>
            </configuration>
        </execution>
    </executions>
</plugin>
```

完整的Maven配置示例

```xml
<build>
    <plugins>
        <!-- 运行Cucumber测试 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-failsafe-plugin</artifactId>
            <version>3.0.0-M5</version>
            <executions>
                <execution>
                    <goals>
                        <goal>integration-test</goal>
                        <goal>verify</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <includes>
                    <include>**/*Test.java</include>
                </includes>
            </configuration>
        </plugin>
        
        <!-- 合并JSON报告 -->
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>3.1.0</version>
            <executions>
                <execution>
                    <id>merge-cucumber-reports</id>
                    <phase>post-integration-test</phase>
                    <goals>
                        <goal>java</goal>
                    </goals>
                    <configuration>
                        <mainClass>com.yourpackage.CucumberJsonMerger</mainClass>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        
        <!-- 生成HTML报告 -->
        <plugin>
            <groupId>net.masterthought</groupId>
            <artifactId>maven-cucumber-reporting</artifactId>
            <version>5.8.0</version>
            <executions>
                <execution>
                    <id>generate-cucumber-report</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <configuration>
                        <projectName>Your Project</projectName>
                        <outputDirectory>${project.build.directory}/cucumber-reports</outputDirectory>
                        <jsonFiles>
                            <param>${project.build.directory}/merged-cucumber-report.json</param>
                        </jsonFiles>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

使用方法

1. 运行测试：mvn clean verify
2. 合并后的JSON报告将生成在：target/merged-cucumber-report.json
3. HTML报告将生成在：target/cucumber-reports

推荐使用方案一，因为它专门为Cucumber报告设计，能够正确处理各种场景和并发执行的结果合并。
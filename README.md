<build>
    <plugins>
        <!-- 1. JMeter 插件：只跑测试，不生成报告 -->
        <plugin>
            <groupId>com.lazerycode.jmeter</groupId>
            <artifactId>jmeter-maven-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <generateReports>false</generateReports> <!-- 关键：禁用插件报告 -->
                <jmeterVersion>5.6.2</jmeterVersion>      <!-- 与你本地验证通过的版本一致 -->
                <testResultsTimestamp>false</testResultsTimestamp>
                <propertiesJMeter>
                    <jmeter.save.saveservice.time>true</jmeter.save.saveservice.time>
                    <jmeter.save.saveservice.sample_start_time>true</jmeter.save.saveservice.sample_start_time>
                    <jmeter.save.saveservice.sample_end_time>true</jmeter.save.saveservice.sample_end_time>
                </propertiesJMeter>
            </configuration>
            <executions>
                <execution>
                    <id>run-jmeter</id>
                    <phase>integration-test</phase>
                    <goals><goal>jmeter</goal></goals>
                </execution>
            </executions>
        </plugin>

        <!-- 2. 核心：用 exec-maven-plugin 调用插件下载的 JMeter 官方 ReportGenerator -->
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>3.1.0</version>
            <executions>
                <execution>
                    <id>generate-official-report</id>
                    <phase>post-integration-test</phase>
                    <goals><goal>exec</goal></goals>
                    <configuration>
                        <executable>${project.build.directory}/jmeter/bin/jmeter</executable>
                        <arguments>
                            <argument>-g</argument>
                            <argument>${project.build.directory}/jmeter/results/*.jtl</argument>
                            <argument>-o</argument>
                            <argument>${project.build.directory}/jmeter/report</argument>
                        </arguments>
                        <workingDirectory>${project.basedir}</workingDirectory>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>

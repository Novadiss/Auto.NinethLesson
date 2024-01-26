# Обучение в Нетологии.
## Домашнее задание по курсу Автоматизированное тестирование
## Тема: Репортинг: Report Portal

    Настройка Allure, интегрированного с Selenide
    Настройка ReportPortal

## Инструкция по настройке Allure

1. В проекте в build.gradle прописать

    plugins{
    id'java'id'io.qameta.allure'version'2.8.1'
    }
    ...
    allure{
    version='2.16.1'//
    useJUnit5{version='2.16.1'//
    }
    }
    repositories{
    jcenter()
    mavenCentral()
    }

**Для запуска использовать команду**

gradlew clean test allureReport

gradlew allureServe

# Краткая инструкция по установке ReportPortal

1. Создать проект в IDEA на базе Gradle
2. build.gradle должен выглядеть как

    plugins {
    id 'java'
    id 'io.qameta.allure' version '2.9.6'
    }
    
    group 'ru.netology'
    version '1.0-SNAPSHOT'
    
    sourceCompatibility = 11
    
    compileJava.options.encoding = "UTF-8"
    compileTestJava.options.encoding = "UTF-8"
    
    repositories {
    mavenLocal()
    mavenCentral()
    jcenter()
    }
    
    allure {
    version '2.16.1'
    useJUnit5 {
    version = '2.16.1'
    }
    }
    
    sourceCompatibility = 1.8
    targetCompatibility = 1.8
    
    dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.6.1'
    testImplementation 'com.codeborne:selenide:6.17.1'
    testImplementation 'com.github.javafaker:javafaker:1.0.2'
    testCompileOnly 'org.projectlombok:lombok:1.18.28'
    testAnnotationProcessor 'org.projectlombok:lombok:1.18.28'
    testRuntimeOnly 'org.slf4j:slf4j-simple:2.0.3'
    testImplementation 'io.qameta.allure:allure-selenide:2.13.3'
    implementation 'com.epam.reportportal:logger-java-log4j:5.1.4'
    implementation 'org.apache.logging.log4j:log4j-api:2.17.1'
    implementation 'org.apache.logging.log4j:log4j-core:2.17.1'
    implementation 'com.epam.reportportal:agent-java-junit5:5.2.0'
    }
    
    test {
    testLogging.showStandardStreams = true
    useJUnitPlatform()
    systemProperty 'selenide.headless', System.getProperty('selenide.headless')
    systemProperty 'junit.jupiter.extensions.autodetection.enabled', true
    }

(В данном файле также интегрирован selenide и allure)

3. Создать файл docker-compose.yml и скопировать файл из [docker-compose.yml для windows](https://github.com/reportportal/reportportal/blob/master/docker-compose.yml)
4. Раскомментировать строки 'for windows host' и закомментировать 'for unix host'

    volumes:
    # For windows host
    - postgres:/var/lib/postgresql/data
    # For unix host
    # - ./data/postgres:/var/lib/postgresql/data

5. Раскомментировать и закомментировать строки

    volumes:
      ## For unix host
      # - storage:/data
      ## For windows host
       - minio:/data

6. Создать minio: в 

    volumes:
    elasticsearch:
    storage:
    postgres:
    minio:

7. Создать папку /META-INF/services в resources
8. Положить туда файл, названный org.junit.jupiter.api.extension.Extension
9. В данный файл прописать имплементацию одной строкой

    com.epam.reportportal.junit5.ReportPortalExtension

10. Создать файл log4j2.xml file в папке resources и прописать

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration status="WARN">
       <Appenders>
           <Console name="ConsoleAppender" target="SYSTEM_OUT">
               <PatternLayout
                       pattern="%d [%t] %-5level %logger{36} - %msg%n%throwable"/>
           </Console>
           <ReportPortalLog4j2Appender name="ReportPortalAppender">
               <PatternLayout
                       pattern="%d [%t] %-5level %logger{36} - %msg%n%throwable"/>
           </ReportPortalLog4j2Appender>
       </Appenders>
       <Loggers>
           <Root level="DEBUG">
               <AppenderRef ref="ConsoleAppender"/>
               <AppenderRef ref="ReportPortalAppender"/>
           </Root>
       </Loggers>
    </Configuration>


11. Для загрузки и запуска ReportPortal прописать в терминале команду

    docker-compose -p reportportal up -d --force-recreate

12. После того, как все файлы и контейнеры загрузятся открыть в браузере http://localhost:8080
13. Необходимо залогиниться в качестве админа, используя данные с официального сайта

    Логин superadmin
    Пароль erebus

14. Далее добавляем пользователя в проект по шагам, открывая вкладки:

    Administrative -> My Test Project -> Members -> Add user


15. Вводим имя и пароль для нового пользователя.
16. Необходимо перелогиниться под только что созданным пользователем.
17. Нажать на иконку пользователя(user) и выбрать Profile
18. Во вкладке Configuration Examples будет пример файла reportportal.properties для данного пользователя.
19. Создать в проекте в IDEA также в папке resources файл reportportal.properties
20. Скопировать данные из Configuration Examples в данный файл в IDEA
21. Создать в папке resources файл junit-platform.properties и добавить в него строку:

    junit.jupiter.extensions.autodetection.enabled=true

22. После того, как JUnit подключился к ReportPortal нужно запустить приложение и запустить тесты.
23. На странице с ReportPortal слева нажать на вкладку Launches, после чего справа появится название launches эквивалентное указанному в файле reportportal.properties
24. Нажав на нее, появится список тестов. Если нажать на каждый из них, то можно увидеть отчеты и логи.

# Для запуска проекта:

1. Склонировать проект из репозитория командой

    *git clone https://github.com/Novadiss/Auto.NinethLesson.git*

2.  Открыть склонированный проект в Intellij IDEA
3. Открыть в терминале каталог artifacts
4. Для запуска приложения ввести команду java -jar app-card-delivery.jar
5. Запустить команду gradlew test
6. Открыть инструкцию выше и следовать шагам 23-24

# Gradle vs Maven

## Maven
2004년에 첫 출시된 Maven(메이븐)은 소프트웨어 개발을 위한 빌드 자동화 도구다. Apache Jakarta 프로젝트 중 하나였던 Apache Turbine 프로젝트(Java 개발자가 웹 애플리케이션을 빠르게 구축할 수 있도록 하는 서블릿 기반 프레임워크)에서 빌드 프로세스를 단순화하려는 시도로 시작되었다. 따라서 Maven 프로젝트는 아파치 소프트웨어 재단에서 관리하고 있다.  

### Maven의 저장소
Maven 커뮤니티는 [중앙 저장소(Central Repository)](https://mvnrepository.com/)를 제공한다. 이 저장소에는 일반적으로 많이 사용되는 라이브러리들이 들어있다. 저장소에서 관리되는 라이브러리와 플러그인 등을 아티팩트라고 부른다. Maven은 기본적으로 빌드를 실행하면 모든 아티팩트 종속성 jar를 로컬 저장소(빌드가 실행되는 컴퓨터의 폴더 위치, 사용자 지정 가능)에 자동으로 다운로드한다. 그래서 프로젝트를 빌드할 때 로컬 저장소의 종속성을 활용하여 외부 시스템에 접속하는 시간을 최대한 줄인다. 만약 로컬 저장소에서 아티팩트의 종속성을 찾지 못하면 인터넷을 통해 중앙 저장소에 접속하여 종속성을 검색한다. 중앙 저장소뿐만 아니라 사용자 정의 저장소(원격 저장소, Remote Repository)에도 접근할 수 있다.  

정리해보자면 Maven은 빌드 명령이 실행될 때 다음의 순서로 종속성 라이브러리를 검색한다.
1. 로컬 저장소에서 종속성을 찾는다. 
2. 중앙 저장소에서 종속성을 찾는다.
3. 원격 저장소가 설정되지 않은 경우 오류가 발생한다.
4. 원격 저장소에서 종속성을 찾는다. 원격 저장소에서 종속성을 발견하면 나중에 참조할 수 있도록 로컬 저장소에 다운로드한다.
5. 모든 곳에서 종속성을 찾을 수 없다면 오류가 발생한다.

### Maven의 빌드 설정
Maven이 빌드를 지원하는 언어에는 Java, C#, Ruby, Scala가 있다. Maven은 Apache Ant와 같은 이전 도구와 다르게 빌드 절차에 대한 규칙을 사용한다. 이 때 POM(Project Object Model)을 사용해서 설정한다. 스프링 프로젝트를 만들면 자동으로 생성되는 pom.xml이 바로 POM이다. 이 POM은 프로젝트마다 한 개씩 존재해야 하며, 빌드하려는 프로젝트의 groupId, artifactId, version을 필수로 작성해야 한다. 그 밖에도 외부 종속성, 빌드 순서, 디렉토리 등 프로젝트의 세부 빌드 정보를 설정할 수 있다.   

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.6</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>11</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<excludes>
						<exclude>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
						</exclude>
					</excludes>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>
```

- \<dependencies> : 프로젝트가 의존하는 아티팩트들
- \<dependency> : 의존할 아티팩트
- \<project> : Maven의 XML 네임스페이스 지정
- \<modelVersion> : Maven의 model Version
- \<groupId> : 그룹의 ID 
- \<artifactId> : 아티팩트의 ID 
- \<version> : 아티팩트의 버전
- \<packaging> : 패키징 형식(jar, war)
- \<name> : 프로젝트 이름
- \<url> : Maven의 url
- \<properties> : 프로젝트 관련 속성
- \<parent> : pom.xml의 상속에 관련된 태그
- \<scope> : 아티팩트가 프로젝트에서 사용되는 범위

## Gradle
2008년 첫 출시된 Gradle(그레이들)은 소프트웨어 개발을 위한 빌드 자동화 도구다. 지원하는 언어에는 Java, Kotlin, Groovy, Scala, C/C++, JavaScript 등이 있다. Apache Ant와 Apache Maven의 개념을 기반으로 하지만 Maven의 XML 기반 구성과 다르게 Groovy 또는 Kotlin 기반으로 구성할 수 있는 것이 특징이다. Apache Groovy와 Kotlin은 모두 JVM 바이트코드로 컴파일되기 때문에 Gradle 또한 JVM에서 실행된다.   

Gradle은 빌드를 작업 단위의 방향성 비순환 그래프(Directed Acyclic Graphs, DAG)로 모델링한다. 이는 빌드가 기본적으로 작업 세트를 구성하고 종속성에 따라 연결되어서 그래프를 만든다는 것이다. 작업 그래프가 생성되면 Gradle은 어떤 작업을 어떤 순서로 실행해야 하는지를 결정한 다음 빌드를 진행한다.  
### Gradle의 빌드 단계
Gradle의 빌드는 3단계로 이루어진다. 참고로 Gradle의 빌드 단계는 Maven의 단계와 다르다. Maven은 기본적으로 빌드를 여러 단계(스텝)으로 나누기 때문에 작업 그래프보다는 덜 유연하다.
1. 초기화 : 빌드를 위한 환경을 설정한다.
2. 구성 : 빌드를 위한 작업 그래프를 구성한 다음, 사용자가 실행하려는 작업을 기반으로 순서를 결정한다.
3. 실행 : 선택한 작업을 실행한다.

Gradle과 Maven 모두 리포지토리에서 종속성을 받아온다. 둘 다 종속성을 로컬로 캐싱하고 병렬로 다운로드할 수 있다. 하지만 Gradle은 Maven보다 빌드 과정을 더 유연하게 설정할 수 있다. 대표적으로 Task.doFirst(), Task.doLast() 메서드를 통해 작업 전후에 사용자 지정 빌드 로직을 연결할 수 있다. 또한, Gradle은 Maven보다 빌드 속도가 더 빠르다. Gradle이 거의 모든 시나리오에서 Maven보다 최소 2배 이상 빠르다. Gradle은 작업의 입출력을 추적하고 필요한 것만 실행하고 가능한 경우 변경된 파일만 처리하여 빌드 과정을 최적화한다. 또한, 빌드 캐시가 있어서 동일한 입력이 들어왔을 때 다른 Gradle 빌드의 빌드 출력을 재사용한다. Gradle은 빌드를 디버깅하고 최적화하기 위한 대화형 웹 기반 UI인 Build Scan이라는 것도 제공한다. 이러한 이점으로 Google은 Android용 공식 빌드 도구로 Gradle을 선택했다.

### Gradle의 빌드 설정
```groovy
plugins {
	id 'org.springframework.boot' version '2.6.6'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

## 참고
https://en.wikipedia.org/wiki/Apache_Maven  
https://en.wikipedia.org/wiki/Gradle  
https://docs.gradle.org/current/userguide/userguide.html  
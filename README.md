# springboot-upgrade-skill

## Goal
Upgrade project with Springboot 2/3 to Springboot 4.1.0. The work flow looks like:
2.x -> latest 3.5.x
latest 3.5.x -> latest 4.0.x
latest 4.0.x -> 4.1.0
Do it step by step no one big jump

## Context & Background
Depend on the start version of Springboot, upgrade from version 2 to version 4 is a bit harder. But the common practice is the same. If the project is using java 1.8 on Springboot 2.X, We need to first upgrade the JDK. Stop working in this case.
If idk used is 17 or above, we can proceed.

## Known Knowledge and Rules
### Update the Spring Boot parent or BOM first, but execute the upgrade in staged hops rather than a blind single jump.
#### pom.xml 
- if project parent is springboot, change parent to
   <parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>4.1.1</version>
		<relativePath/>
	</parent>
- if project use bom, update box to 4.1.1
- Replace direct javax.validation dependencies with spring-boot-starter-validation when needed.
- If project has tomcat dependency, remove it
- If project has spring-data-jdbc dependency, replace it with spring-boot-starter-data-jpa
- if the project is assembled as war
    <packaging>jar</packaging>
  change to:
    <packaging>jar</packaging>
- Remove all Junit related dependency, simply add this dependency:
     <dependency>
  			<groupId>org.springframework.boot</groupId>
  			<artifactId>spring-boot-starter-test</artifactId>
  			<scope>test</scope>
      </dependency>
- Remove Hibernate dependency simply add this dependency:
    <dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
- If there is dependency as spring-boot-starter-web, change it to spring-boot-starter-webmvc
- If Lombok is used in project, make sure use version 1.18.44
   check this kind of dependency to see if Lombok is used:
      <groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
 if Lombok is used, align Lombok dependency and annotation processor versions in maven-compiler-plugin as well
- If RestTemplate is as dependency, replace it with restClient, do not rewrite implementation to WebClient
- If there is swagger related dependency replace it with:
    <dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>3.0.3</version>
		</dependency>
- Mysql driver upgrade:
  replace mysql-connector-j dependency with:
    <dependency>
			<groupId>com.mysql</groupId>
			<artifactId>mysql-connector-j</artifactId>
			<version>26.7.0</version>
			<scope>compile</scope>
		</dependency>
- If there is aws secrete dependency exist and use old Spring Cloud AWS package, it will cause issue, use io.awspring.cloud
    
#### For Java code
- javax change to Jakarta:
  Search in Java code, replace javax package with Jakarta package:
	- import javax.persistence -> import jakarta.persistence
  	- import javax.annotation -> import jakarta.annotation
  	- import javax.servlet -> import jakarta.servlet
  	- import javax.validation -> import jakarta.validation
- Jackson Package change:
  Search in Java code, replace below:
    - import com.fasterxml.jackson.databind.ObjectMapper -> import tools.jackson.databind.ObjectMapper
    - import com.fasterxml.jackson.core.type.TypeReference -> import tools.jackson.core.type.TypeReference;    
    - import com.fasterxml.jackson.databind.DeserializationFeature; ->  import tools.jackson.databind.DeserializationFeature;
    - import com.fasterxml.jackson.databind.JsonDeserializer; ->  import tools.jackson.databind.ValueDeserializer;
    - import com.fasterxml.jackson.databind.ObjectMapper; -> import tools.jackson.databind.ObjectMapper;
    - import com.fasterxml.jackson.databind.module.SimpleModule; -> import tools.jackson.databind.module.SimpleModule;
    - import com.fasterxml.jackson.core.JsonProcessingException; -> import tools.jackson.core.JacksonException;
    - import com.fasterxml.jackson.databind.JsonNode; -> import tools.jackson.databind.JsonNode;
    - import com.fasterxml.jackson.core.JsonPointer; -> import tools.jackson.core.JsonPointer;
    - import com.fasterxml.jackson.core.type.TypeReference; -> import tools.jackson.core.type.TypeReference;
    - import com.fasterxml.jackson.annotation.JsonInclude; -> import tools.jackson.annotation.JsonInclude;
    - import com.fasterxml.jackson.core.JsonParser; -> import tools.jackson.core.JsonParser;
    - import com.fasterxml.jackson.databind.SerializationFeature; -> import tools.jackson.databind.SerializationFeature;    
    - import com.fasterxml.jackson.annotation.JsonIgnoreProperties; -> No change
	If see this: import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule; delete it and remove JavaTimeModule setting in code, Jackson 3 default use ISO-8601 this is not needed.
    Below is a quick summary:
      - Keep: com.fasterxml.jackson.annotation.* (e.g., @JsonProperty, @JsonIgnore, @JsonIgnoreProperties)
      - Change: com.fasterxml.jackson.databind.* → tools.jackson.databind.*
      - Change: com.fasterxml.jackson.core.* → tools.jackson.core.*
- JPA mapping in Springboot 4 is more strict
  	- If database column is defined a Timestamp, the entity mapping must be LocalDateTime, Temporal is out of date. Re-engineer it to use LocalDateTime for mapping object
  	- The mapping member element in POJO need to have exact data type as defined in DB, ex, if it DB column define a datetime, mapping object need to be LocalDateTime; if DB column defined as date, mapping object need to be LocalDate; The also applicable for the parameter parsed to repository methods.
  	- If found in native query there is datetime result set refer as date, cast DATE(COLUME_NAME) to convert in query immediately
- Junit 4 upgrade to Junit 5/6
- If Spring Security is used, refactor WebSecurityConfigurerAdapter to bean-based SecurityFilterChain configuration.

### Official Springboot4 Guardrails
#### Do not jump directly from an old 2.x service to 4.1.0 in a single unstructured change set. Use staged hops through the latest 3.5.x and then the latest 4.0.x first.
#### Add spring-boot-properties-migrator temporarily during the 4.0 hop so property renames surface clearly at startup.
#### Spring Boot 4 requires Spring Framework 7, Jakarta EE 11, and a Servlet 6.1 baseline.
#### Embedded Undertow support is dropped in Boot 4.
#### Review Boot 4 starter changes and dedicated test starters before keeping old dependency declarations.
#### Review package changes around bootstrapping internals such as BootstrapRegistry and EnvironmentPostProcessor if the repo uses them.

### Working Assumptions
#### Prefer the repo’s existing architecture and business behavior.
#### Make the smallest set of dependency, import, config, and API changes needed to restore green build and runtime correctness.
#### Escalate uncertain third-party library compatibility instead of guessing version numbers.
#### Treat user-provided migration examples as stronger evidence than generic framework advice when both cannot be kept exactly as written.
#### For risky implementations, bracket the risky code with the exact review markers == RISK START == and == RISK END == using the native comment syntax of the file so human review can focus there.

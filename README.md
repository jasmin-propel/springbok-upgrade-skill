# springboot-upgrade-skill

## Goal: 
Upgrade project with Springboot 2/3 to Springboot 4.1.0

## Context & Background
Depend on the start version of Springboot, upgrade from version 2 to version 4 is a bit harder. But the common practice is the same. If the project is using java 1.8 on Springboot 2.X, We need to first upgrade the JDK. Stop working in this case.
If idk used is 17 or above, we can proceed.

## Known Action
### pom.xml 
- if project parent is springboot, change parent to
   <parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>4.1.1</version>
		<relativePath/>
	</parent>
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
    
### For Java code
- javax change to Jakarta:
  Search in Java code, replace javax package with Jakarta package:
  import javax.persistence -> import jakarta.persistence
  import javax.annotation -> import jakarta.annotation
  import javax.servlet -> import jakarta.servlet
  import javax.validation -> import jakarta.validation
- Jackson Package change:
  Search in Java code, replace below:
    import com.fasterxml.jackson.databind.ObjectMapper -> import tools.jackson.databind.ObjectMapper
    import com.fasterxml.jackson.core.type.TypeReference -> import tools.jackson.core.type.TypeReference;    
    import com.fasterxml.jackson.databind.DeserializationFeature; ->  import tools.jackson.databind.DeserializationFeature;
    import com.fasterxml.jackson.databind.JsonDeserializer; ->  import tools.jackson.databind.ValueDeserializer;
    import com.fasterxml.jackson.databind.ObjectMapper; -> import tools.jackson.databind.ObjectMapper;
    import com.fasterxml.jackson.databind.module.SimpleModule; -> import tools.jackson.databind.module.SimpleModule;
    import com.fasterxml.jackson.core.JsonProcessingException; -> import tools.jackson.core.JacksonException;
    import com.fasterxml.jackson.databind.JsonNode; -> import tools.jackson.databind.JsonNode;
    import com.fasterxml.jackson.core.JsonPointer; -> import tools.jackson.core.JsonPointer;
    import com.fasterxml.jackson.core.type.TypeReference; -> import tools.jackson.core.type.TypeReference;
    import com.fasterxml.jackson.annotation.JsonInclude; -> import tools.jackson.annotation.JsonInclude;
    import com.fasterxml.jackson.core.JsonParser; -> import tools.jackson.core.JsonParser;
    import com.fasterxml.jackson.databind.SerializationFeature; -> import tools.jackson.databind.SerializationFeature;    
    import com.fasterxml.jackson.annotation.JsonIgnoreProperties; -> No change
    If see this: import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule; delete it and remove JavaTimeModule setting in code, Jackson 3 default use ISO-8601 this is not needed.
    Below is a quick summary:
      Keep: com.fasterxml.jackson.annotation.* (e.g., @JsonProperty, @JsonIgnore, @JsonIgnoreProperties)
      Change: com.fasterxml.jackson.databind.* → tools.jackson.databind.*
      Change: com.fasterxml.jackson.core.* → tools.jackson.core.*
- JPA mapping in Springboot 4 is more strict
  	- If database column is defined a Timestamp, the entity mapping must be LocalDateTime, Temporal is out of date. Re-engineer it to use LocalDateTime for mapping object
  	- The mapping member element in POJO need to have exact data type as defined in DB, ex, if it DB column define a datetime, mapping object need to be LocalDateTime; if DB column defined as date, mapping object need to be LocalDate; The also applicable for the parameter parsed to repository methods.
  	-  
- Junit 4 upgrade to Junit 5
- 

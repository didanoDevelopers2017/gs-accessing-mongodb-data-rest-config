# gs-accessing-mongodb-data-rest-config
this demo show create a mongodb db mydb,and the mongo db config with springboot

1，get spring demo "Accessing MongoDB Data with REST"
https://spring.io/guides/gs/accessing-mongodb-data-rest/

git:
https://github.com/spring-guides/gs-accessing-mongodb-data-rest.git

check with
$ curl -i -X POST -H "Content-Type:application/json" -d "{  \"firstName\" : \"Frodo\",  \"lastName\" : \"Baggins\" }" http://localhost:8080/people

2，springboot with simple mongo db config

2.1,boot mongodb without auth,create db mydb

mongod

use mydb

2.2,create user

db.createUser( { "user" : "myuser",
                 "pwd": "mypassword",
                 "roles" : [ "readWrite"
                             ] },
               { w: "majority" , wtimeout: 5000 } )
	       
2.3,check connection with robotMongo(a mongodb client),and shut down mongodb

ps -ef|grep mongo

kill -9 XXX

2.4，boot mongodb with auth,and check connection again

mongod --auth

2.5,set application.properties

spring.data.mongodb.database=mydb

spring.data.mongodb.repositories.enabled=true

spring.data.mongodb.uri=mongodb://myuser:mypassword@localhost:27017/mydb

2.6,add SpringMongoConfig

package hello;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.config.AbstractMongoConfiguration;
import org.springframework.data.mongodb.core.mapping.MongoMappingContext;
import com.mongodb.Mongo;
import com.mongodb.MongoClient;
import com.mongodb.MongoClientURI;
@Configuration
public class SpringMongoConfig extends AbstractMongoConfiguration {

    @Value("${spring.data.mongodb.database}")
    private String mongoDB;

	@Value("${spring.data.mongodb.uri}")
    private String uri;

    @Override
    public MongoMappingContext mongoMappingContext()
        throws ClassNotFoundException {
        // TODO Auto-generated method stub
        return super.mongoMappingContext();
    }

    @Override
    @Bean
    public Mongo mongo() throws Exception {
        return new MongoClient(new MongoClientURI(uri));
    }

    @Override
    protected String getDatabaseName() {
        // TODO Auto-generated method stub
        return mongoDB;
    }
}

2.7,push rest data ,and check mongodb

$ curl -i -X POST -H "Content-Type:application/json" -d "{  \"firstName\" : \"Frodo\",  \"lastName\" : \"Baggins\" }" http://localhost:8080/people


# Daily Learning Sheet


## Spring Security
- org.springframework.security.web.access.intercept has AuthorizationFilter class:
```java
public class AuthorizationFilter extends GenericFilterBean{}
```

## Accessing elements in DOM using vanilla javascript
- document.querySelectorAll('.step') will return a `NodeList` which is an array of all html elements that has class `step`.

## Animation in CSS
- `animation-fill-mode` and `transition` in CSS

## Flexbox task in CSS
- You are given a container using a CSS flexbox layout that has four items (A1, A2, A3, and A4). By default, all items are placed at the end of the flexbox. Your task is to adjust the CSS in such a way that the first item A1 is aligned to the left end of the flexbox, while the other items (A2, A3, and A4) remain aligned to the right end.

```html
<div style="display:flex;justify-content:end;background-color:lightgray;width:100%">
  <div>A1</div>
  <div style="margin-left:auto">A2</div>
  <div>A3</div>
  <div>A4</div>
</div>
```

## Bridge Adapter in Virtualbox

A `bridge adapter` in VirtualBox refers to a network configuration that directly connects a `virtual machine's` network adapter to the `host computer's` physical network adapter, essentially allowing the virtual machine to act like a separate physical device on the same network, receiving an IP address from the same DHCP server as other devices on the local network; essentially making the virtual machine appear as a "real" device on the network. 


## Referential Equality in React

Referential equality refers to whether two variables reference the exact same object or array in memory. In JavaScript (and React), objects and arrays are compared by reference, not by their content. This behavior directly impacts React's rendering process, as it determines if components should re-render based on changes in props or state.

Two objects or arrays are considered referentially equal if they point to the same memory location. If a new object or array is created (even with identical content), it is not referentially equal to the previous one.

React uses referential equality to decide if a component should re-render:

- If a parent passes a new object or array to a child (even if it looks the same), the child re-renders because React sees the prop as "different."
- This is why memoizing objects/arrays or their derived values with hooks like useMemo is critical for optimization.

### Without Referential Equality

```javascript
function ParentComponent() {
  const [count, setCount] = React.useState(0);

  // New object created on every render
  const settings = { theme: "dark" };

  return (
    <div>
      <ChildComponent settings={settings} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

function ChildComponent({ settings }) {
  console.log("ChildComponent Rendered");
  return <p>Theme: {settings.theme}</p>;
}
```

Every time the parent re-renders, the settings object is recreated, so ChildComponent re-renders—even though the settings content hasn't changed.

In the code example, `ChildComponent` re-renders because the settings object is being re-created on every render of `ParentComponent`. This happens even though the content of settings (i.e., { theme: "dark" }) does not change.

#### New Object on Every Render
In JavaScript, objects are compared by reference, not by value. When ParentComponent renders, the settings object is re-created as a new object in memory every time, even if its contents are the same. This causes React to detect a new prop for ChildComponent, triggering its re-render.

#### React's Behavior
React does a shallow comparison of props when deciding whether to re-render a child component. Since the reference to the settings object has changed, React assumes the prop has changed, and thus ChildComponent re-renders.

### Fixing with Referential Equality

To prevent ChildComponent from re-rendering unnecessarily, you can use the useMemo hook to memoize the settings object:

```javascript
function ParentComponent() {
  const [count, setCount] = React.useState(0);

  // Memoize the settings object
  const settings = React.useMemo(() => ({ theme: "dark" }), []);

  return (
    <div>
      <ChildComponent settings={settings} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```
#### Alternate Solution

You can also use React.memo to prevent ChildComponent from re-rendering if its props haven’t changed:

```javascript
const ChildComponent = React.memo(function ChildComponent({ settings }) {
  console.log("ChildComponent Rendered");
  return <p>Theme: {settings.theme}</p>;
});
```

This approach works because `React.memo` wraps the component and ensures it only re-renders if its props have changed. However, without `useMemo`, `React.memo` would still detect a change in settings due to the new object reference.

### Where Referential Equality Matters
- Props: Passing new object/array references causes children to re-render.
- Context: Context value updates trigger re-renders in all consumers unless memoized.
- Dependencies in Hooks: Incorrectly referencing objects/arrays in dependency arrays can lead to unintended effects.

### Key Points
- Primitives (e.g., strings, numbers): Compared by value. No issue with referential equality.
- Objects/Arrays: Compared by reference. Can trigger unnecessary re-renders if not memoized.
- Use tools like React.memo, useMemo, and useCallback to maintain referential equality and


## Subsequent-sibling combinator

The subsequent-sibling combinator (~, a tilde) separates two selectors and matches all instances of the second element that follow the first element (not necessarily immediately) and share the same parent element.

In the following example, the subsequent-sibling combinator (~) helps to select and style paragraphs that are both siblings of an image and appear after any image.

```css
img ~ p {
  color: red;
}
```

## ConcurrentHashMap in Java

The ConcurrentHashMap class is introduced in JDK 1.5 belongs to java.util.concurrent package, which implements ConcurrentMap as well as to Serializable interface also. ConcurrentHashMap is an enhancement of HashMap as we know that while dealing with Threads in our application HashMap is not a good choice because performance-wise HashMap is not up to the mark.

ConcurrentHashMap is a thread-safe implementation of the Map interface in Java, which means multiple threads can access it simultaneously without any synchronization issues. It’s part of the java.util.concurrent package and was introduced in Java 5 as a scalable alternative to the traditional HashMap class.

One of the key features of the ConcurrentHashMap is that it provides fine-grained locking, meaning that it locks only the portion of the map being modified, rather than the entire map. This makes it highly scalable and efficient for concurrent operations. Additionally, the ConcurrentHashMap provides various methods for atomic operations such as putIfAbsent(), replace(), and remove().

## Advanced Hibernate Mappings

### 1. One-to-One Unidirectional and One-to-One Bidirectional

This example demonstrates a relationship between two entities: `User` and `UserProfile`

```java
import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String username;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)
    private UserProfile userProfile;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public UserProfile getUserProfile() {
        return userProfile;
    }

    public void setUserProfile(UserProfile userProfile) {
        this.userProfile = userProfile;
        userProfile.setUser(this); // For bidirectional relationship
    }
}
```

```java
import jakarta.persistence.*;

@Entity
@Table(name = "user_profiles")
public class UserProfile {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String address;

    @OneToOne
    @JoinColumn(name = "user_id", referencedColumnName = "id")
    private User user;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
}
```

```java

import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import jakarta.transaction.Transactional;

@Service
public class UserService {

    @PersistenceContext
    private EntityManager entityManager;

    @Transactional
    public void createUserWithProfile() {
        // Create a new User
        User user = new User();
        user.setUsername("john_doe");

        // Create a new UserProfile
        UserProfile profile = new UserProfile();
        profile.setAddress("123 Main Street");

        // Set bi-directional relationship
        user.setUserProfile(profile);

        // Persist User (UserProfile will be cascaded)
        entityManager.persist(user);

        System.out.println("User and UserProfile saved to the database!");
    }

    public void fetchUserWithProfile() {
        // Fetch User by ID
        User user = entityManager.find(User.class, 1L);

        if (user != null) {
            System.out.println("User ID: " + user.getId());
            System.out.println("Username: " + user.getUsername());

            UserProfile profile = user.getUserProfile();
            if (profile != null) {
                System.out.println("Profile ID: " + profile.getId());
                System.out.println("Address: " + profile.getAddress());
            }
        } else {
            System.out.println("User not found!");
        }
    }
}
```

## Marker Interface and Annotation in Java
A marker interface in Java is an interface that doesn’t declare any methods or fields. So then why do we need it? Can’t they be replaced by Annotations?

`The concept of a marker interface in Java has historically been useful. Marker interfaces were more common in earlier Java versions. They provided a simple way to signal to the compiler or runtime that a class had a specific property. Classic examples include `Serializable` and `Cloneable`.`

### How to Recognize a Marker Interface :
- No Methods or Fields: Marker interfaces don’t contain any methods or fields. They’re just used to signal or mark something.
- Type Tagging: They are used to provide metadata about the class that implements them. For example, they can be used to indicate that a class should be treated in a special way by the framework or runtime.
- Checked by Frameworks: Frameworks or libraries check if a class implements a marker interface to apply specific logic or behavior.

### Real Life Example from a Developer POV—

Custom Marker Interface:

Imagine you’re developing a Java application where certain classes should be processed differently based on some criteria. For example, you might want to perform a special type of logging for classes that implement a particular marker interface.

```java
// Marker Interface
public interface Loggable {
}

// Classes that implement the marker interface
public class User implements Loggable {
    private String username;

    public User(String username) {
        this.username = username;
    }

    @Override
    public String toString() {
        return "User{username='" + username + "'}";
    }
}

public class Product {
    private String productName;

    public Product(String productName) {
        this.productName = productName;
    }

    @Override
    public String toString() {
        return "Product{productName='" + productName + "'}";
    }
}

// Logging Utility
public class Logger {
    public static void log(Object obj) {
        if (obj instanceof Loggable) {
            System.out.println("Logging: " + obj);
        } else {
            System.out.println("Not Loggable: " + obj);
        }
    }

    public static void main(String[] args) {
        User user = new User("Alice");
        Product product = new Product("Laptop");

        log(user);     // Should print "Logging: User{username='Alice'}"
        log(product);  // Should print "Not Loggable: Product{productName='Laptop'}"
    }
}
```

In this example:
- `Loggable` is a marker interface.
- `User` implements `Loggable`, while `Product` does not.
- The `Logger` utility class checks if an object is an instance of `Loggable` and logs it accordingly.



## Non-Null Assertion Operator in Typescript

In typescript, the ! operator after an expression tells the compiler that you are certain the value is not null or undefined. This is useful when working with elements like `document.getElementById()` because typescript returns a type of `HTMLElement | null`, meaning the element may or may not exist in DOM.

```typescript
document.getElementById('someId')!.style.display = 'none';
```

Here, you're asserting to TypeScript that the element with ID `someId` definitely exists (i.e., it is not null) and therefore, you can safely access its properties such as style.

## TypedQuery in Spring Data JPA

`TypedQuery` is part of the `Jakarta Persistence (JPA)` API. It allows developers to write type-safe queries when interacting with the database. Unlike `Query`, which works with raw types, `TypedQuery` helps prevent potential `ClassCastException` by enforcing type-safety at compile time.

```java
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Employee {
    @Id
    private Long id;
    private String name;
    private String department;

    // Getters and Setters
}
```

```java
import jakarta.persistence.EntityManager;
import jakarta.persistence.EntityManagerFactory;
import jakarta.persistence.Persistence;
import jakarta.persistence.TypedQuery;
import java.util.List;

public class TypedQueryExample {
    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("my-persistence-unit");
        EntityManager em = emf.createEntityManager();

        try {
            // Start a transaction
            em.getTransaction().begin();

            // JPQL query with TypedQuery
            String jpql = "SELECT e FROM Employee e WHERE e.department = :department";
            TypedQuery<Employee> query = em.createQuery(jpql, Employee.class);

            // Set the parameter
            query.setParameter("department", "IT");

            // Execute the query and get the result list
            List<Employee> employees = query.getResultList();

            for (Employee emp : employees) {
                System.out.println("Employee Name: " + emp.getName());
            }

            // Commit the transaction
            em.getTransaction().commit();
        } finally {
            em.close();
            emf.close();
        }
    }
}
```

 Use `setMaxResults()` and `setFirstResult()` for pagination.

 ## java.sql.SQLIntegrityConstraintViolationException is a checked exception.

## How does `orphanRrmoval` work with JPA and Hibernate ?

https://vladmihalcea.com/orphanremoval-jpa-hibernate/

## How to temporairly disable foreign key constraint in MYSQL

```sql
SET FOREIGN_KEY_CHECKS = 0;
-- DO YOUR STUFF
SET FOREIGN_KEY_CHECKS=1;
```

## JPQL Joins, CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP

DATE_ADD() and DATE_SUB() are not supported by JPQL.

## Curse of Dimensionality in Machine Learning

## One-Hot Encoding and Label Encoding in Machine Learning

One-hot encoding and label encoding are techniques used to convert categorical data into numerical form, which is necessary for most machine learning algorithms:

1. **Label Encoding**:
   - This method assigns a unique integer to each category in a categorical feature.
   - For example, if you have a feature "Color" with categories "Red," "Green," and "Blue," label encoding might assign 0 to "Red," 1 to "Green," and 2 to "Blue."
   - It's simple and efficient, but the numerical ordering can unintentionally imply a priority or ranking which doesn't inherently exist in the actual categories.

2. **One-Hot Encoding**:
   - This method converts each category into a new binary column with values 0 or 1 for each category.
   - Using the same "Color" example, with one-hot encoding, you would create three binary columns: "Is_Red," "Is_Green," and "Is_Blue." A row with "Red" would be represented as (1, 0, 0), "Green" as (0, 1, 0), and "Blue" as (0, 0, 1).
   - This avoids the issue of implied order from label encoding but can increase the dimensionality of the data, especially if there are many categories.

## Interview Questions related to Machine Learning

Interview questions around one-hot encoding and label encoding typically assess your understanding of these techniques, their applications, and their limitations. Here are some common questions you might encounter:

1. **Explain One-Hot Encoding and Label Encoding.**  
   - Can you describe each method and explain when you would use one over the other?

2. **What are the limitations of Label Encoding?**  
   - Why might label encoding not be suitable for certain types of data?

3. **Why could One-Hot Encoding lead to a "curse of dimensionality"?**  
   - How does the number of categories in a feature affect the dimensionality of your dataset after one-hot encoding?

4. **How would you handle a dataset with a large number of categorical features using One-Hot Encoding?**  
   - Discuss strategies to manage or reduce the dimensionality effectively.

5. **Can you give examples of machine learning algorithms that might be sensitive to encoded categories?**  
   - Discuss how algorithms like decision trees or neural networks handle one-hot encoded data differently from linear models like logistic regression.

6. **What impact does encoding have on model performance?**  
   - How do different encoding techniques affect the performance and accuracy of your model?

7. **How do you deal with unseen categories in test data when using encoding?**  
   - Describe strategies to handle categories in test data that were not present during training.

8. **How do you implement One-Hot Encoding and Label Encoding using libraries like scikit-learn?**  
   - Explain or demonstrate coding examples for applying these encoding techniques.


## Default Time zone in Spring Boot


The default timezone in Spring Boot is the system's default timezone, which is typically the one configured on the underlying operating system. This means Spring Boot will use the timezone set in the JVM when the application starts.

If you want to explicitly set the timezone for your Spring Boot application, you can configure it in the application.properties or application.yml file:

For example, in application.properties:
```java
spring.jackson.time-zone=UTC
```

This will ensure that the application works with the specified timezone.

## JsonFormat annotation usage in spring boot.


```java
import com.fasterxml.jackson.annotation.JsonFormat;
import java.sql.Timestamp;

public class TestDTO {

    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss") // Define the date format
    private Timestamp timestamp;

    // Constructor to set the timestamp
    public TestDTO(Timestamp timestamp) {
        this.timestamp = timestamp;
    }

    // Getter and setter methods
    public void setTimestamp(Timestamp timestamp) {
        this.timestamp = timestamp;
    }

    public Timestamp getTimestamp() {
        return this.timestamp;
    }
}
```

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.sql.Timestamp;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.Month;
import java.util.ArrayList;
import java.util.List;

@RestController
public class TestController {

    @GetMapping("/test")
    public List<TestDTO> generateTimestamps() {
        List<TestDTO> timestamps = new ArrayList<>();

        // Current date (today) and end date (e.g., 30th January 2025)
        LocalDate startDate = LocalDate.now();  
        LocalDate endDate = LocalDate.of(2025, Month.JANUARY, 30);

        // Loop through each day between the start and end dates
        for (LocalDate date = startDate; !date.isAfter(endDate); date = date.plusDays(1)) {
            // Set the time to 00:00:00 for each date
            LocalDateTime dateTime = date.atStartOfDay();
            timestamps.add(new TestDTO(Timestamp.valueOf(dateTime))); // Add each timestamp to the list
        }

        return timestamps;  // Return the list of timestamps
    }
}
```

### Output

```json
[
    {
        "timestamp": "2024-12-29 00:00:00"
    },
    {
        "timestamp": "2024-12-30 00:00:00"
    },
    {
        "timestamp": "2024-12-31 00:00:00"
    },
    {
        "timestamp": "2025-01-01 00:00:00"
    },
    {
        "timestamp": "2025-01-02 00:00:00"
    }
    // ... more timestamps till January 30, 2025
]
```

## ObjectMapper in Jackson Explained

`ObjectMapper` is a core class in the Jackson library that provides functionality for converting Java objects to JSON (serialization) and converting JSON to Java objects (deserialization). It is part of the `com.fasterxml.jackson.databind` package and is commonly used in Java applications to work with JSON data.

### Key Features of `ObjectMapper`:

1. **Serialization (Java to JSON)**:
   - `ObjectMapper` can convert Java objects into JSON representations. It handles a wide variety of Java types, including primitive types, collections, `Date`, and `java.time` types.
   - Example:
     ```java
     ObjectMapper objectMapper = new ObjectMapper();
     MyClass obj = new MyClass("John", 25);
     String json = objectMapper.writeValueAsString(obj);
     System.out.println(json); // Output: {"name":"John","age":25}
     ```

2. **Deserialization (JSON to Java)**:
   - `ObjectMapper` can also convert JSON strings into Java objects. The class maps the JSON properties to Java fields based on the names and data types.
   - Example:
     ```java
     String json = "{\"name\":\"John\",\"age\":25}";
     MyClass obj = objectMapper.readValue(json, MyClass.class);
     System.out.println(obj.getName()); // Output: John
     ```

3. **Customizing JSON Processing**:
   - You can customize the serialization and deserialization behavior by using annotations like `@JsonProperty`, `@JsonFormat`, `@JsonIgnore`, and others.
   - Example with `@JsonFormat` for custom date formatting:
     ```java
     @JsonFormat(pattern = "yyyy-MM-dd")
     private LocalDate birthDate;
     ```

4. **Handling Complex Types**:
   - `ObjectMapper` can handle complex types, including nested objects, collections, and generics, by serializing and deserializing them as JSON arrays or objects.
   - Example with a list of objects:
     ```java
     List<MyClass> list = Arrays.asList(new MyClass("John", 25), new MyClass("Jane", 30));
     String json = objectMapper.writeValueAsString(list);
     System.out.println(json); // Output: [{"name":"John","age":25}, {"name":"Jane","age":30}]
     ```

5. **Handling JSON in Different Formats**:
   - `ObjectMapper` can read and write JSON in different formats, including:
     - String-based JSON.
     - Input and Output Streams (useful for reading/writing from files).
     - Files (using `readValue()` and `writeValue()` methods).
     - For handling other formats, you can use `ObjectMapper` with modules like `jackson-dataformat-xml` for XML, `jackson-dataformat-csv` for CSV, etc.

6. **Configuring ObjectMapper**:
   - You can configure `ObjectMapper` globally with various settings, such as enabling/disabling certain features, handling missing properties, or changing the serialization behavior.
   - Example to enable pretty-printing:
     ```java
     ObjectMapper objectMapper = new ObjectMapper();
     objectMapper.enable(SerializationFeature.INDENT_OUTPUT);
     ```

7. **Jackson Modules**:
   - Jackson modules like `jackson-datatype-jsr310` allow you to handle Java 8 Date/Time API types such as `Instant`, `LocalDateTime`, and `ZonedDateTime`. You need to register these modules with `ObjectMapper` for them to be recognized.

   Example:
   ```java
   ObjectMapper objectMapper = new ObjectMapper();
   objectMapper.registerModule(new JavaTimeModule());
   ```

### Example of `ObjectMapper` Usage:

**Serialization (Java to JSON):**
```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class Main {
    public static void main(String[] args) throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();
        MyClass obj = new MyClass("John", 25);
        
        // Serialize to JSON
        String json = objectMapper.writeValueAsString(obj);
        System.out.println(json);  // {"name":"John","age":25}
    }
}

class MyClass {
    private String name;
    private int age;

    // Constructor, getters, setters...
}
```

**Deserialization (JSON to Java):**
```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class Main {
    public static void main(String[] args) throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();
        String json = "{\"name\":\"John\",\"age\":25}";
        
        // Deserialize from JSON
        MyClass obj = objectMapper.readValue(json, MyClass.class);
        System.out.println(obj.getName());  // John
    }
}

class MyClass {
    private String name;
    private int age;

    // Constructor, getters, setters...
}
```

### Common Methods of `ObjectMapper`:
- `writeValueAsString(Object value)` – Converts a Java object to JSON as a String.
- `writeValue(File resultFile, Object value)` – Writes a Java object as JSON to a file.
- `readValue(String content, Class<T> valueType)` – Converts JSON String into a Java object.
- `readValue(InputStream src, Class<T> valueType)` – Converts JSON from an InputStream into a Java object.

### Summary:
`ObjectMapper` is the main class in Jackson used to convert between Java objects and JSON. It provides powerful features for handling JSON data, with support for customization and flexibility, making it an essential tool for working with JSON in Java.

## SemanticException of Hibernate (ORM)

Typically, Hibernate throws `SemanticException` to signal an error in the semantics of `HQL/JPQL` queries. Unlike `QuerySyntaxException` which indicates a syntax error, `SemanticException` denotes that Hibernate doesn’t understand the meaning of a given query.

There are various scenarios that can result in a `SemanticException` in Hibernate. One such way is by creating a valid entity but using an incorrect field path in the query. Here’s how you can do it:

### Scenario: Invalid Field Path

This occurs when you reference a field in your JPQL query that doesn't exist on the entity.

#### Step 1: Define an Entity

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;

    // Getters and setters
}
```

#### Step 2: Create a Repository

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    // No changes needed here
}
```

#### Step 3: Define a Service Layer with an Invalid Query

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import javax.persistence.EntityManager;
import javax.persistence.Query;

@Service
public class UserService {
    @Autowired
    private EntityManager entityManager;

    public void executeFaultyQuery() {
        // Intentionally incorrect field 'usernamee' instead of 'username'
        String hql = "SELECT u FROM User u WHERE u.usernamee = :username";
        Query query = entityManager.createQuery(hql);
        query.setParameter("username", "test");
        query.getResultList(); // This will trigger the SemanticException
    }
}
```

### Explanation

In this scenario, Hibernate will attempt to parse and execute the JPQL query `String hql = "SELECT u FROM User u WHERE u.usernamee = :username";`. Since there is no field `usernamee` in the `User` entity, Hibernate will throw a `SemanticException` indicating that the path expression is invalid.

### Interview Questions
1. What is a SemanticException in the context of Hibernate, and why is it raised in this case?
2. How would you modify the query to prevent this exception?
3. What steps would you take to debug and resolve a SemanticException if encountered in a different, more complex query?

## Segmented Trees Leetcode Question: 2080. Range Frequency Queries

## Dapr (Distributed Application Runtime)

Dapr (Distributed Application Runtime) is a free and open source runtime system designed to support cloud native and serverless computing. sIts initial release supported SDKs and APIs for Java, .NET, Python, and Go, and targeted the Kubernetes cloud deployment system.

## Maven `build-classpath` goal of `dependency` plugin

This goal outputs a classpath string of dependencies from the local repository to a file or log.

The `mvn dependency:build-classpath` command only lists the dependencies (JAR files) that are part of the build classpath, which is used during the compilation and runtime of your Java application. However, this command does not list non-JAR files like `application.properties`, which is typically part of your project resources.

## Crucial points related to META-INF/MANIFEST.MF created by spring boot in the jar executable zip file.

```java
Manifest-Version: 1.0
Created-By: Maven JAR Plugin 3.3.0
Build-Jdk-Spec: 21
Implementation-Title: main-app
Implementation-Version: 0.0.1-SNAPSHOT
Main-Class: org.springframework.boot.loader.launch.JarLauncher
Start-Class: com.school.portal.MainApplication
Spring-Boot-Version: 3.2.1
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Spring-Boot-Classpath-Index: BOOT-INF/classpath.idx
Spring-Boot-Layers-Index: BOOT-INF/layers.idx
```

The `Launcher` class is a special bootstrap class that is used as an executable jar’s main entry point. It is the actual `Main-Class` in your jar file, and it is used to setup an appropriate `ClassLoader` and ultimately call your `main()` method.

https://docs.spring.io/spring-boot/specification/executable-jar/launching.html#:~:text=The%20Launcher%20class%20is%20a,call%20your%20main()%20method.

## List the contents of JAR file

`jar tf myapp.jar`

## How Spring Boot Automatically Detects and Uses Tomcat as the Embedded Server

In Spring Boot, the embedded server (like Tomcat, Jetty, or Undertow) is automatically included as part of the application when you create a Spring Boot project. The fact that it's not explicitly mentioned in the MANIFEST.MF file doesn't prevent Spring Boot from recognizing and using it. When you add a Spring Boot starter dependency for web applications, such as `spring-boot-starter-web`, it automatically includes the embedded Tomcat server (by default).

## `mvn dependency:tree`

## AWS EKS (Elastic Kubernetes Service)
b
## `System.getProperty` in java

```java
  String classpath = System.getProperty("java.class.path");
	System.out.println("Full Classpath:");
	// Print each classpath entry (they are separated by the system-specific path separator)
  for (String path : classpath.split(System.getProperty("path.separator"))) {
    System.out.println(path);
}
```

## `mvn spring-boot:run --debug`

## What is an `uber` or a `fat` JAR ?

An `uber jar` and a `fat jar` are essentially the same thing, both referring to a Java archive (JAR) file that includes not only the application code but also all of its necessary dependencies, allowing it to run independently without requiring separate library installations; in simpler terms, it's a self-contained executable JAR file where everything needed to run the application is bundled together.

## Nested Classes in Java

## Byte Buddy (JAVA Byte Code generation and manipulation library)

Byte Buddy is a modern, powerful library for creating and modifying Java classes at the bytecode level. It abstracts the complexity of bytecode manipulation, allowing developers to dynamically create, alter, or enhance classes without needing to write or understand bytecode directly. Here's how Byte Buddy operates:

### Key Features

- Dynamic class creation: You can generate classes at runtime based on interfaces or existing classes.
- Method delegation/interception: It allows you to intercept method calls and delegate them to another method, an external handler, or even dynamically inject logic at runtime.
- Subclassing and instrumentation: You can create subclasses or modify existing classes, changing their behavior in a seamless way.
- Annotations and Fields: It supports adding fields, methods, and annotations to existing classes or newly generated ones.
- Integration with agents: Byte Buddy integrates well with Java agents, allowing developers to redefine and instrument classes that have already been loaded by the JVM.

### Use cases

- AOP (Aspect-Oriented Programming): Libraries like Spring or JHipster use Byte Buddy to enhance classes with cross-cutting concerns (logging, security, transactions).
- Mocking frameworks: Mockito and other testing libraries use Byte Buddy to generate mock objects for testing.
- Instrumentation and profiling: Byte Buddy is useful for writing Java agents that monitor or profile applications by injecting behavior into the running application.

## Difference between web server and application server

The key difference between a web server and an application server lies in their functionality and scope of services. A web server, like Tomcat, is primarily responsible for handling HTTP requests, serving static content (such as HTML, CSS, and JavaScript), and processing dynamic content like servlets and JSPs. It focuses on the basic task of delivering web pages to users. In contrast, an application server, such as WildFly, offers a broader set of features beyond web page serving. It provides an environment for running enterprise applications, handling business logic, transaction management, messaging, and more, by supporting the full Java EE (Jakarta EE) stack, including EJB (Enterprise JavaBeans), JMS (Java Message Service), and JPA (Java Persistence API). While both can serve web content, an application server is designed to manage complex, enterprise-level applications, making it more suited for handling larger, more intricate systems.

## Where, SQLDelete, Loader annotation in Hibernate


## EventEmitter in Angular 17

In Angular 17, `EventEmitter` is a class that's part of the `@angular/core` package and is used to create custom events in components. It extends the `Subject` class from RxJS, allowing you to emit events and subscribe to them.

Here’s a quick breakdown of its key features and how it is typically used:

1. **Purpose**: The primary use of `EventEmitter` is to facilitate communication from child components to parent components. It allows child components to emit events that parent components can listen to, enabling interaction and data exchange.

2. **Usage**:
   - **Importing**: First, you import `EventEmitter` and `Output` from `@angular/core`.
   - **Declare**: In the child component, you declare an instance of `EventEmitter` and mark it with the `@Output()` decorator. This decorator makes the property available to the parent component for event binding.
   - **Emit Events**: Within the child component, you use the `.emit()` method of `EventEmitter` to send data or a notification to the parent.
   - **Listen to Events**: In the parent component template, you use event binding to listen to these events and trigger appropriate functions.

3. **Code Example**:

   ```typescript
   // child.component.ts
   import { Component, Output, EventEmitter } from '@angular/core';

   @Component({
     selector: 'app-child',
     template: `<button (click)="sendMessage()">Send Message</button>`
   })
   export class ChildComponent {
     @Output() messageEvent = new EventEmitter<string>();

     sendMessage() {
       this.messageEvent.emit('Hello from Child!');
     }
   }
   ```

   ```html
   <!-- parent.component.html -->
   <app-child (messageEvent)="receiveMessage($event)"></app-child>
   ```

   ```typescript
   // parent.component.ts
   import { Component } from '@angular/core';

   @Component({
     selector: 'app-parent',
     templateUrl: './parent.component.html'
   })
   export class ParentComponent {
     receiveMessage(message: string) {
       console.log('Message received:', message);
     }
   }
   ```

4. **Best Practices**:
   - Use specific names for event emitters that convey their purpose, improving code readability.
   - Clean up subscriptions if you manually subscribe to events to prevent memory leaks, although `EventEmitter` itself in Angular manages subscriptions automatically.

## Why Does Angular's EventEmitter Extend RxJS's Subject Class?

`EventEmitter` in Angular extends the `Subject` class from RxJS primarily to leverage the capabilities of observables for event handling. Here are some reasons why this design choice is beneficial:

1. **Reactive Programming**: By extending `Subject`, `EventEmitter` gains the powerful features of observables, allowing developers to apply reactive programming principles. This includes the capability to easily handle asynchronous data streams and leverage operators for more complex data transformations.

2. **Multiple Subscribers**: Like a `Subject`, an `EventEmitter` can have multiple subscribers. This means multiple parts of an application can listen to the same event and respond without interfering with one another.

3. **Hot Observable**: `EventEmitter` acts as a "hot" observable because it begins emitting data as soon as it is created and continues to emit regardless of whether there are any subscribers. This behavior is suitable for event-driven architectures where events may be dispatched at any time.

4. **Flexibility in Event Handling**: By using RxJS subjects, developers can take advantage of advanced features like error handling, completions, and unsubscriptions, which are intrinsic to RxJS observables.

5. **Uniform API**: Extending `Subject` allows `EventEmitter` to have a consistent API with other observables in Angular applications, making it easier for developers to understand and use.



## What is $event in Angular

`$event` is a special variable in Angular templates used to represent the data or payload associated with an event.

### In the Context of Event Binding:

When you bind an event in a template, Angular automatically provides an `$event` variable that contains the event object or data emitted by the event, allowing you to access properties or methods of the event directly within the template.

### Usage:

1. **Standard DOM Events**:
   For standard DOM events like click, input, etc., `$event` contains the properties of the JavaScript Event object.

   ```html
   <button (click)="handleClick($event)">Click Me</button>
   ```

   ```typescript
   handleClick(event: MouseEvent) {
     console.log(event.clientX, event.clientY); // access coordinates of the click
   }
   ```

2. **Custom Events**:
   When dealing with custom component events using `EventEmitter`, `$event` holds the emitted data.

   ```typescript
   // In child.component.ts
   @Output() messageEvent = new EventEmitter<string>();
   // Emission
   this.messageEvent.emit('Hello Parent');
   ```

   ```html
   <!-- In parent.component.html -->
   <app-child (messageEvent)="receiveMessage($event)"></app-child>
   ```

   ```typescript
   // In parent.component.ts
   receiveMessage(message: string) {
     console.log(message); // Outputs: Hello Parent
   }
   ```

By using `$event`, you can pass detailed information from the event to your component methods, enabling more dynamic and responsive behavior within your Angular applications.

Here's a potential discussion question for your topic:

## Understanding Spring Boot Transaction Management and Entity Persistence

**Question:** 
In the context of Spring Boot, why might explicitly calling `repository.save()` be unnecessary within a method annotated with `@Transactional`, and how does Spring's transaction management handle data persistence in such scenarios?

**Explanation:**

As a senior software engineer, it's important to understand the inner workings of Spring Boot's transaction management. The `@Transactional` annotation in Spring Boot is used to manage transactions declaratively at the method level. When a method is annotated with `@Transactional`, Spring creates a proxy that allows you to manage transaction boundaries automatically.

In certain scenarios, explicitly calling `repository.save()` might seem redundant due to the way entities are managed by the persistence context (often in JPA/Hibernate environments):

1. **Persistence Context**: Within a transaction, the persistence context refers to the entity manager's ability to track changes to your entity instances. Once an entity is loaded, it's placed in the persistence context, which monitors changes to the object.

2. **Automatic Dirty Checking**: Hibernate, often a common JPA provider, performs a feature known as automatic dirty checking. This means that within a transaction, Hibernate tracks modifications to entities automatically. At the end of the transaction, before it commits, Hibernate will flush any changes detected in the entities back to the database.

3. **Flushing**: When the transaction is committed, flush operations are triggered, ensuring that the state of the in-memory entities within the persistence context is synchronized with the database. This can often negate the need for explicitly calling `repository.save()`, as modifications are automatically detected and persisted.

However, there are times when calling `repository.save()` is still necessary, such as introducing new entities to the persistence context or explicitly controlling when the persistence context should sync with the database (e.g., to get the generated ID after an insert).

Understanding these nuances ensures that you write efficient and clean code while leveraging the full power of Spring's transaction management features.

https://stackoverflow.com/questions/8190926/transactional-saves-without-calling-update-method
https://docs.jboss.org/hibernate/core/3.6/reference/en-US/html/objectstate.html

## Create a simple Event Emitter

https://xgrommx.github.io/rx-book/content/how_do_i/simple_event_emitter.html

## Multi-tenancy in Spring Boot Hibernate

https://stackoverflow.com/questions/78674892/how-do-i-get-hibernate-tenantid-to-work-with-joincolumn

org.hibernate.context.spi.CurrentTenantIdentifierResolver

https://spring.io/blog/2022/07/31/how-to-integrate-hibernates-multitenant-feature-with-spring-data-jpa-in-a-spring-boot-application

## Difference between Observables and Promises


## Front end Interview Question EventEmitter

https://frontendlead.com/coding-questions/advanced-event-emitter-implementation-javascript
https://www.greatfrontend.com/questions/javascript/event-emitter
https://medium.com/@rahul.jindal57/github-repo-node-js-interview-questions-for-event-emitters-streams-and-multi-threading-24d5dcfb7487


## RxJS Made Easy: The Magical Cookie Factory 🏭

### Imagine a Cookie Bakery

#### Observables: The Cookie Factory
- **What Are They?**: Think of an Observable as a cookie factory. This factory can produce cookies one after another (cookies = data items) and send them to anyone interested in getting cookies.
- **How It Works**: The factory doesn't start baking cookies until someone places an order. Once an order is placed, it starts baking and sending cookies out one by one.

#### Observer: The Cookie Lover
- **What Is It?**: An Observer is like a person who loves cookies and has subscribed to get cookies (data) from the factory.
- **Their Job**: When the factory sends out a cookie, our observer grabs it and enjoys it. They can decide what to do with each cookie, like eating it or saving it for later.

#### Subscription: The Cookie Delivery Service
- **What Is It?**: Subscription is like signing up for a delivery service that brings cookies (data) from the factory to the cookie lover.
- **How It Works**: Once the cookie lover subscribes, cookies start coming in regularly. If the cookie lover decides they have had enough, they can cancel the service to stop getting more cookies.

#### Operators: The Cookie Flavors
- **What Are They?**: Operators are like different flavors or toppings you can add to your cookies. They change how the cookies taste.
- **How They Work**: Before the cookies reach the observer, they can go through different flavor stations where they might be dipped in chocolate, have sprinkles added, or be smushed into cookie sandwiches. These operators help you decide what the cookies should look like when they’re delivered.

#### Subject: The Party Plan
- **What Is It?**: A Subject is like a big party where everyone gets cookies at the same time. Here, multiple people (observers) can enjoy cookies as they are baked and brought out.
- **How It Works**: Everyone at the party sees the cookies at the same time, and they can all react differently. Some might eat them right away, and others might decide to wait.

### How They All Work Together:
1. **You** (as a programmer) decide you want to taste some cookies (get data).
2. You **subscribe** to the cookie factory (observable) and start receiving cookies (data).
3. Each time you get a cookie, you can enjoy it or modify it using different toppings (operators).
4. If you ever get full or disinterested, you cancel the subscription to stop receiving cookies.

This is how the basic components of RxJS work together in a simple analogy! Does this help you to understand ? If yes, then follow my github profile.

## Implement EventEmitter in Javascript from scratch

### My solution:

```javascript
class Event {
    constructor(
        eventName,
        eventIndex,
        eventEmitterReference,
        callback
    ) {
        this.eventName = eventName;
        this.eventIndex = eventIndex;
        this.eventEmitterReference = eventEmitterReference;
        this.callback = callback;
    }

    release() {
        this.eventEmitterReference.map.get(this.eventName).splice(this.eventIndex, 1);
    }

    call(...args) {
        this.callback(...args)
    }

}

class EventEmitter {

    constructor() {
        this.map = new Map();
    }

    subscribe(eventName, callback) {
        if (!this.map.has(eventName)) {
            this.map.set(eventName, []);
        }
        let sizeOfArray = this.map.get(eventName).length;
        let e = new Event(eventName, sizeOfArray, this, callback);
        this.map.get(eventName).push(e);
        return e;
    }

    emit(eventName, ...args) {

        // case 1: If an eventName is not present, throw error
        // case 2: If an eventName is present, then run all the events related to that eventName
        if (!this.map.has(eventName)) {
            return;
        }
        let arr = this.map.get(eventName);
        for (let i = 0; i < arr.length; i++) {
            arr[i].call(...args);
        }
    }
}


function main1() {
    let emitter = new EventEmitter();
    let receivedData = null;

    emitter.subscribe('testEvent', (data) => {
        receivedData = data;
    });

    emitter.emit('testEvent', 'HELLO WORLD');

    console.log("received Data =" ,receivedData);
}

function main2() {
    let emitter = new EventEmitter();
    let callCount = 0;

    emitter.subscribe('testEvent', () => callCount++);
    emitter.subscribe('testEvent', () => callCount++);

    emitter.emit('testEvent');

    console.log("Call Count =" ,callCount);
}

function main3() {
    let emitter = new EventEmitter();
    let callCount = 0;

    const subscription = emitter.subscribe('testEvent', () => callCount++);
    emitter.emit('testEvent');
    subscription.release();
    emitter.emit('testEvent');
    console.log("Call Count =" ,callCount);
}

main3();
```

https://eishta.medium.com/javascript-interview-implement-an-event-emitter-class-8e983a2c3b12

https://stackoverflow.com/questions/31735129/how-do-javascript-closures-work-at-a-low-level

https://dmitryfrank.com/articles/js_closures

## Closures in Javascript

A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives a function access to its outer scope. In JavaScript, closures are created every time a function is created, at function creation time.

## Closures and Memory Management

### Explain how closures affect memory management in JavaScript. How would you detect and mitigate potential memory leaks caused by closures?

Closures in JavaScript have a significant impact on memory management because they maintain references to the variables in their lexical scope even after the function that created them has finished execution. This feature, while powerful for maintaining state and encapsulating functionality, can lead to memory leaks if not managed carefully.

Suppose we have a setup where closures inadvertently hold onto more data than necessary due to event listeners:

```javascript
function createButtonControl(id) {
    const button = document.getElementById(id);
    let clickCount = 0;

    function handleClick() {
        clickCount++;
        console.log(`Button clicked ${clickCount} times.`);
    }

    // Event listener closure holds the reference to `handleClick`
    button.addEventListener('click', handleClick);

    // The issue arises if we forget to remove the event listener:
    // We created a closure that holds a reference to button and its related data
}
```

If this function is called multiple times or if the button is removed from the DOM without removing the event listener, the handleClick closure can keep the button and clickCount in memory unnecessarily, leading to a memory leak.

#### Mitigating the memory leak

```javascript
function createButtonControl(id) {
    const button = document.getElementById(id);
    let clickCount = 0;

    function handleClick() {
        clickCount++;
        console.log(`Button clicked ${clickCount} times.`);
    }

    // Properly manage the lifecycle of the event listener
    button.addEventListener('click', handleClick);

    return () => {
        // Remove the event listener when done
        button.removeEventListener('click', handleClick);
        console.log('Event listener removed.');
    };
}

// Usage
const removeControl = createButtonControl('myButton');

// Later when the control is no longer needed
removeControl();
```

- `Event Listener Management`: By returning a function to remove the event listener, we allow the caller to manage the life cycle of the closure effectively.
- `Manual Clean-up`: This ensures that references held by the closure (like button and handleClick) can be freed when they are no longer needed.
- `Avoiding Long-lived Captures`: Be cautious of attaching closures to global contexts or objects that have long lifecycles without a clean-up mechanism.

### Implementation of Class in Javascript using closures

```javascript
function createCollectionTracker() {
    const collection = {};

    return {
        addItem(item) {
            if (!collection[item]) {
                collection[item] = 0;
            }
            collection[item]++;
        },
        getCount(item) {
            return collection[item] || 0;
        },
        getItems() {
            return Object.keys(collection).filter(key => collection[key] > 0);
        }
    };
}

const tracker = createCollectionTracker();
tracker.addItem('apple');
tracker.addItem('banana');
tracker.addItem('apple');

console.log(tracker.getCount('apple')); // What will this log?
console.log(tracker.getCount('banana')); // What will this log?
console.log(tracker.getItems()); // What will this log maybe with explanation?
```

## Important Note Regarding Closures in Javascript

Closure is observed when an inner function is returned outside of it's lexical scope but still maintains a connection to its lexical environment. A Closure occurs when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope. In simpler terms, a Closure gives you access to an outer function's scope from an inner function.
```javascript
function addOne() {
  var x = 1;
   function bar() {
    console.log(++x);
  }
	return bar;
}

var adder = addOne();
adder(); // 2
adder(); // 3
adder(); // 4
```
## Application of Clocusres

```javascript
document.getElementById('redButton').onclick = func('red');
document.getElementById('blueButton').onclick = func('blue');
document.getElementById('greenButton').onclick = func('green');

function func(color) {
    return () => {
        document.body.style.color = `${color}`;
    };
}
```
This example highlights how closures provide a powerful way to "freeze" specific values in functions for later use. It's a clean and reusable pattern, especially in scenarios like event handling.

## Closure in a Loop

```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000);
}
```

You might expect the output to be:
```sql
0
1
2
```

Actual Output:
```sql
3
3
3
```

### Why Does This Happen?

1. Scope of var: The variable i declared with var has function scope, not block scope. By the time the setTimeout callback executes (after 1000 ms), the loop has already completed, and the value of i is 3.
2. Callback Delay: The setTimeout function registers a callback to be executed later. When the callback is executed, it accesses the same i variable, which by then has the value 3.

### Solution with Closures

To preserve the correct value of i for each iteration, you can use a closure. By creating a new scope for each iteration, you "capture" the value of i at that specific point in time.

#### Using an Immediately Invoked Function Expression (IIFE):

```javascript
for (var i = 0; i < 3; i++) {
    (function(i) {
        setTimeout(() => {
            console.log(i);
        }, 1000);
    })(i);
}
```

#### How It Works:
1. The IIFE (Immediately Invoked Function Expression) creates a new scope for each iteration of the loop.
2. The value of i is passed as an argument to the IIFE, preserving its value inside the function's scope.
3. The setTimeout callback uses the i value from the IIFE's scope, not the outer i.

Output:

```sql
0
1
2
```

#### Modern Solution with let:

Since let has block scope, you don't need an IIFE in modern JavaScript:

```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000);
}
```

#### Why Does This Work?

1. The let declaration creates a new block-scoped i for each iteration.
2. Each iteration's i is independent of others, and the setTimeout callback accesses the correct i for that iteration.


## Function-scoped, Global-scoped, Block-scoped in Javascript

Let’s break down **function scope**, **global scope**, and **local scope** in a beginner-friendly way.


### **1. Scope: What Does It Mean?**
**Scope** determines where variables and functions are accessible in your code. It’s the context in which a variable is defined.

Think of it as:
- **Global scope**: The world — everyone can see it.
- **Function scope**: A house — only people inside the house can see it.
- **Local (block) scope**: A room in the house — only people in the room can see it.

### **2. Global Scope**

#### **What Is It?**
A variable or function is in the global scope if it’s defined **outside of any function or block**. It’s accessible anywhere in your program.

#### **Example:**
```javascript
var globalVariable = "I am global";

function showGlobal() {
    console.log(globalVariable); // Accessible here
}

showGlobal(); // Output: I am global
console.log(globalVariable); // Accessible here too
```

#### **Key Points:**
- Declared outside of any function.
- Available everywhere, even inside functions.


### **3. Function Scope**

#### **What Is It?**
A variable declared inside a function is accessible **only within that function**. It is not accessible outside.

#### **Example:**
```javascript
function myFunction() {
    var functionScoped = "I am inside a function";
    console.log(functionScoped); // Accessible here
}

myFunction();
console.log(functionScoped); // Error: functionScoped is not defined
```

#### **Key Points:**
- Declared inside a function.
- Can’t be accessed from outside the function.


### **4. Local (Block) Scope**

#### **What Is It?**
Variables declared with `let` or `const` inside a block (`{}`) are accessible **only within that block**. This is called **block scope**. A block can be inside a function, loop, or conditional.

#### **Example:**
```javascript
{
    let blockScoped = "I am in a block";
    console.log(blockScoped); // Accessible here
}

console.log(blockScoped); // Error: blockScoped is not defined
```

#### **Example in Loops:**
```javascript
for (let i = 0; i < 3; i++) {
    console.log(i); // Accessible here
}

console.log(i); // Error: i is not defined
```

#### **Key Points:**
- Declared with `let` or `const`.
- Exists only inside the block where it’s defined.

---

### **Comparing `var`, `let`, and `const` Scopes**

#### `var`: Function Scope
```javascript
function myFunction() {
    var x = 10;
    if (true) {
        var x = 20; // Same variable
        console.log(x); // 20
    }
    console.log(x); // 20 (still the same variable)
}
myFunction();
```

#### `let` and `const`: Block Scope
```javascript
function myFunction() {
    let x = 10;
    if (true) {
        let x = 20; // Different variable
        console.log(x); // 20
    }
    console.log(x); // 10 (original variable)
}
myFunction();
```

---

### **Global vs Function vs Local Scope Example**

```javascript
// Global Scope
let globalVar = "I am global";

function myFunction() {
    // Function Scope
    let functionVar = "I am in a function";

    if (true) {
        // Block Scope
        let blockVar = "I am in a block";
        console.log(globalVar); // Accessible
        console.log(functionVar); // Accessible
        console.log(blockVar); // Accessible
    }

    console.log(blockVar); // Error: blockVar is not defined
}

myFunction();
console.log(globalVar); // Accessible
console.log(functionVar); // Error: functionVar is not defined
```

---

### **Why Scope Matters**

1. **Prevent Conflicts**:
   - Variables in the global scope can accidentally overwrite each other.
   - Use local scope to avoid this.

2. **Memory Efficiency**:
   - Variables in local or block scope are destroyed once the function/block is done executing.

3. **Code Readability**:
   - Proper scoping makes it easier to understand which parts of your code can access a variable.

---

### **Key Takeaways**
1. **Global Scope**:
   - Variables declared outside functions are accessible everywhere.
   - Be careful — too many global variables can cause bugs.

2. **Function Scope**:
   - Variables declared inside a function are accessible only within that function.

3. **Local (Block) Scope**:
   - Variables declared with `let` or `const` inside a block (`{}`) are accessible only within that block.

## Hoisting in Javascript

**Hoisting** is a behavior in JavaScript where variable and function declarations are moved to the top of their scope during the compilation phase, **before the code is executed**. This affects how variables and functions behave in different scopes.

Let’s break it down:


### **1. What is Hoisting?**
Hoisting means that:
1. **Variable and function declarations** are moved to the top of their scope.
2. Only the **declaration** is hoisted, not the initialization.


### **2. Hoisting with Variable Declarations**

#### **With `var` (Function Scope):**
Variables declared with `var` are hoisted, but their value is `undefined` until the line of code where they are initialized.

```javascript
console.log(a); // Output: undefined (declaration is hoisted, but not the value)
var a = 10;
console.log(a); // Output: 10
```

Internally, JavaScript treats the code like this:
```javascript
var a; // Declaration is hoisted
console.log(a); // undefined
a = 10; // Initialization happens here
console.log(a); // 10
```

#### **With `let` and `const` (Block Scope):**
Variables declared with `let` and `const` are hoisted, but they are not initialized. They are in a "temporal dead zone" (TDZ) from the start of the block until their declaration is encountered.

```javascript
console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 20;

console.log(c); // ReferenceError: Cannot access 'c' before initialization
const c = 30;
```

---

### **3. Hoisting with Function Declarations**

Function declarations are fully hoisted, meaning you can call a function **before it is defined**.

#### **Example:**
```javascript
sayHello(); // Output: Hello, World!

function sayHello() {
    console.log("Hello, World!");
}
```

Internally:
```javascript
function sayHello() {
    console.log("Hello, World!");
}

sayHello();
```

#### **Key Point:**
Only **function declarations** are hoisted. **Function expressions** are not.


### **4. Hoisting with Function Expressions**

If you assign a function to a variable, it behaves like a variable and only the variable declaration is hoisted, not the function.

#### **Example:**
```javascript
sayHello(); // TypeError: sayHello is not a function

var sayHello = function () {
    console.log("Hello, World!");
};
```

Internally:
```javascript
var sayHello; // Declaration is hoisted
sayHello(); // Error: sayHello is undefined
sayHello = function () {
    console.log("Hello, World!");
};
```

### **5. How Hoisting Relates to Scope**

Hoisting works differently depending on the scope:

#### **Global Scope:**
Variables and functions declared in the global scope are hoisted to the top of the global context.

```javascript
console.log(globalVar); // undefined
var globalVar = 10;
```

#### **Function Scope:**
Variables and functions declared inside a function are hoisted to the top of the function.

```javascript
function test() {
    console.log(localVar); // undefined
    var localVar = 20;
    console.log(localVar); // 20
}
test();
```

#### **Block Scope:**
With `let` and `const`, hoisting still occurs, but they remain in the **temporal dead zone** until the declaration is encountered.

```javascript
{
    console.log(blockVar); // ReferenceError
    let blockVar = 30;
}
```

### **6. Combining Hoisting with Closures**

When closures capture variables, hoisting can sometimes lead to unexpected behavior if not understood properly.

#### **Example:**
```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1000);
}
```

- `var i` is hoisted and shared across all iterations.
- By the time the `setTimeout` callbacks execute, the loop has finished, and `i` is `3`.

Fix it using `let`:
```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1000);
}
```

Now, `let i` is block-scoped, so each iteration creates a new `i`.


### **7. Summary of Hoisting**
- **Declarations are hoisted**: Variables (`var`, `let`, `const`) and functions are moved to the top of their scope.
- **Initialization is not hoisted**: Variables are only assigned values at the point where they are initialized.
- `let` and `const` have a **temporal dead zone**: You cannot use them before their declaration.
- **Function expressions are not hoisted**: Only the variable declaration is hoisted.

## 🔍✨ Exploring JavaScript Scoping: var vs let in Loops & the Magic of Closures 🔐

### `var`

The following code explains how var behaves in a for loop with asynchronous method execution:

```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1000);
}
```

```javascript
function myLoop () {
    var i = 0;
    function f1 () {
      console.log(i);  
    }
    
    i = 1;
    function f2 () {
        console.log(i);
    }
    
    i = 2;
    function f3 () {
        console.log(i);
    }
    
    i = 3;
    function f4 () {
        console.log(i);
    }
    
    return {
        f1: f1, f2: f2, f3: f3, f4: f4
    };
}

var func = myLoop();

func.f1();
func.f2();
func.f3();
func.f4();
```

### `let`

The following code will explain how `let` behaves in a for loop:

```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 1000);
}
```

```javascript

function myLoop2 () {
    
    {
        let i = 0;
        function f1 () {
            console.log(i);
        }
    }
    
    {
        let i = 1;
        function f2 () {
            console.log(i);
        }
    }
    
    {
        let i = 2;
        function f3 () {
            console.log(i);
        }
    }
    
    {
        let i = 3;
        function f4 () {
            console.log(i);
        }
    }
    
    return {
        f1: f1, f2: f2, f3: f3, f4: f4
    };
}

var func2 = myLoop2();

func2.f1();
func2.f2();
func2.f3();
func2.f4();
```

- `ES5 (older versions of JavaScript)`: Function declarations are not block-scoped. If you declare a function inside a block, such as within an if statement or for loop, the function is hoisted to the nearest function or global scope, ignoring the block. This means it will be available outside the block as well.
- ES6+ (modern JavaScript): Function declarations inside a block are block-scoped. This means the function is only available inside the block, similar to how let and const work.

In strict mode (e.g., using 'use strict'), functions inside blocks are always block-scoped. In non-strict mode, the behavior may vary depending on the environment (browsers or versions).

#### Note
 In the context of asynchronous operations, closures are particularly useful because they enable access to variables that were defined before the asynchronous code executes.

## What is `scope` in Javascript

`Scope` are a the set of rules that govern how the engine can look up a variable by its identifier name and find it, either in the current scope, or in any of the nested scopes it’s contained within.

In programming, "lexical scope" refers to a variable's scope being determined by its position in the source code (where it is defined), while "dynamic scope" means a variable's scope is determined by the calling context at runtime, essentially based on the most recent assignment to that variable when the function is called; most modern languages use lexical scoping as it is considered more predictable and easier to reason about, while dynamic scoping is less common and can lead to unexpected behavior.


### Scope Determination:
- Lexical Scope: Based on where a variable is defined in the code (static, compile-time). 
- Dynamic Scope: Based on where a function is called from (runtime). 

## Javascript Interview Question

Give the output of that code:

```javascript
const x = 0;
function pippo() {
 console.log(x);
}
function pluto() {
 const x = 1;
 pippo();
}
pluto();
```

## Difference between Observables and Promises

https://stackoverflow.com/questions/37364973/what-is-the-difference-between-promises-and-observables

Observables are called "lazy" because they only begin to execute their logic when a subscriber actively subscribes to them, meaning no actions are taken until someone is actively listening for data, unlike a Promise which starts executing immediately regardless of whether anyone is listening; this allows for efficient data handling and building complex data pipelines through chaining operators before actual execution occurs.


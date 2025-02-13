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

```javascriptfunction myLoop () {
    var i = 0;
    
    var f1 = (function (val) {
        return () => {
            console.log(val);
        }
    })(i);
    
    i = 1;
    var f2 = (function (val) {
        return () => {
            console.log(val);
        }
    })(i);
    
    i = 2;
    var f3 = (function (val) {
        return () => {
            console.log(val);
        }
    })(i);
    
    i = 3;
    var f4 = (function (val) {
        return () => {
            console.log(val);
        }
    })(i);
    
    return {
        f1: f1, f2: f2, f3: f3, f4: f4,
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

The output of the code is:

```
0
```

### Explanation:

1. **Global Scope**:
   - A variable `x` is declared and initialized with `0`.
   - A function `pippo` is defined, which logs the value of `x`.

2. **Function `pluto`**:
   - Inside `pluto`, a new `x` variable is declared and initialized with `1`. 
   - The function `pippo` is called.

3. **Function Execution**:
   - When `pluto` is called, the variable `x` within its local scope is not used by `pippo`. This is because `pippo` was defined in the global scope and retains its reference to the global variable `x`.
   - JavaScript uses **lexical scoping**. This means that a function's scope is determined by its definition, not by its invocation.

4. **Final Output**:
   - The `console.log(x)` inside `pippo` refers to the `x` from the global scope, which is `0`.

So the output is `0`.

## Meaning of **Lexical** in "Lexical Scoping" (JavaScript)

The term **lexical** in **lexical scoping** refers to the **physical or textual structure of the code as written by the programmer**. It means that the scope of a variable is determined by its position in the source code at the time the code is written (or parsed), not at runtime.

In JavaScript:
- Lexical scoping is the rule that defines how variable names are resolved in nested functions.
- Inner functions have access to variables defined in their outer scope due to where the functions are **declared** in the code, not where they are **called**.

---

### Example:
```javascript
function outer() {
    let outerVariable = "I'm from the outer function";

    function inner() {
        console.log(outerVariable); // Accessing a variable from the outer scope
    }

    inner();
}

outer();
```

#### Key Points:
1. **Declaration Matters**: 
   - The `inner` function is lexically inside the `outer` function, so it has access to `outerVariable`.
2. **Not Affected by Call Location**:
   - Even if `inner` were passed elsewhere and called, it would still have access to `outerVariable` because of where it was **declared**.

---

### Why Lexical Scoping is Important
Lexical scoping enables **closures**, where inner functions can "remember" variables from their lexical (outer) scope even after the outer function has finished executing.

```javascript
function makeCounter() {
    let count = 0;

    return function () {
        count++;
        console.log(count);
    };
}

const counter = makeCounter();
counter(); // 1
counter(); // 2
```

Here, the returned function keeps a reference to `count` because of lexical scoping.

In summary, **lexical** in lexical scoping relates to **how the code is written** and determines variable accessibility based on **where functions are declared in the code hierarchy**, not where they are executed.

## Understanding ReferenceError vs. TypeError in JavaScript

I got a very interesting paragraph from `You Don't Know JS: Scope & Closures` by Kyle Simpson:

`ReferenceError is scope resolution-failure related, whereas TypeEr
ror implies that scope resolution was successful, but that there was an
illegal/impossible action attempted against the result.`

1. **`ReferenceError`**:  
   This error occurs when a variable is not defined in the accessible scope. It means JavaScript could not resolve the identifier during scope resolution.  

   **Example**:
   ```javascript
   console.log(x); // ReferenceError: x is not defined
   ```
   In this case, JavaScript tried to resolve `x` in the current and parent scopes, but it couldn't find it anywhere.

2. **`TypeError`**:  
   This error happens when a variable or object exists and was resolved successfully in the scope, but an illegal or invalid operation was attempted with it.  

   **Example**:
   ```javascript
   let obj = null;
   console.log(obj.property); // TypeError: Cannot read property 'property' of null
   ```
   Here, `obj` is defined (so no `ReferenceError`), but you are trying to access a property of `null`, which is not allowed.

### Key Takeaway:
- **`ReferenceError`**: The identifier itself couldn't be found.
- **`TypeError`**: The identifier was found, but the operation on it was invalid. 

Understanding these distinctions can significantly help in debugging JavaScript programs.

## Behavior of Unfulfilled RHS and LHS References in JavaScript

I got a very interesting paragraph from `You Don't Know JS: Scope & Closures` by Kyle Simpson:

`Unfulfilled RHS references result in ReferenceErrors being thrown.
Unfulfilled LHS references result in an automatic, implicitly created
global of that name (if not in Strict Mode), or a ReferenceError (if in
Strict Mode).`

### 1. **RHS (Right-Hand Side) Reference:**
An RHS reference occurs when you attempt to **retrieve the value** of a variable.

- **Unfulfilled RHS Reference**:  
  If the variable doesn't exist in the accessible scope chain, **`ReferenceError`** is thrown.  

  **Example:**
  ```javascript
  console.log(x); // ReferenceError: x is not defined
  ```
  - JavaScript attempts to "read" the value of `x`, but it doesn't exist.

---

### 2. **LHS (Left-Hand Side) Reference:**
An LHS reference occurs when you attempt to **assign a value** to a variable.

- **Unfulfilled LHS Reference (Non-Strict Mode):**  
  If the variable does not exist in the scope, JavaScript **implicitly creates a global variable** with that name.  

  **Example (Non-Strict Mode):**
  ```javascript
  function foo() {
      y = 10; // Creates a global variable 'y' implicitly
  }
  foo();
  console.log(y); // 10
  ```
  This behavior is a legacy feature of JavaScript and is considered bad practice because it can lead to unexpected bugs.

- **Unfulfilled LHS Reference (Strict Mode):**  
  In **Strict Mode**, JavaScript enforces stricter rules. If you try to assign to a variable that is not declared, it throws a **`ReferenceError`** instead of implicitly creating a global variable.  

  **Example (Strict Mode):**
  ```javascript
  "use strict";
  function foo() {
      y = 10; // ReferenceError: y is not defined
  }
  foo();
  ```

---

### Why Does This Distinction Matter?
- **RHS references** deal with retrieving values, while **LHS references** deal with creating or assigning them.  
- In modern JavaScript, **Strict Mode** is strongly recommended to avoid accidental global variable creation with unfulfilled LHS references.

### Summary:
- **RHS unfulfilled reference**: Throws `ReferenceError` because the variable cannot be resolved.
- **LHS unfulfilled reference**:  
  - Non-Strict Mode: Creates an implicit global variable.  
  - Strict Mode: Throws `ReferenceError`.

**NOTE**: Lexical scope means that scope is defined by author-time decisions of where functions are declared. The lexing phase of compilation is essentially able to know where and how all identifiers are declared, and thus predict how they will be looked up during execution.

## Scope in Javascript

Scope consists of a series of “bubbles” that each act as a container or bucket, in which identifiers (variables, functions) are declared. These bubbles nest neatly inside each other, and this nesting is defined at author time.

## Collision Avoidance in Javascript

Another benefit of “hiding” variables and functions inside a scope is
to avoid unintended collision between two different identifiers with
the same name but different intended usages. Collision results often
in unexpected overwriting of values.

```javascript
function foo() {
    function bar(a) {
        i = 3; // changing the `i` in the enclosing scope's
        // for-loop
        console.log(a + i);
    }
    for (var i = 0; i < 10; i++) {
        bar(i * 2); // oops, inifinite loop ahead!
    }
}
foo();
```

## What Is the Principle of Least Privilege? 

The principle of least privilege (PoLP) is an information security concept which maintains that a user or entity should only have access to the specific data, resources and applications needed to complete a required task.

## TurboFan

TurboFan is one of V8’s optimizing compilers leveraging a concept called “Sea of Nodes”.

## JavaScript Shadowing Concept
The ability of a variable declared with `let` or `var` to shadow another variable depends on their **scope**. Here's a detailed explanation:


### 1. **Can a `let` variable shadow a `var` variable?**
Yes, a `let` variable can shadow a `var` variable if they are in different scopes. For example:

```javascript
var x = 10; // Global scope

function example() {
    let x = 20; // Local (block) scope shadows the global `x`
    console.log(x); // 20
}

example();
console.log(x); // 10
```

**Key Points**:
- The `let` variable inside the function or block scope shadows the `var` variable in the outer/global scope.
- The `var` variable remains accessible outside the block or function where `let` is defined.


### 2. **Can a `var` variable shadow a `let` variable?**
Yes, a `var` variable can shadow a `let` variable if they are in different scopes. However, this is less common and can create confusing behavior.

```javascript
let y = 30; // Global scope

function example() {
    var y = 40; // Local (function) scope shadows the global `y`
    console.log(y); // 40
}

example();
console.log(y); // 30
```

**Key Points**:
- The `var` variable inside the function shadows the `let` variable in the outer/global scope.
- Outside the function, the `let` variable remains unaffected.


### 3. **Block Scope vs. Function Scope**
- Variables declared with `let` are block-scoped. They only exist within the block `{}` where they are declared.
- Variables declared with `var` are function-scoped. They are scoped to the nearest function or global context.

Example with block scope:

```javascript
var z = 50;

if (true) {
    let z = 60; // Block scope shadows the outer `var` z
    console.log(z); // 60
}

console.log(z); // 50
```


### 4. **Temporal Dead Zone**
When using `let`, it’s important to note that a **Temporal Dead Zone (TDZ)** exists, during which the variable cannot be accessed before its declaration. This is not true for `var`.

Example:

```javascript
console.log(a); // undefined (var is hoisted)
var a = 10;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 20;
```


### Conclusion
- A `let` variable can shadow a `var` variable, and vice-versa, depending on their scope.
- `let` has block scope, while `var` has function scope.
- Be cautious when mixing `let` and `var` as it can lead to confusing and error-prone code.

## Closure (Computer Programming)

According to wikipedia:

`In programming languages, a closure, also lexical closure or function closure, is a technique for implementing lexically scoped name binding in a language with first-class functions. Operationally, a closure is a record storing a function[a] together with an environment.[1] The environment is a mapping associating each free variable of the function (variables that are used locally, but defined in an enclosing scope) with the value or reference to which the name was bound when the closure was created.[b] Unlike a plain function, a closure allows the function to access those captured variables through the closure's copies of their values or references, even when the function is invoked outside their scope.`

## Name binding

According to wikipedia:

`In programming languages, name binding is the association of entities (data and/or code) with identifiers.[1] An identifier bound to an object is said to reference that object. Machine languages have no built-in notion of identifiers, but name-object bindings as a service and notation for the programmer is implemented by programming languages. Binding is intimately connected with scoping, as scope determines which names bind to which objects – at which locations in the program code (lexically) and in which one of the possible execution paths (temporally).`

In Python, all functions are first-class, just like in JavaScript.

## 🌟 Are Functions in Java First-Class Citizens? 🤔

No, **functions in Java are not first-class citizens**. In Java, a first-class citizen (or first-class object) is an entity that can:

1. Be assigned to a variable.
2. Be passed as an argument to a method.
3. Be returned from a method.

Although Java does not support first-class functions natively, starting from **Java 8**, it introduced **lambda expressions** and the **`java.util.function`** package, which provide some functional programming capabilities. While these features simulate first-class functions, they are still implemented as objects under the hood, not true functions.

### Explanation:

1. **Before Java 8:**
   - Functions couldn't be passed as arguments or returned from methods.
   - Developers used interfaces or abstract classes to simulate such behavior.

2. **Java 8 and Beyond:**
   - **Lambda expressions** allow you to create anonymous functions:
     ```java
     Function<Integer, Integer> square = x -> x * x;
     System.out.println(square.apply(5)); // Output: 25
     ```
   - The `Function` interface is a part of the `java.util.function` package, which provides a way to treat behavior as data.
   - Lambda expressions and method references make Java more expressive, but the functions are still wrapped as objects.

3. **Key Difference:**
   - In true first-class function support (e.g., in Python or JavaScript), functions are standalone entities.
   - In Java, functions are represented by objects (e.g., instances of functional interfaces like `Function`, `Consumer`, etc.).

### Conclusion:
Functions in Java are not first-class citizens because they cannot exist independently of a class or interface. However, with the introduction of lambda expressions and functional interfaces, Java provides tools to emulate first-class function behavior effectively.

## Interesting Closure Javascript Code

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
    
    function f5 (newValue) {
        i = newValue;
    }
    
    return {
        f1: f1, f2: f2, f3: f3, f4: f4, f5: f5
    };
}

var func = myLoop();

func.f1(); //3
func.f2(); //3
func.f5(200);
func.f3(); // 200
func.f4(); // 200
```

### Why f3 and f4 print 200 ?

This is due to the concept of closure. All f1, f2, f3, f4 and f5 share the same lexical scope. When there is a change occur due to one function, then all other functions will see it due to shared lexical scope.

## ✨ Mastering JavaScript: Implementing Debounce for Efficient Event Handling 🚀

Debouncing is a programming technique used to control how often a particular function is executed. It's especially useful in scenarios where a function might be called multiple times in quick succession. This is commonly seen with events like window resizing, scrolling, or key presses, where you only want the function to run once after the action completes.

Example Use Case: Consider a text input field where you want to send an API request as the user types. Without debouncing, a request might be sent with every keystroke, potentially overwhelming the server. By using a debounce function, you can ensure the API call is made only after the user has stopped typing for a specific duration (e.g., 300 milliseconds).

### How Debounce Works:

- A debounce function takes a function and a delay in milliseconds as arguments.
- When the debounced function is called, it waits for the specified delay.
- If another call to the debounced function is made within this delay, the timer resets.
- The original function is only executed after the delay period passes since the last call.

### How to Implement

1. **Define a Function:** Create a function called `debounce` that will take two parameters: the function you want to execute (`func`) and the delay period in milliseconds (`delay`).

2. **Manage the Timeout:** Inside the `debounce` function, use a variable (`timeoutId`) to keep track of the timeout. This ensures you can reference and clear it anytime the debounced function is called again.

3. **Return a Closure:** The `debounce` function returns a new function. This function:
   - Clears the existing timeout using `clearTimeout(timeoutId)`.
   - Starts a new timer using `setTimeout`, scheduling the execution of `func` after the specified delay.
   - If the function is called again before the delay is over, it cancels the previous timeout and resets the delay.

4. **Execute with Delay:** Once the delay period passes without another call, the original function (`func`) is executed with the context and arguments that were provided in the last call.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Debounce Example</title>
</head>

<body>
    <input type="text" id="search-input" placeholder="Type to search..." />

    <script>
        // Debounce function
        function debounce(func, delay) {
            let timeoutId; // Variable to store the timeout ID
            return function (...args) { // Returning a function
                clearTimeout(timeoutId); // Clear previous timeout
                timeoutId = setTimeout(() => { // Set a new timeout
                    func.apply(this, args); // Execute the function with the given context and arguments
                }, delay);
            };
        }

        // Function to simulate fetching search results
        function fetchSearchResults(query) {
            console.log(`Fetching results for: ${query}`);
            // Imagine this function makes an API call to fetch results
        }

        // Debounced version of the fetchSearchResults function
        const debouncedFetch = debounce(fetchSearchResults, 500);

        // Event listener for the search input
        document.getElementById('search-input').addEventListener('input', (event) => {
            // Call the debounced function with the current input value as the query
            debouncedFetch(event.target.value);
        });
    </script>
</body>

</html>
```

In a nutshell, debounce ensures that the target function is not called too frequently and only executes after a specified "quiet" period.

## Very crucial Line that clears all your doubts regarding the Interface in Java

https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html

According to java docs :
`In the Java programming language, an interface is a reference type, similar to a class, that can contain only constants, method signatures (abstract method), default methods, static methods, and nested types. Method bodies exist only for default methods and static methods. Interfaces cannot be instantiated—they can only be implemented by classes or extended by other interfaces.`

Default methods are defined with the default modifier, and static methods with the static keyword. All abstract, default, and static methods in an interface are implicitly public, so you can omit the public modifier.

## Some Important points related to Javascript Closures

- The engine checks if there is at least one reference to the local environment of a function exiting, outside the function. If there is, the local environment is not garbage-collected.
- A function along with its lexical environment is collectively called a closure.
- The term 'lexical' simply means 'source code' or in other words, 'relating to the text of a program'.
- The lexical environment for a function `f` simply refers to the environment enclosing that function's definition in the source code.
- The lexical environment of a function can contain multiple environments.
  
## Environment Record in Javascript

An Environment Record in JavaScript is an abstract concept used by the JavaScript engine to manage the execution context of code. It's part of the underlying engine implementation of how variable scope and context are managed.

Here’s a simple breakdown:

1. **Role**: Environment Records keep track of the bindings (associations between variable names and values) within a particular execution context. They are created for functions, blocks, and the global scope.

2. **Types of Environment Records**:
   - **Declarative Environment Record**: Used for functions, blocks, and other declarative code structures, holding variables, constants, function arguments, and other declarations.
   - **Object Environment Record**: Often used for global variables, where bindings are associated with properties of an object, like the `window` object in browsers.

3. **Structure**: An Environment Record doesn’t exist as a directly accessible object like other objects in JavaScript. Instead, it is part of the execution context and used internally by the JavaScript engine.

4. **Closures**: Environment Records play a crucial role in the behavior of closures, as they allow a function to remember the variables outside of its immediate scope even after that function has executed.

In essence, Environment Records are a behind-the-scenes mechanism that the JavaScript engine uses to manage variable scope, track values, and handle functions, which are critical components to ensure the correctness of JavaScript execution.

## Difference between addEventListener and onclick in JavaScript

The `addEventListener()` method of the `EventTarget` interface sets up a function that will be called whenever the specified event is delivered to the target.

While addEventListener() allows multiple event listeners and better control over event propagation, onclick is limited to a single event handler and gets overwritten.

### Can addEventListener handle multiple events on the same element?
Yes, addEventListener() allows you to attach multiple event handlers to the same element without overwriting previous handlers.

### Does onclick overwrite existing event handlers?
Yes, onclick overwrites any existing event handlers when you assign a new function to it.

## What is HTMLElement in Web API ?


The **`HTMLElement`** is a core part of the **Web API** and represents any **HTML element** in the DOM (Document Object Model). It is the base class for all HTML element types, such as `<div>`, `<span>`, `<button>`, and custom elements.

When you create or manipulate HTML elements in JavaScript, they are often instances of the `HTMLElement` class or one of its subclasses.

---

### **Key Features of `HTMLElement`:**

1. **Inheritance:**
   - `HTMLElement` is a subclass of the `Element` interface, which itself is a subclass of `Node`.
   - This hierarchy means that `HTMLElement` inherits properties and methods from `Element`, `Node`, and `EventTarget`.

   ```plaintext
   EventTarget → Node → Element → HTMLElement → Specialized HTML Elements
   ```

   For example:
   - `<div>`: Instance of `HTMLDivElement`, which extends `HTMLElement`.
   - `<img>`: Instance of `HTMLImageElement`, which extends `HTMLElement`.

2. **Creating Custom HTML Elements:**
   `HTMLElement` serves as the base class when creating custom elements using **Web Components**.

   ```javascript
   class MyCustomElement extends HTMLElement {
       constructor() {
           super(); // Call the parent constructor
           console.log('Custom element created!');
       }
   }
   customElements.define('my-element', MyCustomElement);
   ```

3. **Commonly Used Properties:**
   `HTMLElement` defines several properties you can use to interact with or manipulate elements:
   - **`innerHTML`**: Gets or sets the HTML content inside the element.
   - **`outerHTML`**: Gets or sets the HTML of the element itself and its descendants.
   - **`style`**: Access the inline styles of the element.
   - **`className`**: Gets or sets the `class` attribute of the element.
   - **`id`**: Gets or sets the `id` attribute of the element.
   - **`hidden`**: Hides or shows the element.
   - **`dataset`**: Access custom data attributes (`data-*`).

4. **Commonly Used Methods:**
   - **`focus()`**: Brings focus to the element.
   - **`click()`**: Simulates a mouse click on the element.
   - **`blur()`**: Removes focus from the element.

5. **Event Handling:**
   - Being a subclass of `EventTarget`, `HTMLElement` can listen for events like `click`, `mouseenter`, `keydown`, etc.

   ```javascript
   const button = document.querySelector('button');
   button.addEventListener('click', () => alert('Button clicked!'));
   ```

---

### **Practical Example:**

```javascript
// Select an existing element
const div = document.querySelector('div');

// Access HTMLElement properties
div.innerHTML = 'Hello, World!'; // Set HTML content
div.style.color = 'blue';        // Change text color
div.className = 'highlight';     // Add a CSS class

// Create a new element dynamically
const button = document.createElement('button');
button.textContent = 'Click Me';
button.addEventListener('click', () => alert('Button clicked!'));

// Append the new button to the div
div.appendChild(button);
```


## JavaScript Interview Prep: Functions, Closures, Currying

### Q1)What is function declaration ?

```javascript
function square(num) {
    return num * num;
}
```

### Q2)What is function expression ?

```javascript
// This function doesn't have a name, hence this is an anonymous function
const square = function(num) {
    return num * num;
}
```

### Q3)What are First-class function ?

```javascript
function square(num) {
    return num * num;
}

function displaySquare(fn, num) {
    console.log("Square of " + num + " is:" + fn(num));
}

displaySquare(square, 101);
```

### Q4)What is Immediately Invoked Function Expression (IIFE) ?

```javascript
console.log(function square(num) {
    return num * num;
}(10))
```

### Q5)What is IIFE ? - O/P based question

```javascript
// Also there is cocept of closure, and currying.
(function(x) {
    return (function(y) {
        console.log(x);
    })(2)
})(1)
```

### Q6) What is Scope ?

```javascript
// The following variables are defined in the global scope
const num1 = 20;
const num2 = 3;
const name = "Chamakh";

// This function is defined in the global scope
function multiply() {
  return num1 * num2;
}

console.log(multiply()); // 60

// A nested function example
function getScore() {
  const num1 = 2;
  const num2 = 3;

  function add() {
    return `${name} scored ${num1 + num2}`;
  }

  return add();
}

console.log(getScore()); // "Chamakh scored 5"
```

### Q7) What is Function Scope ?  - O/P based question

```javascript
for(var i = 0; i < 5; i++) {
    setTimeout(() => {
        console.log(i)
    }, i * 1000);
}
```

### Q8) What is Function Hoisting ?

```javascript
func(); // Work fine

function func() {
    console.log("HELLO");
}
```

```javascript
console.log(x); // undefined
var x = 10;
```

### Q9) What is Function Hoisting ? - O/P based Question (Tricky Question)

```javascript
var x = 21;

var fun = function () {
    console.log(x);
    var x = 20;
}

fun();
```

### Q10) Params vs Arguments

```javascript
function square(num) // Param
{ 
    return num * num;
}
square(10); // Argument
```

### Q10) Params vs Arguments -- Concept of Spread and Rest Operator

```javascript
function multiply(...nums) // rest operator
{
   return nums[0] * nums[1];
}
var nums = [5, 6];
multiply(...nums); // spread operator
```

### Q11) Params vs Arguments -- Concept of Spread and Rest Operator

The following fucntin give syntax error. ==> Rest operator should be at the end of the function argument list.
```javascript
const fn = (a, ...num, x, y) {
    console.log(x + " " + y);
}

fn(5, 6, 3, 7);
```

### Q12) What are callback functions ?
Can you give example where callback functions are used.
map, filter, reduce, setTimeout, Event Listeners (addEventListener)

### Q13) What are arrow functions ?

Arrow functions in JavaScript are a more concise way to write function expressions. They were introduced in ES6 (ECMAScript 2015) and have a syntax that is shorter and easier to understand, especially for simple and one-liner functions.

Here are some key features of arrow functions:

1. **Syntax**: Arrow functions use the `=>` syntax, which makes them more concise. For example:

   ```javascript
   // Regular function expression
   const add = function(a, b) {
       return a + b;
   };

   // Arrow function
   const add = (a, b) => a + b;
   ```

2. **Implicit Returns**: If the function body contains only a single expression, the arrow function can omit the `return` keyword and the curly braces.

   ```javascript
   const square = x => x * x;
   ```

3. **Lexical `this`**: Arrow functions do not have their own `this` context but inherit `this` from the enclosing scope. This makes them particularly useful in situations where you want to preserve the context of `this`, such as within callback functions.

   ```javascript
   function Person() {
       this.age = 0;
       setInterval(() => {
           this.age++; // `this` refers to the Person object
       }, 1000);
   }

   const person = new Person();
   ```

4. **No `arguments` object**: Arrow functions do not have their own `arguments` object. If you need to access the `arguments` object, you must use a regular function.

5. **Usage**: Arrow functions are commonly used in array methods like `map`, `filter`, and `reduce`, as well as in event handlers and promise chaining.

#### Question

```javascript
let user = {
    username: "JOHN DOE",
    f1: () => {
        console.log("f1 = " + this.username);
    },
    f2() {
        console.log("f2 = " + this.username);
    }
};

user.f1();
user.f2();
```

### Q14) What is a closure ?

```javascript
let count = 0;
(function printCount() {
    if(count === 0) {
        // let is block-scoped.
        let count = 1;
        console.log(count); // 1
    }
    console.log(count); // 0
})();
```

```javascript
function createBase(base) {
    return function(num) {
        return base + num;
    }
}

var addSix = createBase(6);
console.log(addSix(10));
console.log(addSix(21));
```

##### Time Optimization

```javascript
function find() {
    let a = [];
    for(let i = 0; i < 1000000; i++) {
        a[i] = i * i;
    }
    
    return function(index) {
        console.log(a[index]);
    }
}
const closure = find();
closure(50);
closure(120);
```

##### How would you use closure to create a private counter ?

```javascript
function counter() {
    var _counter = 0;
    
    function add(increment) {
        _counter += increment;
    }
    
    function getCounter() {
        return _counter;
    }
    
    return {add, getCounter };
}

var c = counter();
c.add(100);
console.log(c.getCounter());
c.add(200);
console.log(c.getCounter());
```

##### What is a Module Pattern

The module pattern in JavaScript is a structural design pattern used to encapsulate code into separate, independent units with private and public APIs. It helps organize and structure code, making it more readable, reusable, and maintainable. By using the module pattern, you can create private variables and functions that are not accessible from the outside, while exposing only the parts of the module you want to be publicly available.

### Key Features of the Module Pattern

1. **Encapsulation**: The module pattern allows you to encapsulate private data and functions, exposing only those you choose to be part of the module's public API.

2. **Scope**: It leverages JavaScript's function scope to create private members. The module is usually defined as an IIFE (Immediately Invoked Function Expression) that returns an object with the public interface.

3. **Example Usage**:

   ```javascript
   const myModule = (function() {
       // Private variables and functions
       let privateVariable = 'I am private';

       function privateFunction() {
           console.log(privateVariable);
       }

       // Public API
       return {
           publicMethod: function() {
               console.log('I am a public method');
               privateFunction();
           },
           anotherPublicMethod: function() {
               console.log('Another public method');
           }
       };
   })();

   myModule.publicMethod(); // Outputs: I am a public method
                            //          I am private
   // myModule.privateFunction(); // Not accessible
   ```

4. **Advantages**:
   - **Namespace Management**: Helps avoid polluting the global namespace by encapsulating code within modules.
   - **Data Privacy**: Secures certain data and logic from being accessed or modified from outside the module.

5. **Variants**: The revealing module pattern is a variant where functions and variables are defined within the closure and assigned to the returned object to make the API more clear.

The module pattern is foundational for modern JavaScript application architectures, often preceding modules in ECMAScript 6 (ES6).

##### Make this run only once

A function that runs only once can be created using a technique called "function wrapping". This involves defining a function and using a closure to ensure that it can only be executed one time. Here's a simple example of how you can achieve this:

```javascript
function once(fn) {
    let hasBeenCalled = false;

    return function(...args) {
        if (!hasBeenCalled) {
            hasBeenCalled = true;
            return fn(...args);
        }
    };
}

// Usage
const initialize = once(() => {
    console.log('This will run only once');
});

// Test
initialize(); // Output: This will run only once
initialize(); // No output
```

### How It Works

- **Closure**: The `once` function returns another function that maintains a private `hasBeenCalled` variable. This variable is part of the closure and retains its value between invocations.

- **Condition Check**: Each time the returned function is called, it checks if `hasBeenCalled` is `false`. If so, it calls the original function and sets `hasBeenCalled` to `true`.

- **Subsequent Calls**: On subsequent calls, since `hasBeenCalled` is now `true`, the function does nothing.

This pattern can be particularly useful in scenarios where you need to ensure that an initialization function or event handler is executed only once.

## Close the sidebar by checking the click happended outside of the sidebar

```javascript
var sidebarOpen = false;
var sidebar = document.getElementById("sidebar");
var openMenu = document.getElementById("openMenu");
function openSidebar() {
    if (!sidebarOpen) {
        sidebar.classList.add("sidebar-responsive");
        sidebarOpen = true;
    }
}

function closeSidebar() {
    if (sidebarOpen) {
        sidebar.classList.remove("sidebar-responsive");
        sidebarOpen = false;
    }
}

document.addEventListener('click', function (event) {
    if (!sidebar.contains(event.target) && !openMenu.contains(event.target)) {
        // console.log("Clicked outside of the sidebar.");
        sidebar.classList.remove("sidebar-responsive");
        sidebarOpen = false;
    }
});
```

## Bootstrap Dropdown Toggling

In Bootstrap, the `data-toggle` attribute is used to enable certain JavaScript functionality for components without writing custom JavaScript code. For dropdowns, `data-toggle="dropdown"` is specifically used to make a button or link toggle the visibility of a dropdown menu when clicked.

Here’s how it works:

### Example
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bootstrap Dropdown Example</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
  <div class="dropdown">
    <button class="btn btn-primary dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">
      Dropdown Button
    </button>
    <ul class="dropdown-menu">
      <li><a class="dropdown-item" href="#">Action</a></li>
      <li><a class="dropdown-item" href="#">Another action</a></li>
      <li><a class="dropdown-item" href="#">Something else here</a></li>
    </ul>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### Key Points:
1. **`data-bs-toggle="dropdown"` (Bootstrap 5+):**
   - This attribute specifies that the button should toggle the dropdown menu.
   - When clicked, the dropdown is shown or hidden automatically.

2. **Bootstrap 4 vs. 5:**
   - In Bootstrap 4, it was `data-toggle="dropdown"`.
   - In Bootstrap 5 and later, it’s updated to `data-bs-toggle="dropdown"`.

3. **Automatic Behavior:**
   - By adding this attribute, you don’t need to write JavaScript to show/hide the dropdown. Bootstrap's built-in JavaScript handles it.

4. **Additional Attributes:**
   - **`aria-expanded="false"`**: Provides accessibility, indicating the dropdown state.
   - **`class="dropdown-toggle"`**: Adds styling to indicate the element is interactive.


### Learn patterns in frontend development like closing a sidebar by clicking outside of the sidebar.

## Best Javascript Article

https://medium.com/no-nonsense-backend/8-javascript-performance-tips-i-discovered-after-years-of-coding-56ab5fae43af

## Javascript Closure best Article

https://www.codeguage.com/courses/js/functions-closures

## Javascript Roadmap

https://roadmap.sh/javascript

## Difference between Observables and Promises

https://stackoverflow.com/questions/37364973/what-is-the-difference-between-promises-and-observables

Observables are called "lazy" because they only begin to execute their logic when a subscriber actively subscribes to them, meaning no actions are taken until someone is actively listening for data, unlike a Promise which starts executing immediately regardless of whether anyone is listening; this allows for efficient data handling and building complex data pipelines through chaining operators before actual execution occurs.

## `JpaSpecificationExecutor` Overview

### 1. **Introduction:**
"`JpaSpecificationExecutor` is an interface provided by Spring Data JPA that allows developers to write dynamic and complex queries using specifications rather than traditional JPQL or native SQL queries."

---

### 2. **Why Use JpaSpecificationExecutor?**
- **Dynamic Queries:** In real-world applications, query requirements often depend on user input (e.g., multiple optional filters in search forms). 
- **Separation of Concerns:** Keeps query logic separate from the business logic, improving code readability and maintainability.
- **Type Safety:** Unlike JPQL, it offers type-safe querying.

---

### 3. **Key Components:**
To use `JpaSpecificationExecutor`, you need:
1. **Entity Class:** Define a typical JPA entity.
2. **Repository:** Extend both `JpaRepository` and `JpaSpecificationExecutor`.

    ```java
    public interface EmployeeRepository 
        extends JpaRepository<Employee, Long>, JpaSpecificationExecutor<Employee> {
    }
    ```

3. **Specification:** Implement a `Specification` using the builder pattern.

---

### 4. **Example Use Case:**
Suppose we have an `Employee` entity, and we want to search by multiple optional criteria: name, department, and joining date.

#### **Specification Implementation**
```java
public class EmployeeSpecification {

    public static Specification<Employee> hasName(String name) {
        return (root, query, criteriaBuilder) ->
            name == null ? null : criteriaBuilder.equal(root.get("name"), name);
    }

    public static Specification<Employee> belongsToDepartment(String department) {
        return (root, query, criteriaBuilder) ->
            department == null ? null : criteriaBuilder.equal(root.get("department"), department);
    }

    public static Specification<Employee> joinedAfter(LocalDate date) {
        return (root, query, criteriaBuilder) ->
            date == null ? null : criteriaBuilder.greaterThanOrEqualTo(root.get("joiningDate"), date);
    }
}
```

#### **Service Layer Usage**
```java
@Service
public class EmployeeService {
    private final EmployeeRepository employeeRepository;

    public EmployeeService(EmployeeRepository employeeRepository) {
        this.employeeRepository = employeeRepository;
    }

    public List<Employee> searchEmployees(String name, String department, LocalDate joiningDate) {
        Specification<Employee> spec = Specification.where(EmployeeSpecification.hasName(name))
            .and(EmployeeSpecification.belongsToDepartment(department))
            .and(EmployeeSpecification.joinedAfter(joiningDate));
        
        return employeeRepository.findAll(spec);
    }
}
```

---

### 5. **Best Practices:**
- **Modular Specifications:** Create reusable specifications to promote clean and maintainable code.
- **Error Handling:** Ensure robust handling when input criteria are null or invalid.
- **Performance Consideration:** Combine only the necessary criteria to avoid inefficient database queries.

---

### 6. **Follow-up Discussion:**
- "Can you explain a scenario where you've optimized a complex dynamic query using `JpaSpecificationExecutor`?"  
- "How would you compare it with QueryDSL or Criteria API for dynamic query building?"

## Building Dynamic Queries with Spring Data JPA Specifications: A Flexible Approach to Data Retrieval

#### Example: Dynamic Search for Products in an E-Commerce Application

Let’s say you’re building an e-commerce application, and you want to create a flexible search mechanism for products based on various filters such as price range, category, availability, and rating. Some filters may be optional, and you only apply the ones that are provided. If no filters are passed, it will simply fetch all the data.

Here’s how you can use Spring Data JPA Specifications to achieve this:

```java
private Specification<Product> productSpecification(
        String category,
        Double minPrice,
        Double maxPrice,
        Boolean inStock,
        Double minRating) {
    return (Root<Product> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) -> {
        List<Predicate> predicates = new ArrayList<>();

        // Add category filter
        if (category != null && !category.isEmpty()) {
            predicates.add(criteriaBuilder.equal(root.get("category"), category));
        }

        // Add price range filter
        if (minPrice != null) {
            predicates.add(criteriaBuilder.greaterThanOrEqualTo(root.get("price"), minPrice));
        }
        if (maxPrice != null) {
            predicates.add(criteriaBuilder.lessThanOrEqualTo(root.get("price"), maxPrice));
        }

        // Add in-stock filter
        if (inStock != null) {
            predicates.add(criteriaBuilder.equal(root.get("inStock"), inStock));
        }

        // Add rating filter
        if (minRating != null) {
            predicates.add(criteriaBuilder.greaterThanOrEqualTo(root.get("rating"), minRating));
        }

        // If no filters are applied, fetch all data
        if (predicates.isEmpty()) {
            return criteriaBuilder.isTrue(criteriaBuilder.literal(true)); // Return all rows
        }

        // Combine all predicates with AND operator
        return criteriaBuilder.and(predicates.toArray(new Predicate[0]));
    };
}

// Usage of the specification
Specification<Product> spec = productSpecification("Electronics", 100.0, 500.0, true, 4.0);
List<Product> filteredProducts = productRepository.findAll(spec);

// If no filter values are provided (all null), it will fetch all products
Specification<Product> allProductsSpec = productSpecification(null, null, null, null, null);
List<Product> allProducts = productRepository.findAll(allProductsSpec);
```

#### Explanation:
- **Flexible Filters**: You can dynamically apply filters (like price range, availability, category, and rating) based on the values passed.
- **Fetch All Data**: If no filters are provided (i.e., when all parameters are `null`), the specification will return all the rows by using a `true` predicate (`criteriaBuilder.isTrue`).
- **Specifications**: This approach allows combining multiple conditions with AND/OR logic dynamically, offering a clean and powerful solution.
- **Real-world Application**: This kind of search functionality is common in e-commerce platforms where users can filter products based on different criteria. When no filter is applied, the system defaults to showing all available data.

This modification emphasizes the use of Specifications not only for dynamic filtering but also for handling scenarios where no filters are applied, allowing your students to see how to fetch all data when necessary.

## 🚀 Implementing Two Interfaces with Same Method Name but Different Parameter Types in Java 21 🛠️

```java
interface InterfaceA {
    void doSomething(int x);
}

interface InterfaceB {
    void doSomething(String x);
}


class MyClass implements InterfaceA, InterfaceB {

    // Implementation for the method from InterfaceA
    @Override
    public void doSomething(int x) {
        System.out.println("doSomething with int: " + x);
    }

    // Implementation for the method from InterfaceB
    @Override
    public void doSomething(String x) {
        System.out.println("doSomething with String: " + x);
    }
    
}

class Main {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        
        obj.doSomething(10);        // Calls doSomething(int)
        obj.doSomething("Hello");   // Calls doSomething(String)
    }
}
```

### Example 2

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;
public interface EmployeeRepository extends JpaRepository<Employee, Integer>, JpaSpecificationExecutor<Employee> {}
```

- `org.springframework.data.jpa.repository.JpaSpecificationExecutor` => `List<T> findAll(Specification<T> spec);` `Page<T> findAll(Specification<T> spec, Pageable pageable)` `List<T> findAll(Specification<T> spec, Sort sort);`
- `org.springframework.data.jpa.repository.JpaSpecificationExecutor` => `<S extends T> List<S> findAll(Example<S> example);` `<S extends T> List<S> findAll(Example<S> example, Sort sort);`

## Automating Row Duplication and Timestamp Updates in MySQL

```sql
INSERT INTO `poc-school`.tenure (created_on, some_column, some_date, some_column, some_column, some_column, some_date, some_column, updated_on, foreign_key_id)
SELECT NOW(), deleted, end_date, freeze, pinned, position, start_date, title, NOW(), 2
FROM `my-schema`.table
WHERE foreign_key_id = 1;
```

## Inline, Block, and inline-block in CSS

- No margins, width, height, have not effect on inline HTML elements. font-size have effect.
- What happens when you need inline elements that need margins and paddings ? Here comes `inline-block`
- `img` is an inline element, but we can also apply width property on it.

## Why are my div elements stacking vertically instead of displaying side by side?

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        .box1{
            background-color: red;
            width: 20%;
        }
        .box2{
            background-color: blue;
            width: 20%;
        }
    </style>
</head>
<body>
    <div class="box1">ABC</div>
    <div class="box2">DEF</div>
</body>
</html>
```

The reason the two div elements are stacking on top of each other rather than appearing side by side is that div elements are block-level elements by default. Block-level elements take up the full width of the container and will stack vertically.

## Why CSS `overflow` property is crucial in creating dropdowns ? 

## Crucial CSS Articles

https://stackoverflow.com/questions/28353625/why-does-percentage-width-work-even-if-no-explicit-width-value-given-for-contain/28354270#28354270

## How to Check for Foreign Key Constraints and ON DELETE CASCADE in MySQL?

```sql
SELECT 
    CONSTRAINT_NAME,
    TABLE_NAME,
    REFERENCED_TABLE_NAME,
    DELETE_RULE
FROM 
    INFORMATION_SCHEMA.REFERENTIAL_CONSTRAINTS
WHERE 
    CONSTRAINT_SCHEMA = 'poc-school'
    AND TABLE_NAME = 'food_category';
```

## Unit of Work in Repository Pattern

- https://dotnettutorials.net/lesson/unit-of-work-csharp-mvc/#google_vignette
- https://learn.microsoft.com/en-us/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application

## Implementing the Outbox Pattern
- https://www.milanjovanovic.tech/blog/implementing-the-outbox-pattern
- https://www.milanjovanovic.tech/blog/scaling-the-outbox-pattern?utm_source=LinkedIn&utm_medium=social&utm_campaign=27.01.2025
- https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html

## Specification Pattern
- https://medium.com/@carlosraphael/specification-design-pattern-in-java-8-bac6f5f943bc

## Factory Pattern
- https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/converters-how-to

## Writing your own Custom JSON Serializer in .NET 6

you can use a **custom serializer** in your C# .NET 6 console application. To do this, you can configure and use the **`System.Text.Json`** serializer (built into .NET) and provide your own custom serialization logic using converters like `JsonConverter`. This gives you the ability to control how specific types are serialized and deserialized.

### Steps to Use a Custom Serializer in a .NET 6 Console Application:

1. **Create a Custom Converter**: You define a custom converter by inheriting from `JsonConverter<T>` where `T` is the type you want to customize the serialization for.
  
2. **Register the Custom Converter**: You register the custom converter either globally by passing it to the `JsonSerializerOptions` or apply it to specific properties using the `[JsonConverter]` attribute.

3. **Use `System.Text.Json` for Serialization and Deserialization**: This involves calling `JsonSerializer.Serialize()` and `JsonSerializer.Deserialize()` with the custom options.

### Example of Using a Custom Serializer

Let’s create a custom serializer for a `DateTime` property with a specific format in a .NET 6 Console app:

#### 1. Create a Custom `JsonConverter`:
```csharp
using System;
using System.Text.Json;
using System.Text.Json.Serialization;

public class CustomDateTimeConverter : JsonConverter<DateTime>
{
    private readonly string _format = "yyyy-MM-dd";

    public override DateTime Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
    {
        return DateTime.ParseExact(reader.GetString(), _format, null);
    }

    public override void Write(Utf8JsonWriter writer, DateTime value, JsonSerializerOptions options)
    {
        writer.WriteStringValue(value.ToString(_format));
    }
}
```

#### 2. Define a Model:
```csharp
public class MyModel
{
    public int Id { get; set; }

    // Apply the custom serializer to this property
    [JsonConverter(typeof(CustomDateTimeConverter))]
    public DateTime CreatedAt { get; set; }
}
```

#### 3. Use the Custom Converter in Serialization/Deserialization:
```csharp
using System;
using System.Text.Json;

class Program
{
    static void Main(string[] args)
    {
        var model = new MyModel
        {
            Id = 1,
            CreatedAt = DateTime.Now
        };

        // Serialize the model to JSON using the custom date format
        var options = new JsonSerializerOptions
        {
            WriteIndented = true
        };

        string jsonString = JsonSerializer.Serialize(model, options);
        Console.WriteLine("Serialized JSON:");
        Console.WriteLine(jsonString);

        // Deserialize back from JSON
        var deserializedModel = JsonSerializer.Deserialize<MyModel>(jsonString);
        Console.WriteLine("\nDeserialized Object:");
        Console.WriteLine($"Id: {deserializedModel.Id}, CreatedAt: {deserializedModel.CreatedAt}");
    }
}
```

#### Output:
```json
Serialized JSON:
{
  "id": 1,
  "createdAt": "2025-02-06"
}

Deserialized Object:
Id: 1, CreatedAt: 2025-02-06 00:00:00
```

### Explanation:
1. **CustomDateTimeConverter**: This custom converter formats the `DateTime` as `yyyy-MM-dd` during serialization and ensures it can be parsed back during deserialization.
2. **[JsonConverter] Attribute**: The custom serializer is applied only to the `CreatedAt` property of the `MyModel` class, but it could be applied globally via `JsonSerializerOptions` if needed.
3. **Serialization/Deserialization**: You use `JsonSerializer.Serialize()` and `JsonSerializer.Deserialize()` with optional formatting options, which makes your console app flexible.

### Using Global Custom Serializer
Instead of using the `[JsonConverter]` attribute on each model, you can globally apply the converter:

```csharp
var options = new JsonSerializerOptions
{
    WriteIndented = true,
    Converters =
    {
        new CustomDateTimeConverter()
    }
};

string jsonString = JsonSerializer.Serialize(model, options);
```

### Conclusion:
In your .NET 6 console app, you can easily implement a custom serializer by creating a custom `JsonConverter` and applying it either globally or on a per-property basis. This gives you full control over the JSON serialization and deserialization process.

## Creating our own custome serializer and apply it on timestamp fields for adding a cooment at the end of the timestamp string in Spring Boot.

## Best icons library
- https://lucide.dev/

## Implement Rate Limiter
- https://foojay.io/today/token-bucket-rate-limiter-redis-java/

## Role-based Access Control vs Attribute-based Access Control

- https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/pep.html
- policy enforcement point (pep)
- https://www.youtube.com/watch?v=rvZ35YW4t5k&list=LL&index=1

## Lexical `this` binding
An arrow function expression (also known as fat arrow function) has a shorter syntax compared to function expressions and lexically binds the this value (does not bind its own this, arguments, super, or new.target). Arrow functions are always anonymous.

## TypeScript-Only Feature: Explicit `this` Parameter

## Fundamental Counting Principle
- https://brilliant.org/wiki/fundamental-counting-principle/#:~:text=The%20fundamental%20counting%20principle%20is,perform%20both%20of%20these%20actions.

## Hot Observable vs Cold Observable

## Build your own Redux
https://zapier.com/engineering/how-to-build-redux/

## Crucial Udemy Courses to learn in future:

- https://www.udemy.com/course/functional-programming-and-reactive-programming-in-java
- https://www.udemy.com/course/design-and-develop-a-killer-website-with-html5-and-css3/
- Advanced CSS and Sass: Flexbox, Grid, Animations and More!

## Derivation of Area of Triangle
https://wumbo.net/examples/derive-area-of-triangle-formula/


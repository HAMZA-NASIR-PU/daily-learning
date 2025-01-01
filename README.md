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


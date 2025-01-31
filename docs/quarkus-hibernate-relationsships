# 🔗 Hibernate Relationships in Quarkus

## 📌 Introduction
Hibernate ORM supports various **entity relationships** to define how objects interact with each other in a relational database. In this guide, we'll explore:

- **One-to-One (`@OneToOne`)**
- **One-to-Many (`@OneToMany`)**
- **Many-to-One (`@ManyToOne`)**
- **Many-to-Many (`@ManyToMany`)**

Each section includes **an explanation** and **an example implementation** using **Hibernate ORM with Panache** in Quarkus.

---

## 1️⃣ **One-to-One Relationship** (`@OneToOne`)
### 🔹 Description
A **one-to-one** relationship means **one entity is associated with exactly one other entity**. Example: A **User** has **one Profile**.

### 🏗 Implementation

```java
import jakarta.persistence.*;
import io.quarkus.hibernate.orm.panache.PanacheEntity;
import com.fasterxml.jackson.annotation.JsonIgnore;

@Entity
public class User extends PanacheEntity {
    public String name;
    
    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "profile_id")
    @JsonIgnore
    public Profile profile;
}

@Entity
public class Profile extends PanacheEntity {
    public String bio;
}
```

### 📌 Key Points
- **`@OneToOne`** links User → Profile.
- **`cascade = CascadeType.ALL`** ensures Profile updates when User changes.
- **`@JoinColumn(name = "profile_id")`** sets the foreign key in `User`.
- **`@JsonIgnore`** prevents infinite recursion during JSON serialization.

---

## 2️⃣ **One-to-Many Relationship** (`@OneToMany`)
### 🔹 Description
A **one-to-many** relationship means **one entity is linked to multiple instances of another entity**. Example: A **Customer** can have **multiple Orders**.

### 🏗 Implementation

```java
import com.fasterxml.jackson.annotation.JsonIgnore;

@Entity
public class Customer extends PanacheEntity {
    public String name;

    @OneToMany(mappedBy = "customer", cascade = CascadeType.ALL)
    @JsonIgnore
    public List<Order> orders;
}

@Entity
public class Order extends PanacheEntity {
    public String product;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    public Customer customer;
}
```

### 📌 Key Points
- **One `Customer` has many `Orders`** (`@OneToMany` on `Customer`).
- **Each `Order` belongs to one `Customer`** (`@ManyToOne` on `Order`).
- **`mappedBy = "customer"`** in `@OneToMany` ensures `Order` owns the foreign key.
- **`@JsonIgnore` on `orders` prevents cyclic references**.

---

## 3️⃣ **Many-to-One Relationship** (`@ManyToOne`)
### 🔹 Description
A **many-to-one** relationship is the reverse of one-to-many. Example: Multiple **Orders** belong to a single **Customer**.

### 🏗 Implementation
_(Same as One-to-Many, but focusing on the `Order` side)_

```java
@Entity
public class Order extends PanacheEntity {
    public String product;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    @JsonIgnore
    public Customer customer;
}
```

### 📌 Key Points
- **Each `Order` has a single `Customer`**.
- **A foreign key `customer_id` is stored in `Order`**.
- **`@JsonIgnore` prevents serialization issues with bidirectional relationships**.

---

## 4️⃣ **Many-to-Many Relationship** (`@ManyToMany`)
### 🔹 Description
A **many-to-many** relationship means **each entity can have multiple associated entities**. Example: A **Student** can enroll in **multiple Courses**, and each **Course** can have multiple Students.

### 🏗 Implementation

```java
@Entity
public class Student extends PanacheEntity {
    public String name;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    @JsonIgnore
    public List<Course> courses;
}

@Entity
public class Course extends PanacheEntity {
    public String title;
    
    @ManyToMany(mappedBy = "courses")
    @JsonIgnore
    public List<Student> students;
}
```

### 📌 Key Points
- **A `Student` can enroll in multiple `Courses`**.
- **A `Course` can have multiple `Students`**.
- **`@JoinTable`** creates a **junction table** (`student_course`).
- **`mappedBy = "courses"`** prevents extra mapping.
- **`@JsonIgnore` on both `courses` and `students` avoids infinite recursion**.

---

## 🎯 Final Summary
| Relationship | Example | Key Annotation |
|-------------|---------|----------------|
| **One-to-One** | User → Profile | `@OneToOne`, `@JsonIgnore` |
| **One-to-Many** | Customer → Orders | `@OneToMany`, `@ManyToOne`, `@JsonIgnore` |
| **Many-to-One** | Orders → Customer | `@ManyToOne`, `@JsonIgnore` |
| **Many-to-Many** | Student ↔ Course | `@ManyToMany`, `@JsonIgnore` |

### ✅ Best Practices
- **Use `cascade = CascadeType.ALL`** if child records should update with parent.
- **Always define `mappedBy`** to avoid extra foreign keys.
- **Use `@JoinTable` for Many-to-Many relationships**.
- **Apply `@JsonIgnore` on bidirectional relationships** to prevent infinite loops in JSON serialization.

---

# 🛠 Hibernate ORM with Mutiny in Quarkus

## 📌 Introduction
Quarkus provides **Hibernate ORM with Panache** to simplify database interactions. When combined with **Mutiny**, it allows fully **reactive, non-blocking** CRUD operations.

This guide covers:
- **How Hibernate ORM integrates with Mutiny**
- **Setting up a reactive entity and repository**
- **Creating a CRUD service with Mutiny**
- **Exposing a REST API using Quarkus**

---

## 🚀 Why Use Hibernate ORM with Mutiny?
### 🔹 Benefits:
✅ **Non-blocking**: Uses `Uni` & `Multi` for async database queries.  
✅ **Better scalability**: Frees up threads while waiting for DB responses.  
✅ **Easy integration**: Works seamlessly with Quarkus.

### 🔹 Requirements:
1. **Quarkus** (with Hibernate ORM and Mutiny dependencies)
2. **Reactive Database Driver** (e.g., PostgreSQL, MySQL)
3. **Panache for simplified ORM handling**

---

## 📦 1️⃣ Setup Dependencies
Add the following dependencies to your `pom.xml`:

```xml
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-hibernate-reactive-panache</artifactId>
</dependency>

<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-reactive-pg-client</artifactId>
</dependency>
```

---

## 🏗 2️⃣ Define a Reactive Entity
Create an entity class `Todo` using **PanacheEntity**:

```java
import io.quarkus.hibernate.reactive.panache.PanacheEntity;
import jakarta.persistence.Entity;

@Entity
public class Todo extends PanacheEntity {
    public String title;
    public boolean completed;
}
```

### 📌 Why Use `PanacheEntity`?
- Reduces boilerplate code (no need for `@Id` and getters/setters).
- Provides **built-in reactive methods** like `persist()`, `findAll()`, etc.

---

## 📂 3️⃣ Create a Reactive Repository
Implement a repository class `TodoRepository` for **reactive DB operations**:

```java
import io.quarkus.hibernate.reactive.panache.PanacheRepository;
import io.smallrye.mutiny.Uni;
import jakarta.enterprise.context.ApplicationScoped;
import java.util.List;

@ApplicationScoped
public class TodoRepository implements PanacheRepository<Todo> {
    public Uni<List<Todo>> listAllTodos() {
        return listAll(); // Returns Uni<List<Todo>> asynchronously
    }
}
```

---

## 🛠 4️⃣ Create a CRUD Service with Mutiny
Implement a **non-blocking service** using Mutiny:

```java
import io.smallrye.mutiny.Uni;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import java.util.List;

@ApplicationScoped
public class TodoService {
    
    @Inject
    TodoRepository todoRepository;
    
    public Uni<List<Todo>> getAllTodos() {
        return todoRepository.listAllTodos();
    }
    
    public Uni<Todo> addTodo(Todo todo) {
        return todo.persist().replaceWith(todo);
    }
    
    public Uni<Boolean> deleteTodo(Long id) {
        return Todo.deleteById(id);
    }
}
```

### 📌 Explanation:
- **`listAll()`** fetches todos **without blocking the main thread**.
- **`persist()`** asynchronously inserts a new todo.
- **`deleteById()`** removes a record **reactively**.

---

## 🌍 5️⃣ Expose a REST API with Quarkus
Create a `TodoResource` class for **handling HTTP requests**:

```java
import io.smallrye.mutiny.Uni;
import jakarta.inject.Inject;
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import java.util.List;

@Path("/todos")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class TodoResource {

    @Inject
    TodoService todoService;

    @GET
    public Uni<List<Todo>> getAllTodos() {
        return todoService.getAllTodos();
    }

    @POST
    public Uni<Todo> createTodo(Todo todo) {
        return todoService.addTodo(todo);
    }

    @DELETE
    @Path("/{id}")
    public Uni<Boolean> deleteTodo(@PathParam("id") Long id) {
        return todoService.deleteTodo(id);
    }
}
```

---

## 🛠 6️⃣ Configure Database Connection
Add the following **database configuration** to `application.properties`:

```properties
# Configure the database
quarkus.datasource.db-kind=postgresql
quarkus.datasource.username=your_user
quarkus.datasource.password=your_password
quarkus.datasource.reactive.url=postgresql://localhost:5432/your_database

# Enable Hibernate ORM in reactive mode
quarkus.hibernate-orm.database.generation=update
```

---

## 🔥 7️⃣ Test Your Reactive API
### ✅ Create a New Todo
```sh
curl -X POST http://localhost:8080/todos -H "Content-Type: application/json" -d '{
    "title": "Learn Quarkus",
    "completed": false
}'
```

### ✅ Fetch All Todos
```sh
curl -X GET http://localhost:8080/todos
```

### ✅ Delete a Todo
```sh
curl -X DELETE http://localhost:8080/todos/1
```

---

## 🎯 Final Summary
✅ **Reactive Hibernate ORM** makes database access **non-blocking**.
✅ **Mutiny’s `Uni` & `Multi`** enable **scalable, efficient queries**.
✅ **Quarkus + Hibernate ORM Panache** simplifies **CRUD operations**.
✅ **Best for cloud-native applications, microservices, and high-performance APIs**.

---

🔥 **Want to extend this example with `Multi` for streaming data? Let me know! 🚀**

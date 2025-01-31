# Quarkus Project Structure: Mutiny Example

## **📂 Project Structure**
```
src/main/
├── docker/                     # Docker configurations (if needed)
├── java/
│   ├── ac.at.htl/
│   │   ├── dto/                # Data Transfer Objects (DTOs)
│   │   ├── model/              # Hibernate Entities
│   │   │   ├── Todo.java       # Example Panache Entity
│   │   ├── repository/         # Repositories using Panache
│   │   ├── resource/           # REST API Endpoints
│   │   │   ├── TodoResource.java
│   │   ├── service/            # Business logic layer
│   │   │   ├── TodoService.java
│   │   ├── infrastructure/      # External API Fetchers
│   │   │   ├── DummyJsonService.java
├── resources/                  # Configuration files (application.properties, etc.)
├── test/                       # Unit and integration tests
.gitignore
mvnw                          # Maven Wrapper
```

---
## **📌 What is Mutiny in Quarkus?**

Mutiny is a **reactive programming library** used in Quarkus for **non-blocking** API operations.

📌 **Why Use Mutiny?**
- **Asynchronous** & **Non-blocking** → Faster APIs
- **Built into Quarkus** → Works natively
- **Better than `CompletableFuture<T>`**

📌 **Reactive Types in Mutiny**
| **Type**  | **Emits** | **Use Case** |
|-----------|----------|--------------|
| `Uni<T>`  | 1 item (or failure) | Fetching a single entity |
| `Multi<T>`| Multiple items (stream) | Streaming responses (e.g., SSE) |

---
## **🛠️ Complete Todo Example Using Mutiny**

### **📌 Define the Entity (`Todo.java`)**
```java
package ac.at.htl.model;

import io.quarkus.hibernate.orm.panache.PanacheEntity;
import jakarta.persistence.Entity;

@Entity
public class Todo extends PanacheEntity {
    public String title;
    public boolean completed;
}
```

### **📌 Define the Repository (`TodoRepository.java`)**
```java
package ac.at.htl.repository;

import ac.at.htl.model.Todo;
import io.quarkus.hibernate.orm.panache.PanacheRepository;
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class TodoRepository implements PanacheRepository<Todo> {
}
```

### **📌 Define the External Fetcher (`DummyJsonService.java`)**
```java
package ac.at.htl.infrastructure;

import ac.at.htl.dto.TodoDTO;
import io.smallrye.mutiny.Uni;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;
import java.util.List;

@RegisterRestClient
@ApplicationScoped
@Path("/todos")
public interface DummyJsonService {
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    Uni<List<TodoDTO>> fetchTodos();
}
```

### **📌 Define the Service (`TodoService.java`)**
```java
package ac.at.htl.service;

import ac.at.htl.model.Todo;
import ac.at.htl.repository.TodoRepository;
import ac.at.htl.infrastructure.DummyJsonService;
import io.smallrye.mutiny.Uni;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.eclipse.microprofile.rest.client.inject.RestClient;
import java.util.List;
import java.util.stream.Collectors;

@ApplicationScoped
public class TodoService {

    @Inject
    TodoRepository todoRepository;

    @Inject
    @RestClient
    DummyJsonService dummyJsonService;

    public Uni<List<Todo>> getAllTodos() {
        return todoRepository.listAll();
    }

    public Uni<Todo> addTodo(Todo todo) {
        return Uni.createFrom().item(todo)
                .onItem().invoke(todoRepository::persist)
                .onItem().transform(ignore -> todo);
    }

    public Uni<List<Todo>> fetchAndStoreTodos() {
        return dummyJsonService.fetchTodos()
            .onItem().transform(todoDTOs -> todoDTOs.stream()
                    .map(dto -> {
                        Todo todo = new Todo();
                        todo.title = dto.title;
                        todo.completed = dto.completed;
                        return todo;
                    })
                    .collect(Collectors.toList()))
            .onItem().invoke(todoRepository::persist)
            .onItem().transform(ignore -> todoRepository.listAll());
    }
}
```

### **📌 Define the Resource (`TodoResource.java`)**
```java
package ac.at.htl.resource;

import ac.at.htl.model.Todo;
import ac.at.htl.service.TodoService;
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
    public Uni<Todo> addTodo(Todo todo) {
        return todoService.addTodo(todo);
    }

    @GET
    @Path("/fetch-external")
    public Uni<List<Todo>> fetchAndStoreTodos() {
        return todoService.fetchAndStoreTodos();
    }
}
```

---
## **📌 Summary**
✅ **Use Mutiny (`Uni` & `Multi`)** for reactive and non-blocking API calls.  
✅ **Panache Repository** simplifies CRUD operations.  
✅ **Service Layer** ensures separation of concerns.  
✅ **REST API** provides endpoints for fetching and adding todos.  
✅ **Fetch External Data** from `DummyJSON` and store it in the database.

🚀 **Next Steps**: Need help setting up tests or error handling? Let me know! 😊


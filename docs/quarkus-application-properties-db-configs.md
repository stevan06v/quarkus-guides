# Quarkus `application.properties` Configurations

## **📌 Database Schema Management (`drop`, `create`, `validate`, `update`)**

### **️⃣ Drop & Create (Useful for Development)**
```properties
quarkus.datasource.db-kind=mysql
quarkus.datasource.jdbc.url=jdbc:mysql://localhost:3306/tododb
quarkus.datasource.username=root
quarkus.datasource.password=secret
quarkus.hibernate-orm.database.generation=drop-and-create
```
📌 **Explanation:**
- Drops the existing schema and creates a new one **on each application startup**.
- Best used **only for development** to prevent accidental data loss.

---

### **2️⃣ Create Only (Useful for First Time Setup)**
```properties
quarkus.hibernate-orm.database.generation=create
```
📌 **Explanation:**
- Creates the database schema **if it doesn’t exist**.
- Will **not drop** existing data.

---

### **3️⃣ Update (Keeps Existing Data, Applies Schema Changes)**
```properties
quarkus.hibernate-orm.database.generation=update
```
📌 **Explanation:**
- **Keeps existing data** while applying **necessary schema updates**.
- Useful for production, but changes are **not always safe**.

---

### **4️⃣ Validate (Ensures Schema Matches Entity Definitions)**
```properties
quarkus.hibernate-orm.database.generation=validate
```
📌 **Explanation:**
- Checks if the database schema **matches the entity model**.
- **Will not modify the schema** (useful for production safety checks).
- Application **fails to start** if mismatches are found.

---

## **📌 Other Useful Configurations in `application.properties`**

### **1️⃣ Setting Default Transaction Isolation Level**
```properties
quarkus.datasource.jdbc.transactions=repeatable-read
```
📌 **Options:** `read-uncommitted`, `read-committed`, `repeatable-read`, `serializable`

---

### **2️⃣ Enable SQL Logging for Debugging**
```properties
quarkus.hibernate-orm.log.sql=true
quarkus.hibernate-orm.format-sql=true
```
📌 **Shows executed SQL queries in logs**, useful for debugging database interactions.

---

### **3️⃣ Connection Pool Settings**
```properties
quarkus.datasource.jdbc.min-size=5
quarkus.datasource.jdbc.max-size=20
quarkus.datasource.jdbc.idle-timeout=300
```
📌 **Optimizes connection handling for performance.**

---

### **4️⃣ Configure Hibernate Caching**
```properties
quarkus.hibernate-orm.second-level-caching.enabled=true
```
📌 **Improves performance** by caching query results to avoid redundant database calls.

---

### **5️⃣ Enable Metrics for Observability**
```properties
quarkus.smallrye-metrics.enabled=true
```
📌 Exposes metrics (useful for monitoring in production environments).

---

### **6️⃣ Configure External API Timeouts**
```properties
quarkus.rest-client.timeout=5000
quarkus.rest-client.connect-timeout=3000
```
📌 Prevents API calls from **hanging indefinitely**.

---

### **📌 Summary of Best Practices**
| **Configuration** | **Best Use Case** |
|------------------|-----------------|
| `drop-and-create` | Development only (resets schema on startup) |
| `create` | First-time setup, creates schema if missing |
| `update` | Development & testing, updates schema without dropping data |
| `validate` | Production, ensures schema matches entities without modifying |
| SQL Logging | Debugging queries in development |
| Connection Pooling | Performance optimization |
| Caching | Speed up DB queries in production |
| API Timeouts | Prevent API requests from hanging |

Would you like additional security-related configurations? 🚀


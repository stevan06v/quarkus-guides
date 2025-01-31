# Server-Sent Events (SSE) in Quarkus

## 1️⃣ What is SSE (Server-Sent Events)?
**Server-Sent Events (SSE)** is a **unidirectional** real-time communication mechanism that allows a server to push **continuous data updates** to a client **over a single HTTP connection**.

SSE is useful for:
- **Live notifications** (e.g., stock prices, live sports updates)
- **Streaming data** (e.g., Twitter feeds, logs, real-time dashboards)
- **Real-time API responses** (e.g., event-driven microservices)

---
## 2️⃣ How SSE Works
### **Basic Flow**
1. The **client** sends an HTTP `GET` request to the **server**.
2. The **server** responds with an **infinite stream** of events (using `text/event-stream`).
3. The **connection stays open**, and the server keeps pushing data **without the client needing to poll**.
4. The **client listens** and processes events as they arrive.

---
## 3️⃣ SSE Example Response
A server responds with **SSE events** like this:

```http
HTTP/1.1 200 OK
Content-Type: text/event-stream
Cache-Control: no-cache
Connection: keep-alive

id: 1
event: message
data: {"message": "Hello, SSE!"}

id: 2
event: message
data: {"message": "Next event!"}
```

Each SSE event consists of:
- `id:` (Optional) – A unique event ID
- `event:` (Optional) – Event type (default: "message")
- `data:` – The actual **payload** (JSON, text, etc.)

---
## 4️⃣ SSE vs WebSockets vs Long Polling

| Feature | **SSE** | **WebSockets** | **Long Polling** |
|---------|--------|--------------|---------------|
| **Direction** | One-way (Server → Client) | Two-way (Client ↔ Server) | Simulated real-time |
| **Use Case** | Streaming updates, notifications | Chat, multiplayer games | When SSE/WebSockets aren't available |
| **Persistent Connection?** | ✅ Yes | ✅ Yes | ❌ No (repeated requests) |
| **Scalability** | ✅ Efficient for many clients | ❌ More expensive for scaling | ❌ High overhead |
| **Reconnection?** | ✅ Automatic reconnection | ❌ Manual logic needed | ✅ Auto reconnects |
| **Protocol** | HTTP/1.1 | WebSockets (TCP) | HTTP |

### **✅ When to Use SSE**
- When you **only need one-way streaming**.
- When working with **firewall-friendly** setups (SSE runs over **HTTP**, unlike WebSockets).
- When you want **automatic reconnections**.

### **❌ When NOT to Use SSE**
- If you need **two-way** communication (use **WebSockets** instead).
- If you need **binary data** (WebSockets handle it better).
- If **many connections are needed** (WebSockets are more scalable).

---
## 5️⃣ SSE in Quarkus

### **Expose an SSE Endpoint in Quarkus**
```java
@GET
@Path("/stream")
@Produces(MediaType.SERVER_SENT_EVENTS)
@SseElementType(MediaType.APPLICATION_JSON)
public Multi<Fight> stream() {
    return Multi.createFrom().ticks()
                .every(Duration.ofSeconds(1))
                .onItem().transformToUniAndMerge(x -> fightService.getFight());
}
```

### **How to Consume SSE?**

#### **JavaScript Frontend**
```javascript
const eventSource = new EventSource("http://localhost:8080/stream");
eventSource.onmessage = (event) => console.log("New event:", JSON.parse(event.data));
```

#### **Python Client**
```python
import requests
import sseclient

client = sseclient.SSEClient(requests.get("http://localhost:8080/stream", stream=True))
for event in client.events():
    print("Received:", event.data)
```

---
## 6️⃣ Summary
✅ **SSE is great** for **one-way real-time streaming** in **HTTP-based APIs**.  
✅ **Easy to use**, supports **auto-reconnect**, and works well in **Quarkus**.  
✅ Best suited for **notifications, dashboards, live data feeds**.  
❌ **Use WebSockets** if you need **bi-directional** communication.

Would you like to see a **complete Quarkus + JavaScript SSE demo?** 🚀


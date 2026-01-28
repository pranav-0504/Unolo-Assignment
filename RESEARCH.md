# Unolo Full Stack Intern – Research Assignment  
## Real-Time Location Tracking Architecture

## Overview

Unolo’s Field Force Tracker currently relies on manual check-ins to record employee visits. To improve real-time visibility and operational efficiency, a new feature is required: **continuous real-time location tracking** of field employees, visible live on a manager’s dashboard.

This document explores multiple real-time communication approaches, compares their trade-offs, and recommends a practical architecture suitable for Unolo’s scale, reliability needs, and startup constraints.

---

## 1. Technology Comparison

### 1. WebSockets

**How it works:**  
WebSockets establish a persistent, full-duplex connection between the client and server. Once connected, both sides can send data anytime without repeated HTTP requests.

**Pros**
- True real-time, low latency
- Bi-directional communication
- Efficient for frequent updates
- Widely supported and mature ecosystem

**Cons**
- Requires managing long-lived connections
- Needs extra infrastructure for horizontal scaling
- Slightly higher implementation complexity

**When to use**
- Live dashboards
- Chat applications
- Real-time tracking systems

---

### 2. Server-Sent Events (SSE)

**How it works:**  
SSE keeps a single long-lived HTTP connection where the server pushes updates to the client. Communication is one-way (server → client).

**Pros**
- Simple to implement
- Automatic reconnection support
- Lower overhead than WebSockets

**Cons**
- One-directional only
- Limited support on older mobile browsers
- Clients cannot push frequent data upstream efficiently

**When to use**
- Notifications
- Live feeds
- Monitoring dashboards

---

### 3. Long Polling

**How it works:**  
The client repeatedly sends HTTP requests to the server, which responds only when new data is available.

**Pros**
- Easy to implement
- Works in all environments
- No special infrastructure needed

**Cons**
- High server load
- Poor scalability
- Inefficient and higher latency

**When to use**
- Very small-scale or legacy systems

---

### 4. Third-Party Services (Firebase, Pusher, Ably)

**How it works:**  
Managed real-time messaging services handle connections, scaling, and delivery.

**Pros**
- Very fast to implement
- Auto-scaling and reliability
- Excellent mobile SDK support

**Cons**
- Expensive at scale
- Vendor lock-in
- Less control over data and architecture

**When to use**
- Rapid MVPs
- Teams with minimal backend infrastructure

---

## 2. Recommended Approach

### ✅ WebSockets with Batched Location Updates

For Unolo’s use case, **WebSockets** are the most balanced and scalable solution.

**Justification**
- **Scale:**  
  10,000+ employees sending updates every 30 seconds (~333 updates/sec) is manageable with Node.js and Redis.
- **Battery Efficiency:**  
  Batched updates reduce GPS and network usage on mobile devices.
- **Reliability:**  
  Reconnect logic and heartbeats handle flaky mobile networks well.
- **Cost:**  
  No per-message cost like third-party services.
- **Development Time:**  
  Integrates easily with the existing Node.js stack using libraries like `socket.io`.

---

## 3. Trade-offs

**What we sacrifice**
- Higher complexity compared to REST or SSE
- Need to manage connection lifecycle and scaling

**When we would reconsider**
- If updates are required only every few minutes, REST APIs may be simpler
- If engineering bandwidth is extremely limited, Firebase could be used temporarily

**Scalability limits**
- Beyond ~100k concurrent connections without horizontal scaling
- Requires Redis/pub-sub for multi-server deployments

---

## 4. High-Level Implementation Plan

### Backend
- Add WebSocket server (`socket.io`)
- Authenticate socket connections using JWT
- Receive location updates from employees
- Store latest location in Redis
- Persist periodic snapshots to the database
- Broadcast updates to manager dashboards

### Frontend / Mobile
- Background service collects GPS data
- Send location updates every 30–60 seconds
- Auto-reconnect on network drops
- Manager dashboard subscribes to live updates

### Infrastructure
- Node.js backend
- Redis (caching + pub/sub)
- Load balancer (e.g., Nginx)
- Horizontal scaling with multiple backend instances

---

## Conclusion

While no real-time solution is perfect, **WebSockets provide the best balance** between real-time performance, scalability, cost, and development effort for Unolo’s needs. With proper batching and reconnect handling, this approach can reliably support thousands of field employees in real-world mobile conditions.

---

## References

- MDN WebSockets: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API  
- Socket.IO Documentation: https://socket.io/docs  
- Firebase Pricing: https://firebase.google.com/pricing  

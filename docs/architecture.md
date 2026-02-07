## [Task] Product & architecture description

### Product Choice
**Product Name:** Telegram  
**Website:** https://telegram.org  

**Description:** Telegram is a globally accessible, cross-platform cloud-based instant messaging service that provides end-to-end encrypted video calling, VoIP, file sharing, and several other features.

---

### Main components

![Telegram Component Diagram](../docs/diagrams/out/telegram/component-diagram/Component%20Diagram.svg)

[Telegram Component Diagram Code](../docs/diagrams/src/telegram/component-diagram.puml)

Based on the architecture diagram provided, here are five key components:

1. **MTProto Gateway (DC Entry):** Acts as the primary entry point for client applications, handling the MTProto protocol to ensure secure and efficient communication between the user and the data center.
2. **Message Handling Service:** The core logic engine responsible for processing incoming messages, determining their destination, and ensuring they are delivered to the correct recipient or group.
3. **Auth & Session Service:** Manages user authentication, registration, and maintains active session states across multiple devices to keep the user logged in securely.
4. **State Cache (Redis):** A high-performance, in-memory data store used to keep track of real-time user statuses (like "online") and temporary session data for rapid access.
5. **Distributed File System (DFS):** A scalable storage layer designed to handle the massive volume of media, documents, and files shared by users, ensuring they are accessible globally.

---

### Data flow

![Telegram Sequence Diagram](../docs/diagrams/out/telegram/sequence-diagram/Sequence%20Diagram.svg)

[Telegram Sequence Diagram Code](../docs/diagrams/src/telegram/sequence-diagram.puml)

**Focus: User Sending a Message**  
In this flow, the Mobile App sends an encrypted payload via the MTProto Gateway. The Gateway forwards this to the Message Handling Service, which interacts with the Sharded Chat DB to persist the message and the Notification Service to alert the recipient. The data exchanged includes the message metadata (sender/receiver IDs), the encrypted message body, and delivery timestamps.

**Components interacting:**
- Mobile App → MTProto Gateway (encrypted message payload)
- MTProto Gateway → Message Handling Service (processed message data)
- Message Handling Service → Sharded Chat DB (message persistence)
- Message Handling Service → Notification Service (recipient alert)

---

### Deployment

![Telegram Deployment Diagram](../docs/diagrams/out/telegram/deployment-diagram/Deployment%20Diagram.svg)

[Telegram Deployment Diagram Code](../docs/diagrams/src/telegram/deployment-diagram.puml)

**Brief Description:** Telegram utilizes a distributed data center architecture. The Connection Layer and Core Services are typically deployed on high-performance Linux clusters across multiple geographic regions (Data Centers) to reduce latency. The Data Layer (Redis and Sharded DBs) is deployed on dedicated storage-optimized hardware with heavy replication for high availability.

---

### Assumptions

1. **Storage Deduplication:** I assume the Distributed File System (DFS) implements a hashing mechanism to deduplicate identical files (e.g., viral memes) to save storage space across millions of users.
2. **Regional Routing:** I assume the MTProto Gateway automatically routes a user to the closest physical Data Center (DC) based on their IP address to minimize message delivery latency.

---

### Open questions

1. **Secret Chats:** How does the data flow differ for "Secret Chats" where the server is not supposed to store the message content in the Sharded Chat DB?
2. **Load Balancing:** What specific load-balancing algorithm does the MTProto Gateway use to distribute millions of concurrent socket connections across the internal Core Services?
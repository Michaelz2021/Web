# Multi-Restaurant Kiosk System Architecture & Message Flow

## System Overview Diagram

```mermaid
graph TB
    subgraph "Customer Interface Layer"
        K1[Ordering Kiosk #1]
        K2[Ordering Kiosk #2]
        K3[Ordering Kiosk #N]
        MA[Mobile App]
    end
    
    subgraph "Cloud Infrastructure"
        LB[Load Balancer]
        
        subgraph "Application Layer"
            API[API Gateway]
            OS[Order Service]
            PS[Payment Service]
            NS[Notification Service]
            AS[Analytics Service]
            IS[Inventory Service]
        end
        
        subgraph "Data Layer"
            DB[(PostgreSQL Database)]
            REDIS[(Redis Cache)]
            QUEUE[Message Queue]
        end
    end
    
    subgraph "Restaurant A"
        POS_A[Restaurant POS A]
        KT_A[Kitchen Terminal A]
        OST_A[Order Status Terminal A]
        TV_A[Status Display TV A]
    end
    
    subgraph "Restaurant B"
        POS_B[Restaurant POS B]
        KT_B[Kitchen Terminal B]
        OST_B[Order Status Terminal B]
        TV_B[Status Display TV B]
    end
    
    subgraph "Restaurant N"
        POS_N[Restaurant POS N]
        KT_N[Kitchen Terminal N]
        OST_N[Order Status Terminal N]
        TV_N[Status Display TV N]
    end
    
    subgraph "External Services"
        GCASH[GCash API]
        MAYA[Maya API]
        SMS[SMS Gateway]
        BIR[BIR Reporting]
    end
    
    subgraph "Management Layer"
        ADMIN[Admin Dashboard]
        ANALYTICS[Analytics Dashboard]
        MOBILE_STAFF[Staff Mobile App]
    end
    
    %% Customer Interface Connections
    K1 --> LB
    K2 --> LB
    K3 --> LB
    MA --> LB
    
    %% Load Balancer to API
    LB --> API
    
    %% API Gateway to Services
    API --> OS
    API --> PS
    API --> NS
    API --> AS
    API --> IS
    
    %% Services to Data Layer
    OS --> DB
    PS --> DB
    NS --> REDIS
    AS --> DB
    IS --> DB
    OS --> QUEUE
    PS --> QUEUE
    NS --> QUEUE
    
    %% Services to Restaurant Systems
    QUEUE --> POS_A
    QUEUE --> POS_B
    QUEUE --> POS_N
    
    POS_A --> KT_A
    POS_A --> OST_A
    OST_A --> TV_A
    
    POS_B --> KT_B
    POS_B --> OST_B
    OST_B --> TV_B
    
    POS_N --> KT_N
    POS_N --> OST_N
    OST_N --> TV_N
    
    %% External Service Connections
    PS --> GCASH
    PS --> MAYA
    NS --> SMS
    AS --> BIR
    
    %% Management Connections
    API --> ADMIN
    API --> ANALYTICS
    API --> MOBILE_STAFF
    
    %% Feedback Loops
    KT_A --> OS
    KT_B --> OS
    KT_N --> OS
```

-----

## System Components Architecture

### 1. Ordering Kiosk Architecture

```mermaid
graph TD
    subgraph "Kiosk Hardware"
        TOUCH[24" Touchscreen Display]
        CPU[Intel i7 Processor]
        MEM[16GB RAM + 512GB SSD]
        QR[QR Code Scanner]
        PRINTER[Receipt Printer]
        SPEAKER[Audio Speaker]
        CAMERA[Security Camera]
    end
    
    subgraph "Kiosk Software Stack"
        UI[React Native UI]
        APP[Kiosk Application]
        CACHE[Local Cache]
        SYNC[Data Sync Service]
        PAYMENT[Payment Module]
        OFFLINE[Offline Mode]
    end
    
    subgraph "Connectivity"
        WIFI[WiFi 6]
        LTE[4G LTE Backup]
        ETH[Ethernet]
    end
    
    TOUCH --> UI
    CPU --> APP
    MEM --> CACHE
    QR --> PAYMENT
    PRINTER --> APP
    SPEAKER --> UI
    CAMERA --> APP
    
    APP --> SYNC
    SYNC --> WIFI
    SYNC --> LTE
    SYNC --> ETH
    
    CACHE --> OFFLINE
    PAYMENT --> OFFLINE
```

### 2. Central Server Infrastructure

```mermaid
graph TB
    subgraph "Load Balancing Tier"
        LB1[Load Balancer 1]
        LB2[Load Balancer 2]
        LB3[Load Balancer 3]
    end
    
    subgraph "Application Tier"
        API1[API Server 1]
        API2[API Server 2]
        API3[API Server 3]
        API4[API Server 4]
    end
    
    subgraph "Microservices Layer"
        ORDER[Order Management Service]
        PAY[Payment Processing Service]
        NOTIFY[Notification Service]
        ANALYTICS[Analytics Service]
        INVENTORY[Inventory Service]
        USER[User Management Service]
        MENU[Menu Management Service]
        REPORT[Reporting Service]
    end
    
    subgraph "Data Persistence Layer"
        MASTER[(Master Database)]
        REPLICA1[(Read Replica 1)]
        REPLICA2[(Read Replica 2)]
        CACHE_CLUSTER[Redis Cluster]
        QUEUE_CLUSTER[RabbitMQ Cluster]
    end
    
    subgraph "File Storage"
        S3[AWS S3 / Object Storage]
        CDN[CloudFront CDN]
    end
    
    LB1 --> API1
    LB2 --> API2
    LB3 --> API3
    LB1 --> API4
    
    API1 --> ORDER
    API2 --> PAY
    API3 --> NOTIFY
    API4 --> ANALYTICS
    
    ORDER --> MASTER
    PAY --> MASTER
    ANALYTICS --> REPLICA1
    INVENTORY --> REPLICA2
    
    ORDER --> CACHE_CLUSTER
    PAY --> CACHE_CLUSTER
    NOTIFY --> QUEUE_CLUSTER
    
    MENU --> S3
    REPORT --> S3
    S3 --> CDN
```

### 3. Restaurant POS Terminal

```mermaid
graph TD
    subgraph "POS Hardware"
        DISPLAY[15.6" Touch Display]
        ARM[ARM Processor]
        STORAGE[8GB RAM + 128GB Storage]
        THERMAL[Thermal Printer]
        CASH[Cash Drawer]
        SCANNER[Barcode Scanner]
    end
    
    subgraph "POS Software"
        POS_UI[POS Interface]
        ORDER_MGT[Order Management]
        INVENTORY_MGT[Inventory Management]
        SALES_RPT[Sales Reporting]
        PAYMENT_INT[Payment Integration]
        BIR_COMP[BIR Compliance]
    end
    
    subgraph "Connectivity"
        WIFI_POS[WiFi Connection]
        CLOUD_SYNC[Cloud Synchronization]
        LOCAL_DB[Local Database]
    end
    
    DISPLAY --> POS_UI
    ARM --> ORDER_MGT
    STORAGE --> LOCAL_DB
    THERMAL --> SALES_RPT
    CASH --> PAYMENT_INT
    SCANNER --> INVENTORY_MGT
    
    POS_UI --> ORDER_MGT
    ORDER_MGT --> CLOUD_SYNC
    CLOUD_SYNC --> WIFI_POS
    LOCAL_DB --> BIR_COMP
```

### 4. Kitchen Terminal System

```mermaid
graph TD
    subgraph "Kitchen Hardware"
        KDS[Kitchen Display System]
        TOUCH_K[Touch Interface]
        AUDIO_K[Audio Alerts]
        PRINTER_K[Order Printer]
        TIMER[Timer Display]
    end
    
    subgraph "Kitchen Software"
        QUEUE_MGT[Order Queue Management]
        TIMER_MGT[Timer Management]
        RECIPE[Recipe Display]
        STATUS_UPD[Status Updates]
        ALERTS[Alert System]
    end
    
    subgraph "Integration"
        ORDER_RECV[Order Receiver]
        STATUS_SEND[Status Sender]
        REAL_TIME[Real-time Sync]
    end
    
    KDS --> QUEUE_MGT
    TOUCH_K --> STATUS_UPD
    AUDIO_K --> ALERTS
    PRINTER_K --> QUEUE_MGT
    TIMER --> TIMER_MGT
    
    QUEUE_MGT --> ORDER_RECV
    STATUS_UPD --> STATUS_SEND
    ORDER_RECV --> REAL_TIME
    STATUS_SEND --> REAL_TIME
```

### 5. Order Status Terminal & TV Display

```mermaid
graph TD
    subgraph "Status Display Hardware"
        TV_SCREEN[Large TV Screen 55"+]
        MEDIA_PLAYER[Media Player/Raspberry Pi]
        SPEAKERS[Audio Speakers]
        LED_LIGHTS[LED Status Lights]
    end
    
    subgraph "Display Software"
        STATUS_APP[Status Display App]
        QUEUE_DISPLAY[Queue Display]
        ANIMATIONS[Transition Animations]
        MULTI_LANG[Multi-language Support]
        BRANDING[Restaurant Branding]
    end
    
    subgraph "Data Management"
        STATUS_SYNC[Status Synchronization]
        CACHE_STATUS[Status Cache]
        UPDATE_ENGINE[Update Engine]
    end
    
    TV_SCREEN --> STATUS_APP
    MEDIA_PLAYER --> QUEUE_DISPLAY
    SPEAKERS --> ANIMATIONS
    LED_LIGHTS --> STATUS_APP
    
    STATUS_APP --> STATUS_SYNC
    QUEUE_DISPLAY --> CACHE_STATUS
    STATUS_SYNC --> UPDATE_ENGINE
```

-----

## Interface Specifications

### 1. Kiosk to Server Interface

```typescript
// REST API Endpoints
interface KioskServerAPI {
  // Menu Management
  getRestaurants(): Promise<Restaurant[]>;
  getMenuItems(restaurantId: string): Promise<MenuItem[]>;
  checkItemAvailability(itemId: string): Promise<boolean>;
  
  // Order Management
  createOrder(orderData: OrderRequest): Promise<OrderResponse>;
  updateOrder(orderId: string, updates: OrderUpdate): Promise<void>;
  getOrderStatus(orderId: string): Promise<OrderStatus>;
  
  // Payment Processing
  initiatePayment(paymentData: PaymentRequest): Promise<PaymentResponse>;
  verifyPayment(transactionId: string): Promise<PaymentStatus>;
  
  // System Health
  healthCheck(): Promise<SystemStatus>;
  syncData(): Promise<SyncResponse>;
}

// WebSocket Events
interface KioskWebSocketEvents {
  onOrderStatusUpdate: (orderId: string, status: OrderStatus) => void;
  onMenuUpdate: (restaurantId: string, menu: MenuItem[]) => void;
  onSystemAlert: (alert: SystemAlert) => void;
  onPaymentCallback: (transactionId: string, status: PaymentStatus) => void;
}
```

### 2. Server to Restaurant POS Interface

```typescript
// Message Queue Interface
interface ServerToPOSInterface {
  // Order Events
  onNewOrder: {
    topic: "orders.new";
    payload: {
      orderId: string;
      restaurantId: string;
      items: OrderItem[];
      customerInfo: CustomerInfo;
      totalAmount: number;
      paymentStatus: PaymentStatus;
      timestamp: string;
    };
  };
  
  onOrderUpdate: {
    topic: "orders.update";
    payload: {
      orderId: string;
      status: OrderStatus;
      estimatedTime: number;
      notes: string;
    };
  };
  
  onOrderCancellation: {
    topic: "orders.cancel";
    payload: {
      orderId: string;
      reason: string;
      refundAmount: number;
    };
  };
}

// POS Response Interface
interface POSToServerInterface {
  // Order Acknowledgment
  acknowledgeOrder: {
    topic: "orders.acknowledge";
    payload: {
      orderId: string;
      acknowledged: boolean;
      estimatedPrepTime: number;
      issues?: string[];
    };
  };
  
  // Status Updates
  updateOrderStatus: {
    topic: "orders.status";
    payload: {
      orderId: string;
      status: "received" | "preparing" | "ready" | "completed";
      timestamp: string;
    };
  };
  
  // Inventory Updates
  updateInventory: {
    topic: "inventory.update";
    payload: {
      itemId: string;
      quantity: number;
      available: boolean;
    };
  };
}
```

### 3. POS to Kitchen Terminal Interface

```typescript
// Local Network Interface
interface POSToKitchenInterface {
  // Order Dispatch
  sendToKitchen: {
    orderId: string;
    items: KitchenItem[];
    priority: "normal" | "urgent";
    specialInstructions: string;
    customerInfo: {
      orderNumber: string;
      customerName?: string;
    };
  };
  
  // Kitchen Feedback
  receiveFromKitchen: {
    orderId: string;
    status: "started" | "in-progress" | "ready";
    estimatedCompletion: string;
    completedItems: string[];
  };
}

// Kitchen Display Protocol
interface KitchenDisplayProtocol {
  displayOrder: {
    orderNumber: string;
    items: DisplayItem[];
    timestamp: string;
    priority: number;
    customerType: "dine-in" | "takeout" | "delivery";
  };
  
  updateTimer: {
    orderNumber: string;
    elapsedTime: number;
    targetTime: number;
    status: "on-time" | "delayed" | "urgent";
  };
}
```

### 4. Kitchen to Status Display Interface

```typescript
// Status Broadcasting Interface
interface KitchenToStatusInterface {
  // Order Ready Notification
  orderReady: {
    orderNumber: string;
    customerInfo: CustomerDisplayInfo;
    readyTime: string;
    pickupLocation: string;
    announcement: {
      text: string;
      audio: boolean;
      language: "en" | "tl" | "zh";
    };
  };
  
  // Queue Updates
  queueUpdate: {
    restaurant: string;
    currentOrders: QueueItem[];
    averageWaitTime: number;
    nextOrderTime: string;
  };
}

// TV Display Protocol
interface TVDisplayProtocol {
  displayQueue: {
    readyOrders: ReadyOrder[];
    preparingOrders: PreparingOrder[];
    waitTimes: WaitTimeInfo[];
    announcements: Announcement[];
  };
  
  styling: {
    theme: "light" | "dark" | "brand";
    language: string;
    fontSize: "small" | "medium" | "large";
    animations: boolean;
  };
}
```

-----

## Message Flow Diagrams

### 1. Complete Order Flow

```mermaid
sequenceDiagram
    participant C as Customer
    participant K as Kiosk
    participant S as Server
    participant P as Restaurant POS
    participant KT as Kitchen Terminal
    participant ST as Status Terminal
    participant TV as Status TV Display
    
    Note over C,TV: Complete Order Processing Flow
    
    C->>K: Select restaurant & items
    K->>S: GET /restaurants, /menu-items
    S-->>K: Restaurant & menu data
    K-->>C: Display menu options
    
    C->>K: Add items to cart
    K->>K: Calculate total & taxes
    
    C->>K: Proceed to payment
    K->>S: POST /orders (create order)
    S-->>K: Order ID & payment QR
    K-->>C: Display GCash QR code
    
    C->>C: Scan QR & pay via GCash
    Note over S: Payment verification
    S->>S: Verify payment status
    S->>K: WebSocket: Payment confirmed
    K-->>C: Payment success & order number
    
    S->>P: Message Queue: New order
    P-->>S: Acknowledge order receipt
    P->>KT: Local: Send order to kitchen
    KT-->>P: Order received in kitchen
    
    P->>S: Update: Order preparing
    S->>ST: WebSocket: Status update
    S->>K: WebSocket: Status update
    
    KT->>KT: Kitchen prepares order
    KT->>P: Local: Order ready
    P->>S: Update: Order ready
    
    S->>ST: WebSocket: Order ready
    ST->>TV: Local: Display order ready
    TV-->>C: Audio/Visual: Order ready announcement
    
    C->>P: Pickup order
    P->>S: Update: Order completed
    S->>ST: WebSocket: Remove from display
```

### 2. Payment Processing Flow

```mermaid
sequenceDiagram
    participant K as Kiosk
    participant S as Server
    participant PS as Payment Service
    participant G as GCash API
    participant P as Restaurant POS
    
    Note over K,P: Payment Processing Sequence
    
    K->>S: POST /payments/initiate
    S->>PS: Create payment request
    PS->>G: Generate QR payment
    G-->>PS: QR code & transaction ID
    PS-->>S: Payment details
    S-->>K: QR code for display
    
    K->>K: Display QR code (5 min timeout)
    K->>S: WebSocket: Monitor payment status
    
    Note over G: Customer pays via GCash app
    G->>PS: Webhook: Payment completed
    PS->>S: Payment confirmation
    S->>K: WebSocket: Payment success
    S->>P: Message Queue: Paid order
    
    alt Payment timeout
        K->>S: Payment timeout
        S->>PS: Cancel payment
        PS->>G: Cancel transaction
        S-->>K: Payment cancelled
    end
    
    alt Payment failed
        G->>PS: Webhook: Payment failed
        PS->>S: Payment failure
        S->>K: WebSocket: Payment failed
        K-->>K: Display retry option
    end
```

### 3. Kitchen Operation Flow

```mermaid
sequenceDiagram
    participant P as Restaurant POS
    participant KT as Kitchen Terminal
    participant ST as Status Terminal
    participant S as Server
    
    Note over P,S: Kitchen Operations Flow
    
    P->>KT: New order received
    KT-->>P: Order acknowledged
    KT->>KT: Display order on screen
    KT->>KT: Start preparation timer
    
    loop Order Preparation
        KT->>KT: Update preparation status
        KT->>P: Status: Item completed
        P->>S: Update order progress
        S->>ST: WebSocket: Progress update
    end
    
    KT->>KT: All items completed
    KT->>P: Order ready notification
    P->>S: Update: Order ready
    S->>ST: WebSocket: Order ready
    
    ST->>ST: Add to ready queue
    ST->>ST: Audio announcement
    ST->>ST: Update TV display
    
    Note over ST: Customer pickup process
    P->>P: Staff confirms pickup
    P->>S: Update: Order picked up
    S->>ST: WebSocket: Remove from display
    ST->>ST: Clear from ready queue
```

### 4. System Health Monitoring Flow

```mermaid
sequenceDiagram
    participant K as Kiosk
    participant S as Server
    participant P as Restaurant POS
    participant KT as Kitchen Terminal
    participant ST as Status Terminal
    participant M as Monitoring System
    
    Note over K,M: System Health Monitoring
    
    loop Every 30 seconds
        K->>S: Health check ping
        S-->>K: Health status
        
        P->>S: Health check ping
        S-->>P: Health status
        
        KT->>P: Local health check
        P-->>KT: Local status
        
        ST->>S: Health check ping
        S-->>ST: Health status
    end
    
    S->>M: Aggregate health metrics
    M->>M: Analyze system performance
    
    alt System issue detected
        M->>M: Generate alert
        M->>S: System alert
        S->>K: WebSocket: System alert
        S->>P: Message Queue: System alert
        P->>KT: Local: System alert
        S->>ST: WebSocket: System alert
    end
    
    alt Component offline
        M->>M: Detect offline component
        M->>S: Failover procedures
        S->>S: Activate backup systems
        S->>K: WebSocket: Fallback mode
    end
```

-----

## Data Models & Protocols

### Core Data Structures

```typescript
// Order Data Model
interface Order {
  orderId: string;
  orderNumber: string;
  restaurantId: string;
  customerId?: string;
  items: OrderItem[];
  totalAmount: number;
  taxAmount: number;
  discountAmount: number;
  paymentMethod: "gcash" | "maya" | "card" | "cash";
  paymentStatus: "pending" | "completed" | "failed" | "refunded";
  orderStatus: "received" | "preparing" | "ready" | "completed" | "cancelled";
  timestamps: {
    created: string;
    paid: string;
    acknowledged: string;
    preparing: string;
    ready: string;
    completed: string;
  };
  estimatedPrepTime: number;
  actualPrepTime?: number;
  specialInstructions?: string;
  customerInfo: CustomerInfo;
}

// Menu Item Model
interface MenuItem {
  itemId: string;
  restaurantId: string;
  name: string;
  description: string;
  price: number;
  category: string;
  subcategory?: string;
  image: string;
  ingredients: string[];
  allergens: string[];
  nutritionInfo?: NutritionInfo;
  available: boolean;
  preparationTime: number;
  spicyLevel?: number;
  customizations: Customization[];
  tags: string[];
}

// Restaurant Model
interface Restaurant {
  restaurantId: string;
  name: string;
  cuisine: string;
  description: string;
  logo: string;
  coverImage: string;
  status: "open" | "closed" | "busy";
  operatingHours: OperatingHours;
  averagePrepTime: number;
  rating: number;
  location: string;
  contact: ContactInfo;
  paymentMethods: PaymentMethod[];
  menu: MenuItem[];
  settings: RestaurantSettings;
}
```

### Communication Protocols

```yaml
# WebSocket Protocol Specification
websocket_protocol:
  connection: "wss://api.nteksystems.ph/ws"
  authentication: "Bearer JWT Token"
  heartbeat_interval: 30_seconds
  
  channels:
    - name: "orders"
      events: ["new", "update", "status", "cancel"]
    - name: "payments"
      events: ["success", "failure", "timeout"]
    - name: "system"
      events: ["health", "alert", "maintenance"]
    - name: "menu"
      events: ["update", "availability"]

# Message Queue Protocol
message_queue_protocol:
  broker: "RabbitMQ"
  exchange_type: "topic"
  
  exchanges:
    orders:
      routing_keys:
        - "orders.new.{restaurant_id}"
        - "orders.update.{restaurant_id}"
        - "orders.cancel.{restaurant_id}"
    
    payments:
      routing_keys:
        - "payments.success.{order_id}"
        - "payments.failure.{order_id}"
    
    notifications:
      routing_keys:
        - "notify.order.{customer_id}"
        - "notify.system.{location_id}"

# REST API Protocol
rest_api_protocol:
  base_url: "https://api.nteksystems.ph/v1"
  authentication: "Bearer JWT Token"
  rate_limiting: "1000 requests/minute"
  
  content_type: "application/json"
  error_format: "RFC 7807 Problem Details"
  
  standard_headers:
    - "X-Request-ID"
    - "X-Client-Version"
    - "X-Location-ID"
    - "Accept-Language"
```

This comprehensive system architecture provides a robust, scalable, and maintainable foundation for the multi-restaurant kiosk system, ensuring reliable communication between all components while maintaining high performance and fault tolerance.
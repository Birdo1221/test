# GoTor C2 Framework

*Advanced Security Research Framework with Tor Support*

## 📌 Overview

SCREAM is a sophisticated security research platform designed for authorized penetration testing and security research. This Golang-based server features:

- **Encrypted TLS Communication** with client agents
- **Tor Hidden Service** integration for anonymous operation
- **Multi-user Role-Based Access Control**
- **Real-time Dashboard** with performance metrics
- **Multiple Network Testing Vectors** with adaptive rate limiting
- **Client Health Monitoring** with heartbeat system

> ⚠️ **Important**: This framework is intended solely for authorized security testing, educational purposes, and legitimate security research. Always obtain proper authorization before testing any system. Unauthorized use may violate laws and regulations.

## 🔥 Features

### Core Architecture

- **Asynchronous Design**: Non-blocking I/O for 10,000+ concurrent clients
- **Modular Testing System**: Pluggable test modules with runtime validation
- **Ephemeral Clients**: Auto-cleanup of stale connections
- **HMAC Challenge-Response**: For client authentication

```mermaid
sequenceDiagram
    Client->>Server: TCP Handshake (TLS 1.3)
    Server->>Client: CHALLENGE:nonce
    Client->>Server: HMAC-SHA256(nonce)
    Server->>Client: AUTH_SUCCESS
    loop Heartbeat
        Client->>Server: PONG (30s interval)
    end
```
```mermaid
graph TB
    subgraph "Internet"
        User["Operator/Administrator"]
        TorNetwork["Tor Network"]
        style TorNetwork fill:#ddd,stroke:#999,stroke-width:1px
    end
    
    subgraph "C2 Server Infrastructure"
        WebServer["HTTPS Web Server\n(Port 443)"]
        C2Server["Command & Control Server\n(Port 7002)"]
        Database["PostgreSQL Database"]
        TorProxy["Tor Hidden Service Proxy"]
        
        style WebServer fill:#f9f,stroke:#333,stroke-width:1px
        style C2Server fill:#bbf,stroke:#333,stroke-width:1px
        style Database fill:#bfb,stroke:#333,stroke-width:1px
        style TorProxy fill:#fbb,stroke:#333,stroke-width:1px
    end
    
    subgraph "Client Network"
        Client1["Client 1\n(Windows)"]
        Client2["Client 2\n(Linux)"]
        Client3["Client 3\n(macOS)"]
        ClientN["Client N\n(...)"]
        
        style Client1 fill:#e6f7ff,stroke:#333,stroke-width:1px
        style Client2 fill:#e6f7ff,stroke:#333,stroke-width:1px
        style Client3 fill:#e6f7ff,stroke:#333,stroke-width:1px
        style ClientN fill:#e6f7ff,stroke:#333,stroke-width:1px
    end
    
    subgraph "Test Target"
        TargetSystem["Target System\n(Authorized Only)"]
        style TargetSystem fill:#ffb380,stroke:#333,stroke-width:1px
    end
    
    %% Connection paths for standard operation
    User -->|"Tor Browser\nAccess"| TorNetwork
    TorNetwork -->|"Encrypted\nTraffic"| TorProxy
    TorProxy -->|"Local\nForward"| WebServer
    WebServer <-->|"Internal\nAPI"| C2Server
    C2Server <-->|"Data\nStorage"| Database
    
    %% Client connections
    Client1 -->|"TLS 1.3\nHMAC Authentication"| C2Server
    Client2 -->|"TLS 1.3\nHMAC Authentication"| C2Server
    Client3 -->|"TLS 1.3\nHMAC Authentication"| C2Server
    ClientN -->|"TLS 1.3\nHMAC Authentication"| C2Server
    
    %% Test execution flow
    C2Server -->|"Command\nDistribution"| Client1
    C2Server -->|"Command\nDistribution"| Client2
    C2Server -->|"Command\nDistribution"| Client3
    C2Server -->|"Command\nDistribution"| ClientN
    
    %% Test targeting
    Client1 -->|"Network\nTesting"| TargetSystem
    Client2 -->|"Network\nTesting"| TargetSystem
    Client3 -->|"Network\nTesting"| TargetSystem
    ClientN -->|"Network\nTesting"| TargetSystem
    
    %% Heartbeat and status reporting
    Client1 -.->|"Heartbeat\n(30s)"| C2Server
    Client2 -.->|"Heartbeat\n(30s)"| C2Server
    Client3 -.->|"Heartbeat\n(30s)"| C2Server
    ClientN -.->|"Heartbeat\n(30s)"| C2Server
    
    %% Admin interface flow
    WebServer -->|"User Authentication\nJWT Token"| User
    
    %% Security features
    subgraph "Security Measures"
        CSRFProtection["CSRF Protection"]
        TLSEncryption["TLS 1.3 Encryption"]
        ChallengeResponse["Challenge-Response Auth"]
        ArgonHashing["Argon2id Password Hashing"]
        RateLimiting["Request Rate Limiting"]
        
        style CSRFProtection fill:#ffe6e6,stroke:#333,stroke-width:1px
        style TLSEncryption fill:#ffe6e6,stroke:#333,stroke-width:1px
        style ChallengeResponse fill:#ffe6e6,stroke:#333,stroke-width:1px
        style ArgonHashing fill:#ffe6e6,stroke:#333,stroke-width:1px
        style RateLimiting fill:#ffe6e6,stroke:#333,stroke-width:1px
    end
    
    WebServer --- CSRFProtection
    WebServer --- TLSEncryption
    C2Server --- ChallengeResponse
    WebServer --- ArgonHashing
    C2Server --- RateLimiting
```

### Network Testing Capabilities

| Method | Layer | Description | Max Duration |
|--------|-------|-------------|--------------|
| UDP Test | 4 | High-volume UDP packet testing | 3600s |
| TCP Smart | 4 | Stateful TCP session analysis | 1800s |
| GRE Test | 3 | Protocol examination with GRE packets | 600s |
| HTTP Connection Test | 7 | Partial HTTP requests with keepalive | 300s |

### Security Features

- **Argon2id Password Hashing**: 128MB memory / 4 threads
- **JWT Authentication**: 15-minute expiry with refresh
- **CSRF Protection**: Per-session tokens
- **CSP Headers**: Strict Content Security Policy
- **WebRTC Disabled**: Prevents IP leakage

## 🛠️ Installation

### Prerequisites

```bash
sudo apt install tor golang-go postgresql
```

### Setup

Generate certificates:
```bash
go run main.go -gencert
```

Configure Tor (automatically done by server):
```bash
cat > /etc/tor/torrc <<EOF
HiddenServiceDir /var/lib/tor/scream_service/
HiddenServicePort 80 127.0.0.1:443
EOF
```

## 🖥️ Dashboard Features

```mermaid
pie
    title Dashboard Components
    "Client Network" : 35
    "Test Control" : 30
    "User Management" : 20
    "Analytics" : 15
```

### Real-time Monitoring

- Client geographic distribution
- CPU/RAM utilization heatmap
- Network throughput graphs
- Packet loss metrics

### User Roles

| Role | Concurrent Tests | Methods Available | Duration Limit |
|------|-----------------|-------------------|---------------|
| Owner | 5 | All | 60 min |
| Admin | 5 | No UPDATE command | 30 min |
| Pro | 3 | Basic protocols | 10 min |
| Basic | 1 | UDP/TCP only | 5 min |

## ⚡ Quick Start

Start the server:
```bash
go build -o scream && ./scream -tor -debug
```

Access via Tor:
```bash
torify curl http://youronionaddress.onion
```

Default credentials:
```
Username: root
Password: [generated during first run]
```

## 📊 Performance Metrics

```mermaid
gantt
    title Test Lifecycle
    dateFormat  HH:mm:ss
    section Client Network
    Connection Establishment :a1, 00:00:00, 5s
    Challenge Response      :a2, after a1, 3s
    section Test
    Command Propagation     :a3, 00:00:08, 2s
    Traffic Generation      :a4, after a3, 30s
```

- Throughput: 1.2M packets/sec per client
- Latency: <200ms command propagation
- Scalability: Tested with 5,000 concurrent clients

## 🔒 Security Considerations

### Operational Security

- All traffic routed through Tor
- Server IP never exposed to clients
- Memory-safe implementations
- No persistent client identifiers

### Defensive Measures

```mermaid
graph LR
    A[Incoming Request] --> B{Rate Limited?}
    B -->|No| C[HMAC Validation]
    B -->|Yes| D[Drop Connection]
    C --> E[Command Whitelist Check]
    E --> F[Execute Command]
```

## 🚨 Legal & Ethical Considerations

This tool is provided for **educational and authorized security testing purposes only**. It is your responsibility to:

1. Obtain proper authorization before testing any system
2. Comply with all applicable laws and regulations
3. Use this tool only in environments you own or have explicit permission to test
4. Understand that the developers assume no liability for misuse

## 📜 License

GNU General Public License v3.0

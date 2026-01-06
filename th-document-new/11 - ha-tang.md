# Giải Pháp Hạ Tầng Backend Mango CMS cho TH eLife

## Mục lục
1. [Tổng quan](#1-tổng-quan)
2. [Kiến trúc tổng thể](#2-kiến-trúc-tổng-thể)
3. [Phương án triển khai](#3-phương-án-triển-khai)
4. [Thiết kế hạ tầng chi tiết](#4-thiết-kế-hạ-tầng-chi-tiết)
5. [Network Topology](#5-network-topology)
6. [High Availability & Backup](#6-high-availability--backup)
7. [Bảo mật hệ thống](#7-bảo-mật-hệ-thống)
8. [Giám sát và vận hành](#8-giám-sát-và-vận-hành)
9. [So sánh phương án](#9-so-sánh-phương-án)

---

## 1. Tổng quan

### 1.1 Mục tiêu
Xây dựng hệ thống backend Mango CMS cho TH eLife với các tiêu chí:
- **Độ sẵn sàng:** 99.9% uptime
- **Khả năng mở rộng:** Scale theo nhu cầu tăng trưởng
- **Bảo mật:** Đáp ứng các tiêu chuẩn an toàn thông tin của TH
- **Linh hoạt:** Hỗ trợ cả On-Cloud và On-Premise

### 1.2 Phạm vi hệ thống
- Website TH eLife (Web Frontend)
- Mobile App TH eLife (iOS/Android)
- Backend API Services (Node.js Fastify)
- CMS Management System (Mango CMS)
- Tích hợp với LsRetail, Loyalty, Payment Gateway

### 1.3 Technology Stack

| Layer | Technology | Version |
|-------|------------|---------|
| **Runtime** | Node.js | 20 LTS |
| **Framework** | Fastify | 4.x |
| **Database** | MongoDB | 7.0 |
| **Cache** | Redis | 7.2 |
| **Search** | Elasticsearch | 8.x |
| **Message Queue** | BullMQ (Redis-based) | 5.x |

---

## 2. Kiến trúc tổng thể

### 2.1 Mô hình kiến trúc 3-Tier

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              INTERNET                                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  DMZ Zone                                                                   │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐              │
│  │   WAF/DDoS      │  │  Load Balancer  │  │   CDN/Cache     │              │
│  │   Protection    │  │   (L7/L4)       │  │   (Static)      │              │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘              │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  API Gateway Zone                                                           │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                         API Gateway Cluster                             ││
│  │            (Rate Limiting, Authentication, Routing)                     ││
│  └─────────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  Application Zone                                                           │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐ │
│  │  API Server   │  │  API Server   │  │  API Server   │  │  API Server   │ │
│  │  (Fastify)    │  │  (Fastify)    │  │  (Fastify)    │  │  (Fastify)    │ │
│  │   Node 1      │  │   Node 2      │  │   Node 3      │  │   Node 4      │ │
│  └───────────────┘  └───────────────┘  └───────────────┘  └───────────────┘ │
│                                                                             │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐                    │
│  │  CMS Admin    │  │  Background   │  │   Queue       │                    │
│  │  (Mango CMS)  │  │  Workers      │  │   (BullMQ/    │                    │
│  │               │  │  (Node.js)    │  │   Redis)      │                    │
│  └───────────────┘  └───────────────┘  └───────────────┘                    │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  Database Zone                                                              │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                    MongoDB Replica Set                                  ││
│  │  ┌───────────┐  ┌───────────┐  ┌───────────┐                            ││
│  │  │  Primary  │  │ Secondary │  │ Secondary │                            ││
│  │  │  (R/W)    │◄─┤  (Read)   │◄─┤  (Read)   │                            ││
│  │  └───────────┘  └───────────┘  └───────────┘                            ││
│  └─────────────────────────────────────────────────────────────────────────┘│
│                                                                             │
│  ┌─────────────────────────────┐  ┌─────────────────────────────┐           │
│  │     Redis Cluster           │  │     Elasticsearch           │           │
│  │     (Session/Cache/Queue)   │  │     (Search Engine)         │           │
│  └─────────────────────────────┘  └─────────────────────────────┘           │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  Storage Zone                                                               │
│  ┌─────────────────────────────┐  ┌─────────────────────────────┐           │
│  │     Object Storage          │  │     File Storage (NFS)      │           │
│  │     (S3/MinIO)              │  │     Media/Assets            │           │
│  └─────────────────────────────┘  └─────────────────────────────┘           │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Các thành phần chính

| Thành phần | Mô tả | Công nghệ |
|------------|-------|-----------|
| **Reverse Proxy** | Xử lý request HTTP/HTTPS, SSL termination | Nginx |
| **API Gateway** | Authentication, Rate limiting, Routing | Kong/AWS API Gateway |
| **API Server** | Business logic, RESTful API endpoints | Node.js 20 + Fastify 4.x |
| **CMS Backend** | Quản trị nội dung, sản phẩm | Mango CMS (Node.js) |
| **Database** | Lưu trữ dữ liệu chính (NoSQL) | MongoDB 7.0 Replica Set |
| **Cache** | Session, Object cache, Rate limiting | Redis 7.2 Cluster |
| **Search** | Full-text search sản phẩm | Elasticsearch 8.x |
| **Message Queue** | Async processing, Background jobs | BullMQ (Redis-based) |
| **Object Storage** | Media, assets, backups | S3/MinIO/Azure Blob |

---

## 3. Phương án triển khai

### 3.1 Phương án A: On-Cloud (AWS/Azure/GCP)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              AWS Cloud                                      │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │  VPC: 10.0.0.0/16                                                       ││
│  │                                                                         ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐││
│  │  │  Public Subnet (DMZ): 10.0.1.0/24                                   │││
│  │  │  - AWS WAF + Shield                                                 │││
│  │  │  - Application Load Balancer (ALB)                                  │││
│  │  │  - NAT Gateway                                                      │││
│  │  └─────────────────────────────────────────────────────────────────────┘││
│  │                                                                         ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐││
│  │  │  Private Subnet (App): 10.0.2.0/24                                  │││
│  │  │  - EC2 Auto Scaling Group (Node.js Fastify Servers)                 │││
│  │  │  - ECS/EKS Cluster (Containerized Services)                         │││
│  │  │  - ElastiCache Redis Cluster                                        │││
│  │  └─────────────────────────────────────────────────────────────────────┘││
│  │                                                                         ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐││
│  │  │  Private Subnet (DB): 10.0.3.0/24                                   │││
│  │  │  - Amazon DocumentDB (MongoDB compatible) / MongoDB Atlas           │││
│  │  │  - Amazon OpenSearch Service                                        │││
│  │  └─────────────────────────────────────────────────────────────────────┘││
│  │                                                                         ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐││
│  │  │  Storage                                                            │││
│  │  │  - S3 (Media, Backups)                                              │││
│  │  │  - EFS (Shared File System)                                         │││
│  │  └─────────────────────────────────────────────────────────────────────┘││
│  └─────────────────────────────────────────────────────────────────────────┘│
│                                                                             │
│                           ┌─────────────┐                                   │
│                           │ VPN Gateway │◄──── Site-to-Site VPN ──── DC TH  │
│                           └─────────────┘                                   │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Dịch vụ AWS đề xuất:**

| Layer | Service | Specs |
|-------|---------|-------|
| **CDN** | CloudFront | Global Edge Locations |
| **WAF** | AWS WAF + Shield Standard | DDoS Protection |
| **Load Balancer** | Application Load Balancer | Layer 7, SSL Termination |
| **Compute** | EC2 (c6i.xlarge) / ECS Fargate | 4 vCPU, 8GB RAM |
| **Database** | Amazon DocumentDB / MongoDB Atlas | db.r6g.xlarge (4 vCPU, 32GB) |
| **Cache** | ElastiCache Redis | cache.r6g.large (2 vCPU, 13GB) |
| **Search** | OpenSearch Service | r6g.large.search (2 nodes) |
| **Storage** | S3 Standard | Unlimited |
| **VPN** | AWS Site-to-Site VPN | 1.25 Gbps |

### 3.2 Phương án B: On-Premise

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           TH Data Center                                    │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │  Core Switch                                                            ││
│  │  └── Firewall (FortiGate/Palo Alto)                                     ││
│  └─────────────────────────────────────────────────────────────────────────┘│
│                          │                                                  │
│     ┌────────────────────┼────────────────────┐                             │
│     │                    │                    │                             │
│     ▼                    ▼                    ▼                             │
│  ┌──────────┐      ┌──────────┐        ┌──────────┐                         │
│  │ VLAN 10  │      │ VLAN 20  │        │ VLAN 30  │                         │
│  │ DMZ      │      │ APP      │        │ DB       │                         │
│  │10.10.10.0│      │10.10.20.0│        │10.10.30.0│                         │
│  └──────────┘      └──────────┘        └──────────┘                         │
│       │                  │                   │                              │
│       ▼                  ▼                   ▼                              │
│  ┌──────────┐      ┌──────────┐        ┌──────────┐                         │
│  │F5/HAProxy│      │ Fastify  │        │ MongoDB  │                         │
│  │Load Bal. │      │ Servers  │        │ Primary  │                         │
│  │(HA Pair) │      │(4 nodes) │        │          │                         │
│  └──────────┘      └──────────┘        └──────────┘                         │
│       │                  │                   │                              │
│       │                  │              ┌────┴────┐                         │
│       │                  │              ▼         ▼                         │
│       │                  │        ┌──────────┐ ┌──────────┐                 │
│       │                  │        │ MongoDB  │ │ MongoDB  │                 │
│       │                  │        │Secondary1│ │Secondary2│                 │
│       │                  │        └──────────┘ └──────────┘                 │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │  Shared Storage (SAN/NAS) - NetApp/Dell EMC/HPE                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │  Backup Infrastructure - Veeam Backup & Replication                  │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.3 Phương án C: Hybrid (Đề xuất)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                HYBRID MODEL                                 │
│                                                                             │
│  ┌─────────────────────────┐          ┌─────────────────────────┐           │
│  │      AWS Cloud          │          │     TH Data Center      │           │
│  │                         │          │                         │           │
│  │  - CloudFront (CDN)     │          │  - MongoDB Replica Set  │           │
│  │  - WAF/DDoS             │◄─VPN────►│  - LsRetail Integration │           │
│  │  - Fastify API Servers  │          │  - Internal APIs        │           │
│  │  - Redis Cache          │          │  - Backup Storage       │           │
│  │  - S3 Storage           │          │                         │           │
│  │                         │          │                         │           │
│  └─────────────────────────┘          └─────────────────────────┘           │
│                                                                             │
│  Ưu điểm:                                                                   │
│  - Frontend/API trên Cloud: Scale linh hoạt, Global reach                   │
│  - Database/Core tại DC: Latency thấp với LsRetail, kiểm soát dữ liệu       │
│  - VPN Site-to-Site: Bảo mật, băng thông cao                                │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Thiết kế hạ tầng chi tiết

### 4.1 Môi trường Production

| Thành phần | Số lượng | Cấu hình | VLAN/Network |
|------------|----------|----------|--------------|
| **Load Balancer** | 2 (HA Pair) | F5 BIG-IP / HAProxy | DMZ - 10.10.10.0/24 |
| **API Server (Fastify)** | 4 | 8 vCPU, 16GB RAM, 200GB SSD | APP - 10.10.20.0/24 |
| **MongoDB Primary** | 1 | 16 vCPU, 64GB RAM, 2TB NVMe | DB - 10.10.30.0/24 |
| **MongoDB Secondary** | 2 | 16 vCPU, 64GB RAM, 2TB NVMe | DB - 10.10.30.0/24 |
| **Redis Cluster** | 3 | 8 vCPU, 32GB RAM, 200GB SSD | APP - 10.10.20.0/24 |
| **Elasticsearch** | 2 | 8 vCPU, 32GB RAM, 1TB SSD | APP - 10.10.20.0/24 |

### 4.2 Môi trường Test/Dev

| Thành phần | Số lượng | Cấu hình | Ghi chú |
|------------|----------|----------|---------|
| **API Server** | 1-2 | 4 vCPU, 8GB RAM | Scaled-down |
| **MongoDB** | 1 | 4 vCPU, 16GB RAM | Standalone |
| **Redis** | 1 | 2 vCPU, 4GB RAM | Standalone |
| **Elasticsearch** | 1 | 2 vCPU, 8GB RAM | Single node |

**Network:** Isolated VLAN 10.10.100.0/24, không kết nối production data.

---

## 5. Network Topology

### 5.1 VLAN Design

| VLAN | Subnet | Mục đích | Firewall Rules |
|------|--------|----------|----------------|
| **VLAN 10 - DMZ** | 10.10.10.0/24 | Load Balancer, WAF, API Gateway | Allow 80/443 from Internet |
| **VLAN 20 - APP** | 10.10.20.0/24 | Fastify Servers, Redis, ES | Allow from DMZ on port 3000 |
| **VLAN 30 - DB** | 10.10.30.0/24 | MongoDB Replica Set | Allow from APP on port 27017 |
| **VLAN 40 - MGMT** | 10.10.40.0/24 | Jump Server, Monitoring | Admin IPs only |

### 5.2 VPN Site-to-Site

| Thông số | Giá trị |
|----------|---------|
| **Protocol** | IPSec IKEv2 |
| **Encryption** | AES-256-GCM |
| **Bandwidth** | 1 Gbps |
| **Cloud VPC** | 10.0.0.0/16 |
| **TH DC** | 172.16.0.0/16 |
| **Failover time** | < 30 seconds |

---

## 6. High Availability & Backup

### 6.1 High Availability

| Component | Strategy | RPO | RTO |
|-----------|----------|-----|-----|
| **Load Balancer** | Active-Standby HA Pair | 0 | < 30s |
| **API Server** | Active-Active (4 nodes) + PM2 Cluster | 0 | < 30s |
| **MongoDB** | 3-node Replica Set, auto failover | 0 | < 30s |
| **Redis** | Cluster mode (3 masters, 3 replicas) | 0 | < 30s |

### 6.2 Backup Strategy

| Loại | Tần suất | Retention | Storage |
|------|----------|-----------|---------|
| **MongoDB Full Backup** | Daily 02:00 AM | 30 days | S3/Local |
| **MongoDB Oplog** | Continuous | 72 hours | Local |
| **Application Config** | Daily | 30 days | Git/S3 |
| **Media/Assets** | Continuous (S3 versioning) | 1 year | S3 |
| **Archive** | Monthly | 7 years | S3 Glacier |

---

## 7. Bảo mật hệ thống

### 7.1 Security Layers

| Layer | Giải pháp |
|-------|-----------|
| **Perimeter** | WAF, DDoS Protection, Network Firewall |
| **Network** | VLAN segmentation, VPN encryption (AES-256) |
| **Application** | @fastify/helmet, @fastify/rate-limit, @fastify/jwt |
| **Data** | Encryption at rest (AES-256), TLS 1.3 |
| **Identity** |  RBAC |

### 7.2 Encryption Standards

| Data Type | At Rest | In Transit |
|-----------|---------|------------|
| **Database** | AES-256 | TLS 1.3 |
| **Passwords** | bcrypt (cost=12) | N/A |
| **Session/API Keys** | AES-256 | TLS 1.3 |
| **Backup** | AES-256 | TLS 1.3 |

---

## 8. Giám sát và vận hành

### 8.1 Monitoring Stack

| Thành phần | Công cụ |
|------------|---------|
| **Metrics** | Prometheus / CloudWatch |
| **Logging** | ELK Stack / CloudWatch Logs |
| **Alerting** | PagerDuty / OpsGenie |
| **Dashboard** | Grafana |

### 8.2 SLA Targets

| Metric | Target |
|--------|--------|
| **Uptime** | 99.9% |
| **Response Time (p95)** | < 200ms |
| **Error Rate** | < 0.1% |
| **MTTR** | < 1 hour |

---

## 9. So sánh phương án

### 9.1 Cost Comparison (Monthly)

| Component | On-Premise | AWS Cloud |
|-----------|------------|-----------|
| **Compute** | $2,500 | $2,000 |
| **Database** | $2,000 | $1,500 (Atlas M50) |
| **Redis/Cache** | $500 | $400 |
| **Storage** | $500 | $400 |
| **Network/Security** | $1,200 | $1,100 |
| **Monitoring/Backup** | $500 | $350 |
| **Support/Admin** | $2,000 | $1,000 |
| **Total** | **~$9,200** | **~$6,750** |

### 9.2 Recommendation

**Đề xuất: Phương án Hybrid (Cloud + VPN to DC TH)**

**Lý do:**
1. Node.js Fastify servers trên Cloud - scale linh hoạt
2. MongoDB Atlas - fully managed, auto backups
3. VPN Site-to-Site - kết nối bảo mật với LsRetail
4. Chi phí hợp lý - giảm operational overhead
5. TH giữ toàn quyền quản trị
6. 99.9% SLA đảm bảo

---

*Document Version: 2.0*
*Stack: Node.js Fastify + MongoDB*
*Created: December 2025*
*Author: MangoAds Solution Team*

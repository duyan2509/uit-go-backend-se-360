# Cost Optimization Comparison Report

**Generated:** 2025-12-18 00:06:47

## Executive Summary

This report compares the cost and performance of different Azure container platforms:
- **Azure Container Apps (ACA)**: Serverless with scale-to-zero
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: With Spot instances (70% discount) and On-Demand

### Test Duration
- **Duration**: 0.07 hours (240 seconds)
- **Services Analyzed**: 6 services
- **Services**: api-gateway, user-service, driver-service, trip-service, matching-service, notification-service

## Cost Comparison

### Test Period Costs

| Platform | Total Cost | Cost per Hour | Monthly Projection | Annual Projection |
|----------|------------|---------------|---------------------|-------------------|
| Azure Container Apps (ACA) | $0.0004 | $0.0053 | $5.05 | $60.57 |
| Azure Container Instances (ACI) | $0.0108 | $0.1620 | $155.52 | $1866.24 |
| Azure Kubernetes Service (AKS) - Spot | $0.1081 | $1.6215 | $1556.61 | $18679.30 |
| Azure Kubernetes Service (AKS) - On-Demand | $0.1083 | $1.6249 | $1559.89 | $18718.72 |

### Cost Savings Analysis

**Cheapest Option**: ACA ($0.0004)

| Platform | Additional Cost vs Cheapest | Savings Percentage |
|----------|----------------------------|-------------------|
| ACA | $0.0000 | 0.0% |
| ACI | $0.0104 | 96.8% |
| AKS Spot | $0.1077 | 99.7% |
| AKS On-Demand | $0.1080 | 99.7% |

## Cost Breakdown by Service

### Azure Container Apps (ACA)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.17 | 37.86 | $0.0000 | $0.0001 | $0.0001 |
| user-service | 0.01 | 28.86 | $0.0000 | $0.0000 | $0.0000 |
| driver-service | 0.02 | 19.37 | $0.0000 | $0.0000 | $0.0000 |
| trip-service | 0.14 | 47.55 | $0.0000 | $0.0001 | $0.0001 |
| matching-service | 0.17 | 50.35 | $0.0000 | $0.0001 | $0.0001 |
| notification-service | 0.09 | 44.91 | $0.0000 | $0.0001 | $0.0001 |

### Azure Container Instances (ACI)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |
| user-service | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |
| driver-service | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |
| trip-service | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |
| matching-service | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |
| notification-service | 120.00 | 240.00 | $0.0014 | $0.0004 | $0.0018 |

### Azure Kubernetes Service (AKS) - Spot

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.17 | 37.86 | $0.0000 | $0.0000 | $0.0167 |
| user-service | 0.01 | 28.86 | $0.0000 | $0.0000 | $0.0167 |
| driver-service | 0.02 | 19.37 | $0.0000 | $0.0000 | $0.0167 |
| trip-service | 0.14 | 47.55 | $0.0000 | $0.0000 | $0.0167 |
| matching-service | 0.17 | 50.35 | $0.0000 | $0.0000 | $0.0167 |
| notification-service | 0.09 | 44.91 | $0.0000 | $0.0000 | $0.0167 |

**Additional Costs:**
- Control Plane: $0.0067
- Node Overhead: $0.0013

### Azure Kubernetes Service (AKS) - On-Demand

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.17 | 37.86 | $0.0000 | $0.0001 | $0.0167 |
| user-service | 0.01 | 28.86 | $0.0000 | $0.0000 | $0.0167 |
| driver-service | 0.02 | 19.37 | $0.0000 | $0.0000 | $0.0167 |
| trip-service | 0.14 | 47.55 | $0.0000 | $0.0001 | $0.0167 |
| matching-service | 0.17 | 50.35 | $0.0000 | $0.0001 | $0.0167 |
| notification-service | 0.09 | 44.91 | $0.0000 | $0.0001 | $0.0167 |

**Additional Costs:**
- Control Plane: $0.0067
- Node Overhead: $0.0013


## Recommendations

### Best for Cost Optimization
- **ACA** offers the lowest cost for this workload

### Platform Selection Guide

1. **Azure Container Apps (ACA)**
   - Best for: Variable traffic, microservices with scale-to-zero
   - Advantages: Automatic scaling, pay-per-use, no infrastructure management
   - Use when: Traffic is unpredictable, need to minimize idle costs

2. **Azure Container Instances (ACI)**
   - Best for: Simple container hosting, predictable workloads
   - Advantages: Simple deployment, per-second billing
   - Use when: Need simple container hosting without orchestration

3. **Azure Kubernetes Service (AKS) with Spot**
   - Best for: Stable workloads that can tolerate interruptions
   - Advantages: Significant cost savings (60-80%), full Kubernetes features
   - Use when: Need Kubernetes features, can handle spot evictions

4. **Azure Kubernetes Service (AKS) On-Demand**
   - Best for: Critical workloads requiring guaranteed availability
   - Advantages: Full control, no eviction risk
   - Use when: High availability is critical, cost is secondary

## Methodology

- **Metrics Collection**: Docker container stats collected every 10 seconds
- **Cost Calculation**: Based on Azure pricing as of 2024
- **Scale-to-Zero**: Applied for ACA (services with no traffic don't incur costs)
- **Spot Discount**: 70% discount applied for AKS Spot instances
- **Monthly Projection**: Based on average hourly cost with peak/off-peak patterns

## Notes

- Costs are estimates based on collected metrics and Azure pricing models
- Actual costs may vary based on:
  - Regional pricing differences
  - Reserved instance discounts
  - Data transfer costs
  - Storage costs (not included in this analysis)
- Performance metrics (latency, throughput) should be considered alongside costs

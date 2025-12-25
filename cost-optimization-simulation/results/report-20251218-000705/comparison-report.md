# Cost Optimization Comparison Report

**Generated:** 2025-12-18 00:15:23

## Executive Summary

This report compares the cost and performance of different Azure container platforms:
- **Azure Container Apps (ACA)**: Serverless with scale-to-zero
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: With Spot instances (70% discount) and On-Demand

### Test Duration
- **Duration**: 0.13 hours (480 seconds)
- **Services Analyzed**: 6 services
- **Services**: api-gateway, user-service, driver-service, trip-service, matching-service, notification-service

## Cost Comparison

### Test Period Costs

| Platform | Total Cost | Cost per Hour | Monthly Projection | Annual Projection |
|----------|------------|---------------|---------------------|-------------------|
| Azure Container Apps (ACA) | $0.0006 | $0.0048 | $4.60 | $55.23 |
| Azure Container Instances (ACI) | $0.0216 | $0.1620 | $155.52 | $1866.24 |
| Azure Kubernetes Service (AKS) - Spot | $0.1562 | $1.1713 | $1124.48 | $13493.80 |
| Azure Kubernetes Service (AKS) - On-Demand | $0.1566 | $1.1745 | $1127.48 | $13529.73 |

### Cost Savings Analysis

**Cheapest Option**: ACA ($0.0006)

| Platform | Additional Cost vs Cheapest | Savings Percentage |
|----------|----------------------------|-------------------|
| ACA | $0.0000 | 0.0% |
| ACI | $0.0210 | 97.0% |
| AKS Spot | $0.1555 | 99.6% |
| AKS On-Demand | $0.1560 | 99.6% |

## Cost Breakdown by Service

### Azure Container Apps (ACA)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.22 | 69.94 | $0.0000 | $0.0001 | $0.0001 |
| user-service | 0.01 | 53.31 | $0.0000 | $0.0001 | $0.0001 |
| driver-service | 0.04 | 30.66 | $0.0000 | $0.0000 | $0.0000 |
| trip-service | 0.27 | 87.87 | $0.0000 | $0.0001 | $0.0001 |
| matching-service | 0.29 | 93.27 | $0.0000 | $0.0001 | $0.0001 |
| notification-service | 0.17 | 83.05 | $0.0000 | $0.0001 | $0.0001 |

### Azure Container Instances (ACI)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |
| user-service | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |
| driver-service | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |
| trip-service | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |
| matching-service | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |
| notification-service | 240.00 | 480.00 | $0.0029 | $0.0007 | $0.0036 |

### Azure Kubernetes Service (AKS) - Spot

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.22 | 69.94 | $0.0000 | $0.0000 | $0.0234 |
| user-service | 0.01 | 53.31 | $0.0000 | $0.0000 | $0.0234 |
| driver-service | 0.04 | 30.66 | $0.0000 | $0.0000 | $0.0233 |
| trip-service | 0.27 | 87.87 | $0.0000 | $0.0000 | $0.0234 |
| matching-service | 0.29 | 93.27 | $0.0000 | $0.0000 | $0.0234 |
| notification-service | 0.17 | 83.05 | $0.0000 | $0.0000 | $0.0234 |

**Additional Costs:**
- Control Plane: $0.0133
- Node Overhead: $0.0027

### Azure Kubernetes Service (AKS) - On-Demand

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.22 | 69.94 | $0.0000 | $0.0001 | $0.0234 |
| user-service | 0.01 | 53.31 | $0.0000 | $0.0001 | $0.0234 |
| driver-service | 0.04 | 30.66 | $0.0000 | $0.0000 | $0.0234 |
| trip-service | 0.27 | 87.87 | $0.0000 | $0.0001 | $0.0235 |
| matching-service | 0.29 | 93.27 | $0.0000 | $0.0001 | $0.0235 |
| notification-service | 0.17 | 83.05 | $0.0000 | $0.0001 | $0.0235 |

**Additional Costs:**
- Control Plane: $0.0133
- Node Overhead: $0.0027


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

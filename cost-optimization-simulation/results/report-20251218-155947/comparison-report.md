# Cost Optimization Comparison Report

**Generated:** 2025-12-18 16:30:14

## Executive Summary

This report compares the cost and performance of different Azure container platforms:
- **Azure Container Apps (ACA)**: Serverless with scale-to-zero
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: With Spot instances (70% discount) and On-Demand

### Test Duration
- **Duration**: 0.50 hours (1800 seconds)
- **Services Analyzed**: 6 services
- **Services**: api-gateway, user-service, driver-service, trip-service, matching-service, notification-service

## Cost Comparison

### Test Period Costs

| Platform | Total Cost | Cost per Hour | Monthly Projection | Annual Projection |
|----------|------------|---------------|---------------------|-------------------|
| Azure Container Apps (ACA) | $0.0028 | $0.0056 | $5.35 | $64.15 |
| Azure Container Instances (ACI) | $0.0810 | $0.1620 | $155.52 | $1866.24 |
| Azure Kubernetes Service (AKS) - Spot | $0.4208 | $0.8416 | $807.95 | $9695.34 |
| Azure Kubernetes Service (AKS) - On-Demand | $0.4227 | $0.8454 | $811.55 | $9738.61 |

### Cost Savings Analysis

**Cheapest Option**: ACA ($0.0028)

| Platform | Additional Cost vs Cheapest | Savings Percentage |
|----------|----------------------------|-------------------|
| ACA | $0.0000 | 0.0% |
| ACI | $0.0782 | 96.6% |
| AKS Spot | $0.4180 | 99.3% |
| AKS On-Demand | $0.4199 | 99.3% |

## Cost Breakdown by Service

### Azure Container Apps (ACA)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 3.49 | 260.52 | $0.0000 | $0.0004 | $0.0004 |
| user-service | 0.04 | 174.54 | $0.0000 | $0.0003 | $0.0003 |
| driver-service | 0.14 | 162.18 | $0.0000 | $0.0002 | $0.0002 |
| trip-service | 16.28 | 302.89 | $0.0002 | $0.0005 | $0.0006 |
| matching-service | 12.90 | 272.84 | $0.0002 | $0.0004 | $0.0006 |
| notification-service | 14.12 | 307.41 | $0.0002 | $0.0005 | $0.0006 |

### Azure Container Instances (ACI)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |
| user-service | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |
| driver-service | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |
| trip-service | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |
| matching-service | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |
| notification-service | 900.00 | 1800.00 | $0.0108 | $0.0027 | $0.0135 |

### Azure Kubernetes Service (AKS) - Spot

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 3.49 | 260.52 | $0.0000 | $0.0001 | $0.0601 |
| user-service | 0.04 | 174.54 | $0.0000 | $0.0001 | $0.0601 |
| driver-service | 0.14 | 162.18 | $0.0000 | $0.0001 | $0.0601 |
| trip-service | 16.28 | 302.89 | $0.0001 | $0.0001 | $0.0602 |
| matching-service | 12.90 | 272.84 | $0.0001 | $0.0001 | $0.0602 |
| notification-service | 14.12 | 307.41 | $0.0001 | $0.0001 | $0.0602 |

**Additional Costs:**
- Control Plane: $0.0500
- Node Overhead: $0.0100

### Azure Kubernetes Service (AKS) - On-Demand

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 3.49 | 260.52 | $0.0000 | $0.0004 | $0.0604 |
| user-service | 0.04 | 174.54 | $0.0000 | $0.0002 | $0.0602 |
| driver-service | 0.14 | 162.18 | $0.0000 | $0.0002 | $0.0602 |
| trip-service | 16.28 | 302.89 | $0.0002 | $0.0004 | $0.0606 |
| matching-service | 12.90 | 272.84 | $0.0002 | $0.0004 | $0.0606 |
| notification-service | 14.12 | 307.41 | $0.0002 | $0.0004 | $0.0606 |

**Additional Costs:**
- Control Plane: $0.0500
- Node Overhead: $0.0100


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

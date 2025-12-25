# Cost Optimization Comparison Report

**Generated:** 2025-12-16 14:11:09

## Executive Summary

This report compares the cost and performance of different Azure container platforms:
- **Azure Container Apps (ACA)**: Serverless with scale-to-zero
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: With Spot instances (70% discount) and On-Demand

### Test Duration
- **Duration**: 0.01 hours (30 seconds)
- **Services Analyzed**: 6 services
- **Services**: api-gateway, user-service, driver-service, trip-service, matching-service, notification-service

## Cost Comparison

### Test Period Costs

| Platform | Total Cost | Cost per Hour | Monthly Projection | Annual Projection |
|----------|------------|---------------|---------------------|-------------------|
| Azure Container Apps (ACA) | $0.0001 | $0.0085 | $8.19 | $98.28 |
| Azure Container Instances (ACI) | $0.0014 | $0.1620 | $155.52 | $1866.24 |
| Azure Kubernetes Service (AKS) - Spot | $0.0660 | $7.9224 | $7605.51 | $91266.12 |
| Azure Kubernetes Service (AKS) - On-Demand | $0.0661 | $7.9280 | $7610.90 | $91330.81 |

### Cost Savings Analysis

**Cheapest Option**: ACA ($0.0001)

| Platform | Additional Cost vs Cheapest | Savings Percentage |
|----------|----------------------------|-------------------|
| ACA | $0.0000 | 0.0% |
| ACI | $0.0013 | 94.7% |
| AKS Spot | $0.0659 | 99.9% |
| AKS On-Demand | $0.0660 | 99.9% |

## Cost Breakdown by Service

### Azure Container Apps (ACA)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.36 | 8.43 | $0.0000 | $0.0000 | $0.0000 |
| user-service | 0.00 | 2.85 | $0.0000 | $0.0000 | $0.0000 |
| driver-service | 0.00 | 2.08 | $0.0000 | $0.0000 | $0.0000 |
| trip-service | 0.03 | 10.41 | $0.0000 | $0.0000 | $0.0000 |
| matching-service | 0.04 | 9.90 | $0.0000 | $0.0000 | $0.0000 |
| notification-service | 0.03 | 10.06 | $0.0000 | $0.0000 | $0.0000 |

### Azure Container Instances (ACI)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |
| user-service | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |
| driver-service | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |
| trip-service | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |
| matching-service | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |
| notification-service | 15.00 | 30.00 | $0.0002 | $0.0000 | $0.0002 |

### Azure Kubernetes Service (AKS) - Spot

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.36 | 8.43 | $0.0000 | $0.0000 | $0.0108 |
| user-service | 0.00 | 2.85 | $0.0000 | $0.0000 | $0.0108 |
| driver-service | 0.00 | 2.08 | $0.0000 | $0.0000 | $0.0108 |
| trip-service | 0.03 | 10.41 | $0.0000 | $0.0000 | $0.0108 |
| matching-service | 0.04 | 9.90 | $0.0000 | $0.0000 | $0.0108 |
| notification-service | 0.03 | 10.06 | $0.0000 | $0.0000 | $0.0108 |

**Additional Costs:**
- Control Plane: $0.0008
- Node Overhead: $0.0002

### Azure Kubernetes Service (AKS) - On-Demand

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.36 | 8.43 | $0.0000 | $0.0000 | $0.0108 |
| user-service | 0.00 | 2.85 | $0.0000 | $0.0000 | $0.0108 |
| driver-service | 0.00 | 2.08 | $0.0000 | $0.0000 | $0.0108 |
| trip-service | 0.03 | 10.41 | $0.0000 | $0.0000 | $0.0108 |
| matching-service | 0.04 | 9.90 | $0.0000 | $0.0000 | $0.0108 |
| notification-service | 0.03 | 10.06 | $0.0000 | $0.0000 | $0.0108 |

**Additional Costs:**
- Control Plane: $0.0008
- Node Overhead: $0.0002


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

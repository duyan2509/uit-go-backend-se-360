# Cost Optimization Comparison Report

**Generated:** 2025-12-17 23:57:15

## Executive Summary

This report compares the cost and performance of different Azure container platforms:
- **Azure Container Apps (ACA)**: Serverless with scale-to-zero
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: With Spot instances (70% discount) and On-Demand

### Test Duration
- **Duration**: 0.03 hours (120 seconds)
- **Services Analyzed**: 6 services
- **Services**: api-gateway, user-service, driver-service, trip-service, matching-service, notification-service

## Cost Comparison

### Test Period Costs

| Platform | Total Cost | Cost per Hour | Monthly Projection | Annual Projection |
|----------|------------|---------------|---------------------|-------------------|
| Azure Container Apps (ACA) | $0.0002 | $0.0057 | $5.44 | $65.23 |
| Azure Container Instances (ACI) | $0.0054 | $0.1620 | $155.52 | $1866.24 |
| Azure Kubernetes Service (AKS) - Spot | $0.0841 | $2.5216 | $2420.72 | $29048.60 |
| Azure Kubernetes Service (AKS) - On-Demand | $0.0842 | $2.5253 | $2424.26 | $29091.06 |

### Cost Savings Analysis

**Cheapest Option**: ACA ($0.0002)

| Platform | Additional Cost vs Cheapest | Savings Percentage |
|----------|----------------------------|-------------------|
| ACA | $0.0000 | 0.0% |
| ACI | $0.0052 | 96.5% |
| AKS Spot | $0.0839 | 99.8% |
| AKS On-Demand | $0.0840 | 99.8% |

## Cost Breakdown by Service

### Azure Container Apps (ACA)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.10 | 20.38 | $0.0000 | $0.0000 | $0.0000 |
| user-service | 0.00 | 15.51 | $0.0000 | $0.0000 | $0.0000 |
| driver-service | 0.01 | 10.42 | $0.0000 | $0.0000 | $0.0000 |
| trip-service | 0.08 | 25.56 | $0.0000 | $0.0000 | $0.0000 |
| matching-service | 0.09 | 27.08 | $0.0000 | $0.0000 | $0.0000 |
| notification-service | 0.05 | 24.13 | $0.0000 | $0.0000 | $0.0000 |

### Azure Container Instances (ACI)

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |
| user-service | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |
| driver-service | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |
| trip-service | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |
| matching-service | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |
| notification-service | 60.00 | 120.00 | $0.0007 | $0.0002 | $0.0009 |

### Azure Kubernetes Service (AKS) - Spot

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.10 | 20.38 | $0.0000 | $0.0000 | $0.0133 |
| user-service | 0.00 | 15.51 | $0.0000 | $0.0000 | $0.0133 |
| driver-service | 0.01 | 10.42 | $0.0000 | $0.0000 | $0.0133 |
| trip-service | 0.08 | 25.56 | $0.0000 | $0.0000 | $0.0133 |
| matching-service | 0.09 | 27.08 | $0.0000 | $0.0000 | $0.0133 |
| notification-service | 0.05 | 24.13 | $0.0000 | $0.0000 | $0.0133 |

**Additional Costs:**
- Control Plane: $0.0033
- Node Overhead: $0.0007

### Azure Kubernetes Service (AKS) - On-Demand

| Service | vCPU-seconds | Memory GB-seconds | vCPU Cost | Memory Cost | Total Cost |
|---------|--------------|-------------------|-----------|-------------|------------|
| api-gateway | 0.10 | 20.38 | $0.0000 | $0.0000 | $0.0134 |
| user-service | 0.00 | 15.51 | $0.0000 | $0.0000 | $0.0134 |
| driver-service | 0.01 | 10.42 | $0.0000 | $0.0000 | $0.0133 |
| trip-service | 0.08 | 25.56 | $0.0000 | $0.0000 | $0.0134 |
| matching-service | 0.09 | 27.08 | $0.0000 | $0.0000 | $0.0134 |
| notification-service | 0.05 | 24.13 | $0.0000 | $0.0000 | $0.0134 |

**Additional Costs:**
- Control Plane: $0.0033
- Node Overhead: $0.0007


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

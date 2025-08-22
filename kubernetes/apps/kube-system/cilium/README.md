# Cilium CNI with BGP Integration

## Overview
This directory contains the Cilium CNI configuration with BGP integration for MikroTik router peering.

## BGP Configuration
- **Kubernetes ASN**: 64514 (Cilium local ASN)
- **MikroTik ASN**: 64513 (peer ASN)
- **Peer Address**: 172.16.20.1 (MikroTik router on VLAN20)

## LoadBalancer Services
- **IP Pool**: 172.16.20.220/30 (172.16.20.220-223)
- **Advertisement**: Automatic BGP advertisement of LoadBalancer service IPs
- **High Availability**: ECMP routing across all Kubernetes nodes

## Features Enabled
- ✅ BGP Control Plane (`bgpControlPlane: enabled: true`)
- ✅ Pod CIDR Advertisement (10.41.x.0/24 networks)
- ✅ LoadBalancer Service Advertisement
- ✅ Gateway API support
- ✅ L2 Announcements
- ✅ DSR Load Balancing

## Network Flow
```
External Client → MikroTik (172.16.20.1) → BGP ECMP → K8s LoadBalancer → Pod
172.16.x.x     →     BGP Router         →   Routes  →   172.16.20.220    → 10.41.x.x
```

## Verification Commands
```bash
# Check BGP peer status
kubectl exec -n kube-system cilium-xxx -- cilium bgp peers

# Check BGP routes
kubectl exec -n kube-system cilium-xxx -- cilium bgp routes

# View LoadBalancer services
kubectl get svc -A --field-selector spec.type=LoadBalancer
```

## MikroTik Configuration
The MikroTik router must be configured with:
- BGP instance with ASN 64513
- BGP connections to all Kubernetes nodes (172.16.20.201, 202, 203)
- Filter rules accepting pod networks (10.41.0.0/16) and LoadBalancer IPs (172.16.20.220/30)

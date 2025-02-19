# Lab EC2 Internet Access using Nat Gateway

## 1. Create a VPC

```sh
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query Vpc.VpcId --output text )
```

## 2. Create a subnet

```sh
SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.0.0/24 --availability-zone $MY_ZONE --query Subnet.SubnetId --output text)
```

## 3. Create an EC2

```sh
VM_ID=$(aws ec2 run-instances --instance-type t2.micro --subnet-id $SUBNET_ID --image-id  ami-09d3b3274b6c5d4aa --query Instances[0].InstanceId --output text)
```

## 4. Allocate an Elastic IP Address

```sh
ALLOCATION_ID=$(aws ec2 allocate-address --domain vpc --query AllocationId --output text)
```

## 5. Create Nat Gateway

```sh
NAT_GATEWAY_ID=$(aws ec2 create-nat-gateway --subnet-id $SUBNET_ID --allocation-id $ALLOCATION_ID --query 'NatGateway.NatGatewayId' --output text)
```

## 6. Create a Routing Table

```sh
RTT=$(aws ec2 create-route-table --vpc-id $VPC_ID --query RouteTable.RouteTableId --output text )
```

## 7. Associate route table to subnet

```sh
aws ec2 associate-route-table --subnet-id $SUBNET_ID --route-table-id $RTT
```

## 8. Add a Route to Route Table

```sh
aws ec2 create-route --route-table-id $RTT --destination-cidr-block 0.0.0.0/0 --nat-gateway-id $NAT_GATEWAY_ID
```

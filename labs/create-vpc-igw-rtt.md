# VPC, Internet Gateway and Routing Tables

## 1. Create a VPC

```sh
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query Vpc.VpcId --output text )
```

## 2. Create an Internet Gateway

```sh
IGW=$(aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text)
```

## 3. Create a Routing Table

```sh
RTT=$(aws ec2 create-route-table --vpc-id $VPC_ID --query RouteTable.RouteTableId --output text )
```

## 4. Attach an Internet Gateway to VPC

```sh
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW
```

## 5. Add a Route to Route Table

```sh
aws ec2 create-route --route-table-id $RTT --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW
```

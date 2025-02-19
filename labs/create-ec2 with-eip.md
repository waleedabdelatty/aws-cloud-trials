# Lab Create an EC2 with Elastic IP

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
EIP_ID=$(aws ec2 allocate-address --domain vpc --query AllocationId --output text)
```

## 5. List IP Addresses

```sh
aws ec2 describe-addresses
```

## 6. Create an Internet Gateway

```sh
IGW=$(aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text)
```

## 7. Attach an Internet Gateway to VPC

```sh
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW
```

## 8. Create a Network IFace

```sh
NT_IFACE_ID=$(aws ec2 create-network-interface --subnet-id $SUBNET_ID --description "WAN_IFACE" --query NetworkInterface.NetworkInterfaceId --output text)
```

## 9. Associate the Elastic IP with the Network Interface

```sh
ASSOCIATION_ID=$(aws ec2 associate-address --network-interface-id $NT_IFACE_ID --allocation-id $EIP_ID --query AssociationId --output text)
```

## 10. Attach the ENI to the EC2 Instance

```sh
aws ec2 attach-network-interface --network-interface-id $NT_IFACE_ID --instance-id $VM_ID --device-index 1
```

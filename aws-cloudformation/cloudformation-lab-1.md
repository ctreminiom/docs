# CloudFormation - Lab \#1

## Create the Stack

```yaml
Resources:
  Ec2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-467ca739 # Amazon Linux AMI in N.Virginia
      Tags:
        - Key: Name
          Value: A simple exampleU
```

![](../.gitbook/assets/image%20%285%29.png)

![](../.gitbook/assets/image%20%289%29.png)

![](../.gitbook/assets/image%20%287%29.png)

![](../.gitbook/assets/image%20%281%29.png)

![](../.gitbook/assets/image%20%2812%29.png)



## Update the Stack

```yaml
Resources:
  Ec2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-467ca739 # Amazon Linux AMI in N.Virginia
      Tags:
        - Key: Name
          Value: A simple example
        - Key: Description
          Value: A simple stack update
```

![](../.gitbook/assets/image.png)

![](../.gitbook/assets/image%20%284%29.png)

![](../.gitbook/assets/image%20%282%29.png)


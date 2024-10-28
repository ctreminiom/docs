# CloudFormation - Lab #1

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

![](<../.gitbook/assets/image (6) (1) (1).png>)

![](<../.gitbook/assets/image (10).png>)

![](<../.gitbook/assets/image (8) (1) (1).png>)

![](<../.gitbook/assets/image (2) (1).png>)

![](<../.gitbook/assets/image (13).png>)



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

![](<../.gitbook/assets/image (1) (1).png>)

![](<../.gitbook/assets/image (5).png>)

![](<../.gitbook/assets/image (3).png>)

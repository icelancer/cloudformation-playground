**EC2 인스턴스 생성 클라우드 포메이션**

**요구사항**

- 선택한 환경 (development, production)에 따라 인스턴스 타입을 지정할 수 있어야 한다.
  - development: t3.micro
  - production: t3.small
- EC2 인스턴스에 적용할 보안 그룹을 만들어야 한다.
  - 접속 가능한 포트는 22번과 80번 포트만 가능해야 한다.

**파라메터**

- 환경 선택 (development, production)

```yaml
Parameters:
  Environment:
    Description: Environment Name
    Type: String
    AllowedValues: [development, production]
    ConstraintDescription: must be development or production
```



**매핑**

- 환경 선택에 따라 인스턴스 타입을 결정할 수 있도록 정의한다.

```yaml
Mappings:
  EnvironmentToInstanceType:
    development:
      instanceType: t3.micro
    production:
      instanceType: t3.small
```



**리소스**

```yaml
Resources:
  EC2Server:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0b69ea66ff7391e80
      InstanceType: !FindInMap [EnvironmentToInstanceType, !Ref Environment, instanceType]
      KeyName: icelancer # EC2 접근에 필요한 키 페어
      SecurityGroupIds:
      - !Ref EC2SecurityGroup
      Tags:
      - Key: Name
        Value: !Ref AWS::StackName

  EC2SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 Basic Security Group. Created By CloudFormation.
      GroupName: cf-sg-ec2-basic
      SecurityGroupIngress:
        - CidrIp: 0.0.0.0/0
          FromPort: 80
          ToPort: 80
          IpProtocol: tcp
        - CidrIp: 0.0.0.0/0
          FromPort: 22
          ToPort: 22
          IpProtocol: tcp
```


# CfnCreatingVPC
VPC環境作成用の、CloudFormation テンプレート

# 単一VPC作成のCloudFormationテンプレート
テンプレートは以下の３種類あります。
- 2subnets in a VPC
- 4subnets in a VPC
- 6subnets in a VPC

4サブネット(IGWあり、NATGatewayなしパターンのスタック作成例)
```shell
CFN_STACK_PARAMETERS='
[
  {
    "ParameterKey": "DnsHostnames",
    "ParameterValue": "true"
  },
  {
    "ParameterKey": "DnsSupport",
    "ParameterValue": "true"
  },
  {
    "ParameterKey": "InternetAccess",
    "ParameterValue": "true"
  },
  {
    "ParameterKey": "EnableNatGW",
    "ParameterValue": "false"
  },
  {
    "ParameterKey": "VpcName",
    "ParameterValue": "TestVPC"
  }
]'

aws --profile ${PROFILE} cloudformation create-stack \
    --stack-name VPC \
    --template-body "file://./vpc-4subnets.yaml" \
    --parameters "${CFN_STACK_PARAMETERS}" \
    --capabilities CAPABILITY_IAM ;
```

- パラメータ説明
    - InternetAccess : trueにするとIGWがアタッチされます
    - EnableNatGW : InternetAccessとEnableNatGW両方がTrueの場合, NatGWが作成されます
    - DnsSupport : VPCのDnsSupportの有効・無効設定
    - DnsHostnames : VPCのDnsHostnamesの有効・無効設定
    - VpcInternalDnsNameEnable : Private Hosted Zoneの作成有無(true:作成する、false:作成しない。デフォルトfalse)

# 複合型VPC作成のCloudFormationテンプレート
４つのサブネットを持つInternalVPCと、Internetフェンシングする2サブネットを持つExternalVPCを作成し、VPC Peeringで接続する構成です。
<img src="./Documents/arch1.png" whdth=500>

利用方法
```shell
aws --profile ${PROFILE} cloudformation create-stack \
    --stack-name VPC \
    --template-body "file://./2VPCEnvironment/2vpc_2subnet-4subnets.yaml" \
    --capabilities CAPABILITY_IAM ;
```

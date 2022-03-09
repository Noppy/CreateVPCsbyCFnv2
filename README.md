# CfnCreatingVPC
VPC環境作成用の、CloudFormation テンプレート

# 単一VPC作成のCloudFormationテンプレート
テンプレートは以下の4種類あります。
- サブネットタイプが１種類の構成
  - 2AZ、2サブネットVPC: `vpc-2az-2subnets.yaml`
    - Publicサブネット x 2
- サブネットタイプが2種類の構成
  - 2AZ、4サブネットVPC: `vpc-2az-4subnets.yaml`
    - Publicサブネット x 2、Privateサブネット x 2
  - 3AZ、6サブネットVPC: `vpc-3az-6subnets.yaml`
    - Publicサブネット x 3、Privateサブネット x 3
- サブネットタイプが3種類の構成
  - 2AZ、6サブネットVPC: `vpc-2az-6subnets.yaml`
    - Publicサブネット x 2、Privateサブネット x 2、TransitGWサブネット x 2

# 作成例
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
    --template-body "file://./vpc-2az-4subnets.yaml" \
    --parameters "${CFN_STACK_PARAMETERS}" \
    --capabilities CAPABILITY_IAM ;
```

- パラメータ説明
    - InternetAccess : trueにするとIGWがアタッチされます
    - EnableNatGW : InternetAccessとEnableNatGW両方がTrueの場合, NatGWが作成されます
    - DnsSupport : VPCのDnsSupportの有効・無効設定
    - DnsHostnames : VPCのDnsHostnamesの有効・無効設定
    - VpcInternalDnsNameEnable : Private Hosted Zoneの作成有無(true:作成する、false:作成しない。デフォルトfalse)


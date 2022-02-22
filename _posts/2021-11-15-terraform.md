---
toc: true
layout: post
description: section1
categories: [aws, terraform]
title: Terraform
---

Terraform with AWS

---

# 1. DevOps

<br><br>
---
# 2. Terraform

## 2.1. 구성요소
- resource : 실제로 생성할 인프라 자원  
ex) aws_security_group, aws_lb, aws_instance  

- provider : Terraform으로 정의할 Infrastructure Provider  
[각종 providers](https://www.terraform.io/docs/providers/index.html)  

- output : 인프라를 provisioning 한 후에 생성된 자원을 스크립트로 추출  
추출한 script는 이후에 remote state에서 활용 가능  

- backend : terraform의 상태를 저장하는 공간(default local)  
현재 배포된 최신 상태를 외부에 저장하기 때문에 다른 사람과의 협업이 가능 (S3...등)  

- module : 공통적으로 활용할 수 있는 인프라 코드를 한 곳으로 모아서 정의한 함수  
변수만 바꿔서 동일한 리소스를 손쉽게 생성 가능  

- remote state : remote state를 사용하면 VPC, IAM 등과 같은 공용 서비스를 다른 서비스에서 참조 가능, tfstate파일(최신 테라폼 상태정보)이 저장되어 있는 backend 정보를 명시하면, terraform이 해당 backend에서 output 정보들을 가져옴  


## 2.2. 작동 원리

### Form  
- Local 코드 : 현재 개발자가 작성/수정하고 있는 코드  
- AWS 실제 인프라 : 실제로 AWS에 배포되어 있는 인프라  
- Backend에 저장된 상태 : 가장 최근에 배포한 테라폼 코드(tfstate파일) 형상  

- Terraform init
지정한 backend에 상태 저장을 위한 .tfstate 파일을 생성되고 가장 마지막에 적용한 테라폼 내역이 저장  
init 작업을 완료하면, local에는 .tfstate에 정의된 내용을 담은 .terraform 파일이 생성됩니다.
기존에 다른 개발자가 이미 .tfstate에 인프라를 정의해 놓은 것이 있다면, 다른 개발자는 init작업을 통해서 local에 sync를 맞출 수 있습니다.

- Terraform plan
정의한 코드가 어떤 인프라를 만들게 되는지 미리 예측 결과를 보여줍니다. 단, plan을 한 내용에 에러가 없다고 하더라도, 실제 적용되었을 때는 에러가 발생할 수 있습니다.
Plan 명령어는 어떠한 형상에도 변화를 주지 않습니다.


- Terraform apply
실제로 인프라를 배포하기 위한 명령어입니다. apply를 완료하면, AWS 상에 실제로 해당 인프라가 생성되고 작업 결과가 backend의 .tfstate 파일에 저장됩니다.
해당 결과는 local의 .terraform 파일에도 저장됩니다.


- Terraform import
AWS 인프라에 배포된 리소스를 terraform state로 옮겨주는 작업입니다.
이는 local의 .terraform에 해당 리소스의 상태 정보를 저장해주는 역할을 합니다. (절대 코드를 생성해주지 않습니다.)
Apply 전까지는 backend에 저장되지 않습니다.
Import 이후에 plan을 하면 로컬에 해당 코드가 없기 때문에 리소스가 삭제 또는 변경된다는 결과를 보여줍니다. 이 결과를 바탕으로 코드를 작성하실 수 있습니다.





## 2.3. 구동

### provider 생성  
```
] cat providers.tf 
provider "aws" { 
	region = "eu-central-1" 
}
```

### init  
```
] terraform init
Initializing the backend...
Terraform has been successfully initialized!
```

### resource 생성  
```
] cat s3.tf
resource "aws_s3_bucket" "test" {
	bucket = "terraform220222"
}
```

### plan  
```
] terraform plan
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.test will be created
  + resource "aws_s3_bucket" "test" {
      + acceleration_status                  = (known after apply)
      + acl                                  = (known after apply)
      + arn                                  = (known after apply)
      + bucket                               = "terraform220222"
      + bucket_domain_name                   = (known after apply)
      + bucket_regional_domain_name          = (known after apply)
      + cors_rule                            = (known after apply)
      + force_destroy                        = false
      + grant                                = (known after apply)
      + hosted_zone_id                       = (known after apply)
      + id                                   = (known after apply)
      + lifecycle_rule                       = (known after apply)
      + logging                              = (known after apply)
      + policy                               = (known after apply)
      + region                               = (known after apply)
      + replication_configuration            = (known after apply)
      + request_payer                        = (known after apply)
      + server_side_encryption_configuration = (known after apply)
      + tags_all                             = (known after apply)
      + versioning                           = (known after apply)
      + website                              = (known after apply)
      + website_domain                       = (known after apply)
      + website_endpoint                     = (known after apply)

      + object_lock_configuration {
          + object_lock_enabled = (known after apply)
          + rule                = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```


### apply  
```
] terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.test will be created
  + resource "aws_s3_bucket" "test" {
      + acceleration_status                  = (known after apply)
      + acl                                  = (known after apply)
      + arn                                  = (known after apply)
      + bucket                               = "terraform220222"
      + bucket_domain_name                   = (known after apply)
      + bucket_regional_domain_name          = (known after apply)
      + cors_rule                            = (known after apply)
      + force_destroy                        = false
      + grant                                = (known after apply)
      + hosted_zone_id                       = (known after apply)
      + id                                   = (known after apply)
      + lifecycle_rule                       = (known after apply)
      + logging                              = (known after apply)
      + policy                               = (known after apply)
      + region                               = (known after apply)
      + replication_configuration            = (known after apply)
      + request_payer                        = (known after apply)
      + server_side_encryption_configuration = (known after apply)
      + tags_all                             = (known after apply)
      + versioning                           = (known after apply)
      + website                              = (known after apply)
      + website_domain                       = (known after apply)
      + website_endpoint                     = (known after apply)

      + object_lock_configuration {
          + object_lock_enabled = (known after apply)
          + rule                = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
Enter a value: yes
aws_s3_bucket.test: Creating...
aws_s3_bucket.test: Creation complete after 3s [id=terraform220222]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

terraform.tfstate 파일이 생성된 거 확인 가능  

```
] aws s3 ls  # s3 생성된 거 확인
2022-02-22 16:22:24 terraform220222
```

### import 준비  

이전에 먼저 만든 s3 리소스 tf 파일(s3.tf) 날리고 시작
```
] rm s3.tf
] terraform plan
aws_s3_bucket.test: Refreshing state... [id=terraform220222]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the
last "terraform apply":

  # aws_s3_bucket.test has changed
  ~ resource "aws_s3_bucket" "test" {
        id                                   = "terraform220222"
      + tags                                 = {}
        # (18 unchanged attributes hidden)
    }


Unless you have made equivalent changes to your configuration, or ignored the
relevant attributes using ignore_changes, the following plan may include
actions to undo or respond to these changes.

───────────────────────────────────────────────────────────────────────────────

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy # 삭제된거 확인

Terraform will perform the following actions:

  # aws_s3_bucket.test will be destroyed # s3 삭제할거라 함
  # (because aws_s3_bucket.test is not in configuration)
  - resource "aws_s3_bucket" "test" {
      - acl                                  = "private" -> null
      - arn                                  = "arn:aws:s3:::terraform220222" -> null
      - bucket                               = "terraform220222" -> null
      - bucket_domain_name                   = "terraform220222.s3.amazonaws.com" -> null
      - bucket_regional_domain_name          = "terraform220222.s3.eu-central-1.amazonaws.com" -> null
      - cors_rule                            = [] -> null
      - force_destroy                        = false -> null
      - grant                                = [] -> null
      - hosted_zone_id                       = "Z21DNDUVLTQW6Q" -> null
      - id                                   = "terraform220222" -> null
      - lifecycle_rule                       = [] -> null
      - logging                              = [] -> null
      - region                               = "eu-central-1" -> null
      - replication_configuration            = [] -> null
      - request_payer                        = "BucketOwner" -> null
      - server_side_encryption_configuration = [] -> null
      - tags                                 = {} -> null
      - tags_all                             = {} -> null
      - versioning                           = [
          - {
              - enabled    = false
              - mfa_delete = false
            },
        ] -> null
      - website                              = [] -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
```
state상으로는 s3가 남아있으나 실제는 destroy되었기 때문에,  
Objects have changed라는 메시지와 함께 삭제할거라 함  


state파일도 지우고 terraform init 상태로 만들어서 plan 확인
```
] rm -f terraform.tfstate
] terraform init
] terraform plan
No changes. Your infrastructure matches the configuration.
Terraform has compared your real infrastructure against your configuration and
found no differences, so no changes are needed.
```
아무것도 안만들어진 상태로 나옴

여기서 부터 기존 리소스 import 시작

### 실제 import
```
] terraform import aws_s3_bucket.test terraform220222 # 코드 공식문서 참조
Error: resource address "aws_s3_bucket.test" does not exist in the configuration.

Before importing this resource, please create its configuration in the root module. For example:
resource "aws_s3_bucket" "test" {
  # (resource arguments)
}
```

위에 에러 가이드 대로, resource 파일(s3.tf) 재생성 후 import  
```
] terraform import aws_s3_bucket.test terraform220222 # 다시 import
aws_s3_bucket.test: Importing from ID "terraform220222"...
aws_s3_bucket.test: Import prepared!
  Prepared aws_s3_bucket for import
aws_s3_bucket.test: Refreshing state... [id=terraform220222]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

```
]  terraform plan
aws_s3_bucket.test: Refreshing state... [id=terraform220222]
No changes. Your infrastructure matches the configuration.

] terraform apply
aws_s3_bucket.test: Refreshing state... [id=terraform220222]
No changes. Your infrastructure matches the configuration.
Terraform has compared your real infrastructure against your configuration and
found no differences, so no changes are needed.
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

] terraform state list
aws_s3_bucket.test
```


<br><br>
---
# 3. Terraform으로 AWS resource 생성

## 3.1. 


<br><br>
---
# 4. Terraform Advancement


<br><br>
---

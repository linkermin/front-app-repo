# Amazon EKS Demo 
This repo holds **React Frontend sample** for [Amazon EKS Hands on Lab](https://master.d3s71i2n51x60t.amplifyapp.com/ko/)

## 1. OIDC 및 권한구성

---

<aside>
📌 서비스 계정에 IAM 역할을 사용하려면 클러스터에 IAM OIDC 공급자가 있어야 합니다.

</aside>

1. 클러스터에 기존 IAM OIDC 공급자가 있는지 확인합니다.
    - my-cluster 값을 수정 합니다.
        
        ```bash
        aws eks describe-cluster --name my-cluster --query "cluster.identity.oidc.issuer" --output text
        ```
        
    - 출력 예시
        
        ```bash
        https://oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE
        ```
        
2. 아래 명령에서 이전 명령에서 반환된 값으로 바꿉니다.
    
    ```bash
    aws iam list-open-id-connect-providers | grep EXAMPLED539D4633E53DE1B71EXAMPLE
    ```
    
    - 출력 예시
        
        ```bash
        "Arn": "arn:aws:iam::111122223333:oidc-provider/oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE"
        ```
        
        > 위와 같이 출력이 반환된 경우 클러스터에 대한 공급자가 이미 있는것 입니다.
        출력이 반환되지 않은 경우 IAM OIDC 공급자를 생성해야 합니다.



## 3. 서브넷 태그 지정

---

[자동 서브넷 검색을 위해 Amazon EKS 클러스터의 Amazon VPC 서브넷에 태그 지정](https://aws.amazon.com/ko/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/)

1. 노드그룹 서브넷에 태그 지정
    - VPC - 서브넷 - 서브넷 선택 - 태그관리
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af629a8d-2c77-47ff-8758-ce17130d62f1/Untitled.png)
        
    - EKS의 로드 밸런서 리소스에서 퍼블릭 및 프라이빗 서브넷의 태그 지정
        - Cluster-name 수정필요
        
         Key: [kubernetes.io/cluster/cluster-name](http://kubernetes.io/cluster/cluster-name)
        Value: shared
        
    - EKS의 내부 로드 밸런서가 사용하는 프라이빗 서브넷의 태그 지정
        
        Key: [kubernetes.io/role/internal-elb](http://kubernetes.io/role/internal-elb)
        Value: 1
        
    - EKS의 외부 로드 밸런서가 사용하는 퍼블릭 서브넷의 태그 지정
        
        Key: [kubernetes.io/role/elb](http://kubernetes.io/role/elb)
        Value: 1

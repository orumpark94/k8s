# k8s 자동 배포 시스템

# 구성목표
Git action / ansible을 이용하여 1 Master node / 2 Worker node 의 형태의 k8s cluster를 구축하는 것을 목적으로 합니다.

Container Runtime은 containerd, CNI는 Calico를 사용하며, GitHub Actions의 Self-Hosted Runner를 활용하여 내부망에서 배포를 진행합니다.

각 worker node에는 nginx 웹서버를 배포 예정

# 구성 순서도

1. 시스템 구성
   GitHub Actions Runner + Ansible (192.168.1.172) - Ansible을 실행하여 K8s 클러스터에 배포

   Master Node (192.168.1.171)

   Worker Nodes (192.168.1.177, 192.168.1.178)

2. GitHub Actions 설정

   .github/workflows/k8s_deploy.yaml에서 GitHub Actions Workflow 작성

   .github/workflows/inventory.ini에서 Ansible 관리 대상 Kubernetes 서버 정의

3. 각 시스템 간의 SSH 연결 설정
   
   setup_ssh.yml 파일로 각 노드와 ansible 서버와의 연결

4. k8s 구성을 위한 기본 설정 적용

   fix-k8s-cluster.yml 파일을 위한 기본 설정 자동 적용 

5. Kubernetes 클러스터 구성
 
   playbook/k8s_cluster.yml 파일을 이용한 cluster 구성

   worker node cluster에 join

   calico CNI 적용

   k8s_master.yml , k8s_worker.yml 파일을 이용한 각 node에 서비스 설치 및 기본 설정

6. Nginx 어플리케이션 배포

   Kubernetes 매니페스트nginx-deployment.yml를 적용하여 Nginx 배포

# 결과 

![image](https://github.com/user-attachments/assets/f6a1d89c-43e5-432d-9f9c-006b30e2c466)


# 추가 테스트

   k8s 클러스터 내의 node2를 off 시키면 나머지 node 1이 혼자서 웹 서버 서비스를 진행함

   ![image](https://github.com/user-attachments/assets/b3d255ac-f296-496a-b68d-ca0e8787b67f)

![image](https://github.com/user-attachments/assets/ca7ddc51-c2e5-40cb-80b1-a6a53f8be8eb)

   추가적으로 192.168.1.178의 ip로 현재 구성 되었기에 192.168.1.178 node가 죽었지만, cluster 구성으로 192.168.1.178 ip로 접근하더라도 서비스 유지 가능


### deploy.yml

```yml
name: Deploy Imfact Inferer to ECR
  
on:
  push:
    branches: ["cpu-version"]
  
env:
  AWS_REGION: ap-northeast-2
  ECR_REPOSITORY: imfact-inferer
  IMAGE_TAG: latest
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
  
jobs:
  deploy:
    runs-on: ubuntu-latest
  
    permissions:
      id-token: write
      contents: read
  
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
  
      - name: Configure AWS OIDC Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::${{ secrets.AWS_ACCOUNT_ID }}:role/GitHubActionsECRPushRole
          aws-region: ${{ env.AWS_REGION }}
  
      - name: Login to Amazon ECR
        uses: aws-actions/amazon-ecr-login@v2
  
      - name: Build Docker image
        run: |
          docker build -t $ECR_REPOSITORY:$IMAGE_TAG .
  
      - name: Tag Image
        run: |
          docker tag $ECR_REPOSITORY:$IMAGE_TAG \
          $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPOSITORY:$IMAGE_TAG
  
      - name: Push to ECR
        run: |
          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPOSITORY:$IMAGE_TAG
  
      - name: Done
        run: echo "imfact-inferer → ECR:latest push complete."
```

### IAM에서 ID 제공업체 추가`

![[Pasted image 20251117043609.png]]

### IAM 역할 생성

- 신뢰할 수 있는 엔터티 선택
![[Pasted image 20251117044157.png]]

- 권한 추가
![[Pasted image 20251117044245.png]]

- 이름 지정
![[Pasted image 20251117044317.png]]


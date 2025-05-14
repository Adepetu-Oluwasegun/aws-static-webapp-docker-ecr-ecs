# aws-static-webapp-docker-ecr-ecs
Deploy a Static Web App on AWS with Docker, Amazon ECR, and Amazon ECS

## **Project Overview**
This project deploys a **static web application** using:
- **Docker** (with Apache HTTP Server)  
- **Docker Hub** (for image storage)  
- **Amazon ECS** (Fargate) for container orchestration  
- **Application Load Balancer (ALB)** for traffic distribution  

## ** Docker Setup**
The application is containerized using Apache.  
 [View Dockerfile](./Dockerfile)

### **Build & Push to Docker Hub**
```bash
# Build the Docker image
docker build -t your-dockerhub-username/static-webapp:latest .

# Log in to Docker Hub
docker login

# Push the image to Docker Hub
docker push your-dockerhub-username/static-webapp:latest
```

## **☁️ AWS ECS Deployment (Console Setup)**
### **1️⃣ Configure ECS Task Definition**
1. **Task Definition Name**: `static-webapp-task`  
2. **Launch Type**: Fargate  
3. **Container Definition**:
   - **Image**: `your-dockerhub-username/static-webapp:latest`  
   - **Port Mappings**: `80 (HTTP) → 80 (Container)`  

### **2️⃣ Create ECS Cluster**
1. **Cluster Name**: `webapp-cluster`  
2. **VPC**: Use existing VPC with **public & private subnets**  
3. **Networking**:  
   - **Public Subnets** → ALB  
   - **Private Subnets** → ECS Tasks  

### **3️⃣ Set Up ALB (Application Load Balancer)**
1. **Listener**: HTTP (Port 80)  
2. **Target Group**: Routes traffic to ECS tasks  
3. **Security Group**: Allow HTTP (Port 80) from anywhere  

### **4️⃣ Deploy ECS Service**
1. **Service Name**: `webapp-service`  
2. **Desired Tasks**: `2` (for high availability)  
3. **Load Balancer**: Attach ALB  

## ** Accessing the static WebApp**
### **Via ALB DNS**
```
http://<ALB-DNS-NAME>.elb.amazonaws.com
```
*(Find ALB DNS in EC2 → Load Balancers)*  

### **Via Custom Domain (Optional)**
1. **Route 53**: Add an **ALIAS record** pointing to ALB  
2. **HTTPS (SSL)**: Use **ACM (AWS Certificate Manager)**  

## **🛠 Maintenance & Updates**
### **Updating the App**
1. **Rebuild Docker Image**  
   ```bash
   docker build -t your-dockerhub-username/static-webapp:latest .
   docker push your-dockerhub-username/static-webapp:latest
   ```
2. **Force New Deployment in ECS**  
   - Go to **ECS → Services → Update → Force new deployment**  

### **Scaling**
- **Manual**: Adjust **Desired Tasks** in ECS Service  
- **Auto Scaling**: Configure based on CPU/Memory usage  

## **⚠️ Troubleshooting**
| Issue | Solution |
| **Tasks not starting** | Check **CloudWatch Logs** (`/ecs/static-webapp-task`) |
| **ALB health checks failing** | Verify **security groups** allow HTTP traffic |
| **No internet in tasks** | Ensure **NAT Gateway** is properly configured |

## **🚀 Cleanup (Avoid AWS Charges)**
1. **Delete ECS Service**  
2. **Delete ALB & Target Group**  
3. **Remove ECR Images (if used)**  
4. **Delete VPC (if no longer needed)**  

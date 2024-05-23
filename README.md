# Step-by-step-guide-to-create-GKE-cluster

This guide is used to create GKE cluster & deploy sample application nginx.
a Google Kubernetes Engine (GKE) is gcp managed kubernetes service that is used to create, manage k8s clusters.
Creating a Google Kubernetes Engine (GKE) cluster involves several steps. 
Below is a detailed step-by-step guide to create a GKE cluster:

Step 1: Set Up Google Cloud SDK

1. **Install Google Cloud SDK:**

   Follow the instructions on the [Google Cloud SDK installation page](https://cloud.google.com/sdk/docs/install).

   For example, on macOS:

   ```sh
   curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-367.0.0-darwin-x86_64.tar.gz
   tar -xvf google-cloud-sdk-367.0.0-darwin-x86_64.tar.gz
   ./google-cloud-sdk/install.sh
   ```

   On Linux:

   ```sh
   curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-367.0.0-linux-x86_64.tar.gz
   tar -xvf google-cloud-sdk-367.0.0-linux-x86_64.tar.gz
   ./google-cloud-sdk/install.sh
   ```

2. **Initialize Google Cloud SDK:**

   ```sh
   gcloud init
   ```

   Follow the prompts to log in and set up your project and default settings.

### Step 2: Set Up Your GKE Environment

1. **Set Your Project ID:**

   ```sh
   gcloud config set project <your-project-id>
   ```

2. **Enable the Kubernetes Engine API:**

   ```sh
   gcloud services enable container.googleapis.com
   ```

Step 3: Create a GKE Cluster

1. **Create a GKE Cluster:**

   ```sh
   gcloud container clusters create my-cluster \
     --zone us-central1-a \
     --num-nodes 3 \
     --machine-type e2-medium \
     --enable-ip-alias
   ```

   - `--zone`: Specifies the zone where the cluster will be created.
   - `--num-nodes`: Number of nodes in the cluster.
   - `--machine-type`: Type of machine to use for nodes.
   - `--enable-ip-alias`: Enables VPC-native (alias IP) networking.

2. **Wait for the Cluster to be Created:**

   This process can take several minutes.

Step 4: Configure kubectl

1. **Install kubectl:**

   Follow the instructions on the [kubectl installation page](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

2. **Get Cluster Credentials:**

   ```sh
   gcloud container clusters get-credentials my-cluster --zone us-central1-a
   ```

   This command configures `kubectl` to use the credentials for your new cluster.

3. **Verify the Configuration:**

   ```sh
   kubectl get nodes
   ```

   You should see a list of nodes in your cluster.

Step 5: Deploy a Sample Application

1. **Create a Deployment:**

   Create a YAML file for the deployment (e.g., `nginx-deployment.yaml`):

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:latest
           ports:
           - containerPort: 80
   ```

   Apply the deployment:

   ```sh
   kubectl apply -f nginx-deployment.yaml
   ```

2. **Create a Service:**

   Create a YAML file for the service (e.g., `nginx-service.yaml`):

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-service
   spec:
     selector:
       app: nginx
     ports:
     - protocol: TCP
       port: 80
       targetPort: 80
     type: LoadBalancer
   ```

   Apply the service:

   ```sh
   kubectl apply -f nginx-service.yaml
   ```

3. **Verify the Deployment and Service:**

   ```sh
   kubectl get deployments
   kubectl get services
   ```

   The `nginx-service` should have an external IP assigned by the LoadBalancer.

Step 6: Access the Application

1. **Get the External IP:**

   ```sh
   kubectl get services nginx-service
   ```

   Note the `EXTERNAL-IP` of the `nginx-service`.

2. **Access the Application:**

   Open a web browser and navigate to the external IP address. You should see the Nginx welcome page.

Step 7: Clean Up (Optional)

If you no longer need the cluster, you can delete it to avoid incurring charges.

1. **Delete the GKE Cluster:**

   ```sh
   gcloud container clusters delete my-cluster --zone us-central1-a
   ```

### Conclusion

This step-by-step guide covers the creation of a Google Kubernetes Engine (GKE) cluster using the Google Cloud SDK, configuring `kubectl`, and deploying a sample application. For more advanced configurations and management, refer to the [official GKE documentation](https://cloud.google.com/kubernetes-engine/docs).

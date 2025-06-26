kubectl Cheatsheet
Quick reference for essential kubectl commands used in CKA exam preparation.
# API Resources & Cluster Info
# Get all namespaced API resources
kubectl api-resources --namespaced=true -o name

# View current kubeconfig
kubectl config view

# View current context for specific kubeconfig file
kubectl config --kubeconfig=<file> current-context

# Switch context in specific kubeconfig file
kubectl config --kubeconfig <file> use-context newcontext

# List all contexts
kubectl config get-contexts

# Pod Operations
# Get pods with labels
kubectl get pods --show-labels

# Filter pods by label selector
kubectl get pods --selector label=labelname

# Get pods sorted by creation time
kubectl get pods --sort-by=.metadata.creationTimestamp

# Get pod IP address using jsonpath
kubectl get pod web-app -o jsonpath='{.status.podIP}'

# Check pod resource usage
kubectl top pods --containers=true

# Node Information
# Get node names using jsonpath
kubectl get nodes -o jsonpath='{.items[*].metadata.name}'

# Get node OS information using jsonpath
kubectl get nodes -o jsonpath='{.items[*].status.nodeInfo.osImage}'

# Using custom-column
kubectl get nodes -o=custom-columns=NODE_NAME:.metadata.name
kubectl get pv -o=custom-columns=VOLUME_NAME:.metadata.name,CAPACITY:.spec.capacity.storage,NAMESPACE:.metadata.namespace --sort-by=.spec.capacity.storage

# Execution & Debugging
# Execute commands in pods
kubectl exec podname -- whoami
kubectl exec podname -- cat filename

# Multi-container pod access
kubectl exec -it -n cka-multi-containers cka-sidecar-pod -c sidecar-container -- curl http://localhost:80/app.log

# Network connectivity testing
kubectl exec sourcepod -- curl curlme-cka01-svcn

# DNS lookup testing
kubectl run test-nslookup --rm -it --image=nginx --restart=Never -- nslookup servicename

# External connectivity testing
kubectl run -n kube-public --rm -i test-curl-pod --image=curlimages/curl --restart=Never -- curl -m 2 external-webserver-cka03-svcn

# Troubleshooting
# Get events for specific pod
kubectl get events --field-selector=involvedObject.name=podname

# Follow pod logs with namespace
kubectl logs -f podname -n namespace

# Get deployment image information
kubectl get deploy -n namespace deployment -o json | jq -r '.spec.template.spec.containers[].image'

# Restart a deployment
kubectl rollout restart deployment-name

# Certificate Management
# View certificate details
openssl x509 -in <file> -text

# Base64 encode CSR (Certificate Signing Request)
cat xxx.csr | base64 -w 0

# Check certificates
kubectl get csr

# Container Runtime (crictl)
# List all containers
crictl ps -a

# View container logs
crictl logs <containerID>

# Inspect container details
crictl inspect <containerID>

# List running containers only
crictl ps

# Helm Commands
# Search for charts in Helm Hub
helm search hub reponame --list-repo-url | head -n15

# Search in specific repository
helm search repo bitnami/nginx -l | head -n30

# Environment Variables in YAML
# Using Secret as Environment Variables
envFrom:
- secretRef:
    name: db-secret-wl05

# Using ConfigMap as Environment Variables
envFrom:
- configMapRef: 
    name: webapp-wl10-config-map

# Set nodeaffinity
nodeAffinity:
  required:
    nodeSelectorTerms:
      - matchExpressions:
          - key: kubernetes.io/hostname
            operator: In
            values:
              - cluster1-node01

# Command and args example
command: ["/bin/sh"]
args: ["-c", "while true; do echo hello; sleep 10;done"]

      command:
        - "sh"
        - "-c"
        - "while true; do date >> /var/log/shared/date.log; sleep 1; done"
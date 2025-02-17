# Monitor Istio running on Amazon EKS


In addition to EKS infrastructure monitoring, the current example provides
curated Grafana dashboards, Prometheus alerting and recording rules with multiple
configuration options for ISTIO based workloads on EKS.

## Setup

### 1. Add ISTIO metrics, dashboards and alerts

From the [EKS cluster monitoring example's](https://aws-observability.github.io/terraform-aws-observability-accelerator/eks/) configuration,
simply enable the ISTIO pattern's flag.

```hcl

module "eks_monitoring" {
   ...
   enable_istio = true
}
```

You can further customize the NGINX pattern by providing `nginx_config` [options](https://github.com/aws-observability/terraform-aws-observability-accelerator/blob/main/modules/eks-monitoring/README.md#input_nginx_config).

### 2. Grafana API key

Make sure to refresh your temporary Grafana API key

```bash
export TF_VAR_managed_grafana_workspace_id=g-xxx
export TF_VAR_grafana_api_key=`aws grafana create-workspace-api-key --key-name "observability-accelerator-$(date +%s)" --key-role ADMIN --seconds-to-live 1200 --workspace-id $TF_VAR_managed_grafana_workspace_id --query key --output text`
```

## Deploy

Simply run this command to deploy.

```bash
terraform apply
```

!!! note
    To see the complete ISTIO example, open the [example on the repository](https://github.com/aws-observability/terraform-aws-observability-accelerator/tree/main/examples/eks-istio)

## Visualization

1. Grafana dashboards

Go to the Dashboards panel of your Grafana workspace. You will see a list of dashboards under the `Observability Accelerator Dashboards`

<img width="1208" alt="image" src="https://user-images.githubusercontent.com/47993564/236841811-fdd5a07c-6e5e-4654-a735-80f92f5bee56.jpeg">

Open one of the ISTIO dasbhoards and you will be able to view its visualization

<img width="1850" alt="image" src="https://user-images.githubusercontent.com/47993564/236842708-72225322-4f97-44cc-aac0-40a3356e50c6.jpeg">

2. Amazon Managed Service for Prometheus rules and alerts

Open the Amazon Managed Service for Prometheus console and view the details of your workspace. Under the `Rules management` tab, you will find new rules deployed.

<img width="1054" alt="image" src="https://user-images.githubusercontent.com/47993564/236844084-80c754e3-4fe1-45bb-8361-181432675469.jpeg">

!!! note
    To setup your alert receiver, with Amazon SNS, follow [this documentation](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-alertmanager-receiver.html)

## Deploy an example application to visualize metrics

In this section we will deploy sample application and extract metrics using AWS OpenTelemetry collector

### 1. Deploy the Bookinfo Application 

```sh
#https://istio.io/latest/docs/examples/bookinfo/
```

### 2. Start some sample ISTIO traffic by entering the following command.

```sh
#For the Bookinfo sample, visit http://$GATEWAY_URL/productpage in your web browser or issue the following command:

#To see trace data, you must send requests to your service. The number of requests depends on Istio’s sampling rate and can be configured using the Telemetry API. With the default sampling rate of 1%, you need to send at least 100 requests before the first trace is visible. To send a 100 requests to the productpage service, use the following command:

$ for i in $(seq 1 100); do curl -s -o /dev/null "http://$GATEWAY_URL/productpage"; done
```

### 7. Visualize the Application's dashboard

Log back into your Managed Grafana Workspace and navigate to the dashboard side panel, click on `Observability Accelerator Dashboards` Folder and open the `ISTIO Service` Dashboard.  This gives details about metrics for the service and then client workloads (workloads that are calling this service) and service workloads (workloads that are providing this service) for that service.

Explore the Istio Performance and Control Plane dasbhoards as well.  

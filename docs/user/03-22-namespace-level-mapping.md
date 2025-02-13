# Namespace-Level Mapping

You can map a Kubernetes namespace to an SAP Service Manager instance in a given subaccount. The Service Manager instance is then used to provision all service instances in this namespace.

## Prerequisites

* A subaccount in the SAP BTP cockpit.
* kubectl configured for communicating with your Kyma instance.

## Context

To have service instances from one subaccount associated with one namespace, you must use a Secret dedicated to this namespace to create these service instances.

## Procedure

### Create a Namespace-Based Secret

1. In the SAP BTP cockpit, create an SAP Service Manager service instance with the `service-operator-access` plan. See [Creating Instances in Other Environments](https://help.sap.com/docs/service-manager/sap-service-manager/creating-instances-in-other-environments?locale=en-US&version=Cloud).
2. Create a service binding to the SAP Service Manager service instance you have created. See [Creating Service Bindings in Other Environments](https://help.sap.com/docs/service-manager/sap-service-manager/creating-service-bindings-in-other-environments?locale=en-US&version=Cloud).
3. Get the access credentials of the SAP Service Manager instance with the `service-operator-access` plan from its service binding. Copy them from the SAP BTP cockpit as a JSON.
4. Create the `creds.json` file in your working directory and save the credentials there.
5. In the same working directory, generate the Secret by calling the `create-secret-file.sh` script with the **operator** option as the first parameter and **namespace-name-sap-btp-service-operator** Secret as the second parameter.

    ```sh
    curl https://raw.githubusercontent.com/kyma-project/btp-manager/main/hack/create-secret-file.sh | bash -s operator {NAMESPACE_NAME}-sap-btp-service-operator
    ```

    The expected result is the file `btp-access-credentials-secret.yaml` created in your working directory:

    ```yaml
    apiVersion: v1
    kind: Secret
    type: Opaque
    metadata:
      name: {NAMESPACE_NAME}-sap-btp-service-operator
      namespace: kyma-system
    data:
      clientid: {CLIENT_ID}
      clientsecret: {CLIENT_SECRET}
      sm_url: {SM_URL}
      tokenurl: {AUTH_URL}
      tokenurlsuffix: "/oauth/token"
    ```
6. To create the Secret, run:

    ```
    kubectl create -f ./btp-access-credentials-secret.yaml
    ```

   You can see the status `Created`.


### Create a Service Instance with a Namespace-Based Secret

1. To create a service instance with a namespace-based Secret, follow the instructions on [creating service instances](03-30-management-of-service-instances-and-bindings.md#create-a-service-instance).

2. To verify if you've correctly added the access credentials of the SAP Service Manager instance in your service instance, go to the CR `status` section, and make sure the subaccount ID to which the instance belongs is provided in the **subaccountID** field. The field must not be empty.

## Related Information

[Working with Multiple Subaccounts](03-20-multitenancy.md)<br>
[Instance-Level](03-21-instance-level-mapping.md)

# Pulp ArgoCD Application

Pulp encapsulation for deploying it as an ArgoCD application

## Some notes

Deleting the app does not remove neither the database persistent volume for `postgresql` nor the secrets created
for the application. those have to be manually removed:

```bash
kubectl -n pulp delete pvc galaxy-pulp-postgres-galaxy-pulp-database-0
kubectl -n pulp delete secret galaxy-pulp-container-auth galaxy-pulp-db-fields-encryption
```

Useful URLs:

- LoadBalancer endpoint: `kubectl -n pulp get svc galaxy-pulp-web-svc -o json | jq -r '.status.loadBalancer.ingress[0].hostname'`
- Galaxy UI: `http://<galaxy-pulp-web-svc>`. User: `admin`, password: `kubectl -n pulp get secret galaxy-pulp-admin-password -o json | jq -r .data.password | base64 -d`
- Tokens can be obtained under "Collections/Api Token"
- Namespaces can be created under "Collections/Namespaces". There, roles and users can be attached.
- Example of uploading a collection:

    ```text
    $ ansible-galaxy collection publish galaxy_test-test_collection-1.0.0.tar.gz --api-key e3b30bb0ec2aa250992f4c415681c11f38cd0e7e --server http://k8s-pulp-galaxypu-94792254b0-3d37e56438df7779.elb.eu-central-1.amazonaws.com/api/galaxy -vvvv -c
        ansible-galaxy [core 2.11.12] 
        config file = None
        configured module search path = ['/Users/alexis/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
        ansible python module location = /opt/homebrew/lib/python3.11/site-packages/ansible
        ansible collection location = /Users/alexis/.ansible/collections:/usr/share/ansible/collections
        executable location = /opt/homebrew/bin/ansible-galaxy
        python version = 3.11.9 (main, Apr  2 2024, 08:25:04) [Clang 15.0.0 (clang-1500.3.9.4)]
        jinja version = 3.1.2
        libyaml = True
        No config file found; using defaults
        Initial connection to galaxy_server: http://k8s-pulp-galaxypu-94792254b0-3d37e56438df7779.elb.eu-central-1.amazonaws.com/api/galaxy
        Opened /Users/alexis/.ansible/galaxy_token
        Calling Galaxy at http://k8s-pulp-galaxypu-94792254b0-3d37e56438df7779.elb.eu-central-1.amazonaws.com/api/galaxy
        Found API version 'v3, pulp-v3' with Galaxy server cmd_arg (http://k8s-pulp-galaxypu-94792254b0-3d37e56438df7779.elb.eu-central-1.amazonaws.com/api/galaxy)
    ```

- Once uploaded, collections have to be approved unded "Collections/Approval"

http://k8s-pulp-galaxypu-7e26074386-d8a4c7a9234303d7.elb.eu-central-1.amazonaws.com/api/galaxy/v3/auth/token/

POST


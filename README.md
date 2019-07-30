# gce-ssh

Tutorial of how to connect to GCE Linux instances

## Getting Started

1. Ensure the Following APIs are enabled (enable with gcloud services enable [service]):

    - compute.googleapis.com

1. [Ensure the default Google APIs service account (used by deployment manager) has permission to create roles](https://cloud.google.com/deployment-manager/docs/configuration/set-access-control-resources):

    - In macOS/Linux:

        ```sh
        project_id=$(gcloud config list --format 'value(core.project)')
        project_number=$(gcloud projects list --filter "id:ca-labs" --format 'value(projectNumber)')
        gcloud projects add-iam-policy-binding $project_id \
          --member serviceAccount:$project_number@cloudservices.gserviceaccount.com \
          --role roles/iam.roleAdmin
        ```

    - In Windows (PowerShell):

        ```ps1
        $project_id = gcloud config list --format 'value(core.project)'
        $project_number = gcloud projects list --filter "id:ca-labs" --format 'value(projectNumber)'
        gcloud projects add-iam-policy-binding $project_id `
          --member serviceAccount:$project_number@cloudservices.gserviceaccount.com `
          --role roles/iam.roleAdmin
        ```

1. Deploy the deployment manager config in the `infrastructure` directory:

    ```sh
    gcloud deployment-manager deployments create lab --config infrastructure/deployment.yaml
    ```

1. Bind the Lab role to the student user or group:

    - In macOS/Linux:

        ```sh
        member="[GROUP_OR_USER]"
        project_id=$(gcloud config list --format 'value(core.project)')
        role=$(gcloud iam roles list --project $project_id \
                                     --filter "name:projects/$project_id/roles/studentrole*" \
                                     --format "value(name)")
        gcloud projects add-iam-policy-binding $project_id \
        --member $member  \
        --role $role
        ```

    - In Windows (PowerShell):

        ```ps1
        $member = "[GROUP_OR_USER]"
        $project_id = gcloud config list --format 'value(core.project)'
        $role = gcloud iam roles list --project $project_id `
                                      --filter "name:projects/$project_id/roles/studentrole*" `
                                      --format "value(name)"
        gcloud projects add-iam-policy-binding $project_id `
        --member $member  `
        --role $role
        ```

    An example of `[GROUP_OR_USER]` is `user:student@gmail.com`.

## Following Along

1. Click __SSH__ from the [VM instances GCE tab](https://console.cloud.google.com/compute/instances) to connect to the instance using SSH from the Browser.

1. Paste a public key into the instance or project SSH key metadata and connect using a standard SSH client.

## Tearing Down

When finished, remove the GCP resources with:

- In macOS/Linux:

    ```sh
    gcloud projects remove-iam-policy-binding $project_id \
        --member $member  \
        --role $role
    gcloud deployment-manager deployments delete -q lab
    ```

- In Windows (PowerShell):

    ```ps1
    gcloud projects remove-iam-policy-binding $project_id `
        --member $member  `
        --role $role
    gcloud deployment-manager deployments delete -q lab
    ```

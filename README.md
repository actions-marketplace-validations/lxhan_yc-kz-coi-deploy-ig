## GitHub Action to deploy your container into Yandex Cloud instance group created from Container Optimized Image.

The action creates a Instance Group with the provided name in the provided
folder if there is no one. Then it deploys a container using the provided image
name and tag.

**Table of Contents**

<!-- toc -->

- [Usage](#usage)
- [Permissions](#permissions)
- [License Summary](#license-summary)

<!-- tocstop -->

## Usage

```yaml
    - name: Login to Yandex Cloud Container Registry
      id: login-cr
      uses: yc-actions/yc-cr-login@v2
      with:
        yc-sa-json-credentials: ${{ secrets.YC_SA_JSON_CREDENTIALS }}

    - name: Build, tag, and push image to Yandex Cloud Container Registry
      env:
        CR_REGISTRY: crp00000000000000000
        CR_REPOSITORY: my-cr-repo
        CR_ENDPOINT: cr.yandexcloud.kz
        IMAGE_TAG: ${{ github.sha }}
      run: |
        docker build -t $CR_ENDPOINT/$CR_REGISTRY/$CR_REPOSITORY:$IMAGE_TAG .
        docker push $CR_ENDPOINT/$CR_REGISTRY/$CR_REPOSITORY:$IMAGE_TAG

    - name: Deploy COI Instance Group
      id: deploy-coi
      uses: lxhan/yc-kz-coi-deploy-ig@v1
      env:
        CR_REGISTRY: crp00000000000000000
        CR_REPOSITORY: my-cr-repo
        IMAGE_TAG: ${{ github.sha }}
      with:
        yc-sa-json-credentials: ${{ secrets.YC_SA_JSON_CREDENTIALS }}
        folder-id: bbajn5q2d74c********
        api-endpoint: api.yandexcloud.kz
        ig-spec-path: './spec.yaml'
        user-data-path: './user-data.yaml'
        docker-compose-path: './docker-compose.yaml'
```

Data from files `./spec.yaml`, `user-data.yaml`, and `docker-compose.yaml` will
be passed to the Mustache template renderer, so there could be used environment
variables substitution via `{{ env.VARIABLE }}` syntax.

See [action.yml](action.yml) for the full documentation for this action's inputs
and outputs.

## Permissions

To perform this action, it is required that the service account on behalf of
which we are acting has granted the `compute.admin`, `vpc.admin`, and
`iam.serviceAccounts.user` roles or greater.

## License Summary

This code is made available under the MIT license.

# Setup a pipeline with Concourse CI

- [Concourse CI](https://concourse-ci.org/)
- [Concourse Tutorial](https://concoursetutorial.com/)

Run the Concourse CI

```bash
docker-compose up
```

Login

```bash
fly --target tutorial login --concourse-url http://localhost:8080 -u admin -p admin
fly --target tutorial sync
```

Run task

```bash
fly -t tutorial execute -c tasks/hello-world.yml
```

Task without input

```bash
fly --target tutorial execute --config tasks/no-inputs.yml
```

Task with input

```bash
fly --target tutorial execute --config tasks/inputs-required.yml --input some-input=.
```

If the input name and the actual folder name are the same, the actual folder content will be copied inside the container.

## Pipeline

```bash
fly --target tutorial set-pipeline --config tasks/basic-pipeline.yml --pipeline hello-world
fly --target tutorial unpause-pipeline --pipeline hello-world
fly --target tutorial unpause-job --job hello-world/job-hello-world
```

### Git resource

```yaml
resources:
  - name: resource-tutorial
    type: git
    source:
      uri: https://github.com/starkandwayne/concourse-tutorial.git
      branch: develop

jobs:
  - name: job-hello-world
    public: true
    plan:
      - get: resource-tutorial
      - task: hello-world
        file: resource-tutorial/tutorials/basic/task-hello-world/task_hello_world.yml
```

### Watch

```bash
fly -t tutorial builds
fly -t tutorial watch -j hello-world/job-hello-world
```

Trigger a job and watch:

```
fly -t tutorial trigger-job -j hello-world/job-hello-world -w
```

# DevOpsNotes

| DevSecOps Princip  | Hvordan vores GitHub Actions workflow passer ind|
| ------------- | ------------- |
| **Security as Code**  | Workflowen tjekker automatisk om secrets og SSH-nøgler er til stede og i korrekt format – uden manuel indgriben.  |
| **Shared responsibility**  | Sikkerhedsvalidering er en del af CI/CD-pipelinen, ikke kun sikkerhedsteamets ansvar. Alle der arbejder med deploys er involveret.  |
| **Shift Left Security**  | Workflowen kører ved PR eller ved workflow dispatch, det vil sige før der sker en deployment. Det er tidligt i processen.  |
| **Automation First**  | Hele processen foregår automatisk ved PRs eller kald fra andre workflows.  |
| **Security Gate in CI/CD**  | Secrets valideres før næste trin må køres. Hvis noget mangler, afbrydes deploymenten med fejl.  |
| **Continuous Validation** | Secrets og SSH-nøgler bliver valideret ved hver PR og workflow-kald, så sikkerhed sikres løbende og ikke kun en gang. |





**Security as Code**

*Sikkerhed er skrevet som kode:*
```
- name: Ensure PROD_ENV_FILE is provided for production
  if: env.PROD_ENV_FILE_PRESENT == 'false'
  run: |
    echo "::error::Secret PROD_ENV_FILE is required..."
    exit 1
```


**Shared responsibility**

*Alle workflows aktiveres af udviklere via PRs:*

```
on:
  pull_request:
    branches: [development, main]
```

**Shift left security + Automation First**

*Sikkerhedsvalidering sker tidligt – før merge, før deploy eller ved manuel test:*

*Workflowet kører ved hver ændring, hver PR, hver deploy. Det sikrer konstant overvågning af sikkerheds-konfigurationer.*

```
on:
  pull_request:
  workflow_dispatch:
  workflow_call:


```

 **Continuous Validation**

 *Secrets og SSH-nøgler valideres hver gang der oprettes en PR, eller når workflowet kaldes af et andet – ikke kun ved release.*

 ```

jobs:
  validate-dev-secrets:
    if: inputs.environment == 'development'
    runs-on: ubuntu-latest


```

**Security Gate in CI/CD**

*Secrets valideres før næste trin må køres. Hvis noget mangler, afbrydes deploymenten med en fejl.*

```
- name: Test DEV SSH Key is provided for development
  if: inputs.environment == 'development' && env.DEV_SSH_KEY_PRESENT == 'false'
  run: |
    echo "::error::Secret DEV_SSH_PRIVATE_KEY is required for environment 'development' but was not provided."
    exit 1
```




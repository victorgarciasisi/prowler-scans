### 1: Ejecutar pipeline base (https://github.com/victorgarciasisi/demo-gh-action-cd-minimo)

Usa la pipeline ya definida en:
- https://github.com/victorgarciasisi/demo-gh-action-cd-minimo

Qué validar en directo:
- jobs verdes/rojos y su gating
- artefactos generados
- resultado final de merge gate

### 2: Comandos creacion scans dir


```bash
cd docker
mkdir prowler
chmod 777 prowler
cd prowler
```

```bash
# Ver identidad AWS activa
aws sts get-caller-identity

# Ver regiones activas de referencia
aws ec2 describe-regions --query 'Regions[].RegionName' --output text
```

### 3: Prowler AWS provider (https://docs.prowler.com/getting-started/basic-usage/prowler-cli)

Permisos prowler: https://docs.prowler.com/user-guide/providers/aws/authentication

```
arn:aws:iam::aws:policy/SecurityAudit
arn:aws:iam::aws:policy/job-function/ViewOnlyAccess
```

```bash
# Scan AWS con perfil por defecto (credenciales montadas desde ~/.aws)
docker run --rm -it \
  -v $HOME/.aws:/home/prowler/.aws:ro \
  -v ./:/home/prowler/output/ \
  -e AWS_PROFILE=default \
  toniblyx/prowler:5.21.0 aws
```

```bash
# Lista de compliance (credenciales montadas desde ~/.aws)
docker run --rm -it \
  -v $HOME/.aws:/home/prowler/.aws:ro \
  -e AWS_PROFILE=default \
  toniblyx/prowler:5.21.0 aws --list-compliance
```

```bash
#  Ejecucion de compliance (credenciales montadas desde ~/.aws)
docker run --rm -it \
  -v $HOME/.aws:/home/prowler/.aws:ro \
  -v ./:/home/prowler/output/ \
  -e AWS_PROFILE=default \
  toniblyx/prowler:5.21.0 aws --compliance iso27001_2022_aws
```

### 4: Prowler IaC sobre Terragrunt (https://docs.prowler.com/user-guide/providers/iac/getting-started-iac)

### copiamos y borramos

```bash
git clone https://github.com/victorgarciasisi/terragrunt-modules
```

```bash
# Ejemplo: escanear IaC local (Terragrunt/Terraform)
docker run --rm -it \
   -v ./:/workdir \
   -v ./:/home/prowler/output/ \
   toniblyx/prowler:5.21.0 iac --scan-path /workdir/terragrunt-modules/aws-eks --output-formats csv html
```

### 4: Prowler GitHub sobre repo concreto (https://docs.prowler.com/user-guide/providers/github/getting-started-github#prowler-cli) 
```bash
# Token recomendado en variable de entorno
export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_xxx"

# Scan de un repositorio concreto
docker run --rm -it \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=$GITHUB_PERSONAL_ACCESS_TOKEN \
  -v ./:/home/prowler/output/ \
  toniblyx/prowler:5.21.0 github --repository victorgarciasisi/demo-gh-action-cd-minimo


```

### 5: Quick Inventory AWS (https://docs.prowler.com/user-guide/cli/tutorials/quick-inventory)

```bash
# Inventario rápido de recursos AWS
docker run --rm -it \
  -v $HOME/.aws:/home/prowler/.aws:ro \
  -v ./:/home/prowler/output/ \
  -e AWS_PROFILE=default \
  toniblyx/prowler:5.21.0 aws -i
```


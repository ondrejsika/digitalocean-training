[Ondrej Sika (sika.io)](https://sika.io) | <ondrej@sika.io>

# Digital Ocean Training

    Ondrej Sika <ondrej@ondrejsika.com>
    https://github.com/ondrejsika/digital-ocean-training

## Course

## About Me - Ondrej Sika

**Freelance DevOps Engineer, Consultant & Lecturer**

- Complete DevOps Pipeline
- Open Source / Linux Stack
- Cloud & On-Premise
- Technologies: Git, Gitlab, Gitlab CI, Docker, Kubernetes, Terraform, Prometheus, ELK / EFK, Rancher, Proxmox, DigitalOcean, AWS

## Star, Create Issues, Fork, and Contribute

Feel free to star this repository or fork it.

If you found bug, create issue or pull request.

Also feel free to propose improvements by creating issues.

## Live Chat

For sharing links & "secrets".

<https://tlk.io/sika-do>

## Agenda

- Cloud & Digital Ocean Intro
- DO's Structure
- DO's Management
- DO's Resources

## Main Resources

- Control Panel: https://cloud.digitalocean.com/
- Docs: https://www.digitalocean.com/docs/
- Terraform Provider Docs: https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs

## Cloud & Digital Ocean

### What is Cloud

Cloud computing is the on-demand availability of computer system resources, especially data storage and computing power, without direct active management by the user. -- [Wikipedia](https://en.wikipedia.org/wiki/Cloud_computing)

### What is Digit Ocean

DigitalOcean is a developer's cloud. We make it simple to launch in the cloud and scale up as you grow – with an intuitive control panel, predictable pricing, team accounts, and more. -- DigitalOcean

### Digital Ocean vs Others

- Digital Ocean vs AWS, GCP, Azure
- Digital Ocean vs Linode, Hetzner Cloud
- Digital Ocean vs hosting

## Digital Ocean Structure

### Users

- One account / login per user (same as GitHub)
- Easy switch between Teams

### Teams

- Units for companies, teams, ...
- Own billing settings
- Own access tokens
- Roles
  - Owner
  - Member
  - Biller

### Projects

- Only for organization inside of Team
- No restrictions & permissions

## Digital Ocean Management

### Terraform

- Terraform Provider - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs

### CLI

- Setup CLI - https://www.digitalocean.com/docs/apis-clis/doctl/how-to/install/
- Reference - https://www.digitalocean.com/docs/apis-clis/doctl/reference/

Install `doctl`

```
brew install doctl
```

Setup CLI

```
doctl auth init
```

Validate

```
doctl account get
```

### Web UI

Go to: <https://cloud.digitalocean.com/>

## Digital Ocean Resources

### SSH Keys

Web UI: https://cloud.digitalocean.com/account/security

#### Terraform

[Docs](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/ssh_key)

```terraform
resource "digitalocean_ssh_key" "default" {
  name       = "default"
  public_key = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHtI4BsjxWHmRB3EzyQDSX5idgyjD67XL4WmIjz+pcG6"
}
```

or from file

```terraform
resource "digitalocean_ssh_key" "default" {
  name       = "default"
  public_key = file("~/.ssh/id_rsa.pub")
}
```

### Droplets

- Product: https://www.digitalocean.com/products/droplets/
- Docs: https://www.digitalocean.com/docs/droplets/

#### Web UI

Go to: https://cloud.digitalocean.com/droplets

#### Terraform

[Docs](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/droplet)

```tf
resource "digitalocean_droplet" "demo" {
  image  = "ubuntu-18-04-x64"
  name   = "demo-tf"
  region = "fra1"
  size   = "s-1vcpu-1gb"
}
```

or with SSH key

```tf
resource "digitalocean_droplet" "demo-ssh-key" {
  image    = "ubuntu-18-04-x64"
  name     = "demo-tf-ssh-key"
  region   = "fra1"
  size     = "s-1vcpu-1gb"
  ssh_keys = [
    digitalocean_ssh_key.default.fingerprint
  ]
}
```

#### CLI

Create

```
doctl compute droplet create demo-cli --region fra1 --size s-1vcpu-1gb --image ubuntu-18-04-x64
```

List

```
doctl compute droplet list
```

Delete

```
doctl compute droplet delete <id>
```

### Kubernetes

- Product: https://www.digitalocean.com/products/kubernetes/
- Docs: https://www.digitalocean.com/docs/kubernetes/

Get avaiable Kubernetes versions:

```
doctl kubernetes options versions
```

Get available node sizes:

```
doctl kubernetes options sizes
```

Get available regions:

```
doctl kubernetes options regions
```

#### Web UI

Go to: https://cloud.digitalocean.com/kubernetes/clusters

#### Terraform

Docs:

- Cluster - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/kubernetes_cluster
- Node Pool - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/kubernetes_node_pool

```tf
resource "digitalocean_kubernetes_cluster" "demo-tf" {
  name   = "demo-tf"
  region = "fra1"
  version = "1.19.3-do.2"
  node_pool {
    name       = "demo-tf"
    size       = "s-2vcpu-2gb"
    node_count = 1
  }
}
```

Additional Node pool

```
resource "digitalocean_kubernetes_node_pool" "demo-tf-2" {
  cluster_id = digitalocean_kubernetes_cluster.demo-tf.id
  name       = "demo-tf-2"
  size       = "s-2vcpu-2gb"
  node_count = 2
}
```

#### CLI

- doctl kubernetes - https://www.digitalocean.com/docs/apis-clis/doctl/reference/kubernetes/
- doctl kubernetes cluster - https://www.digitalocean.com/docs/apis-clis/doctl/reference/kubernetes/cluster/

### Loadbalancers

- Docs: https://www.digitalocean.com/docs/networking/load-balancers/

#### Web UI

Go to: https://cloud.digitalocean.com/networking/load_balancers

#### Terraform

[Docs](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/loadbalancer)

```tf
resource "digitalocean_loadbalancer" "demo-tf" {
  name   = "demo"
  region = "fra1"
  droplet_tag = "k8s:${digitalocean_kubernetes_cluster.demo-tf.id}"

  healthcheck {
    port     = 30001
    protocol = "tcp"
  }

  forwarding_rule {
    entry_port      = 80
    target_port     = 30001
    entry_protocol  = "tcp"
    target_protocol = "tcp"
  }
}
```

#### CLI

[Docs](https://www.digitalocean.com/docs/apis-clis/doctl/reference/compute/load-balancer/)

List load balancers:

```
doctl compute load-balancer list
```

### Databases

### Spaces (S3)

### Monitoring

### VPC

### Firewalls

### DNS

### App Platform

## Digital Ocean Czech Community

## Thank you! & Questions?

That's it. Do you have any questions? **Let's go for a beer!**

### Ondrej Sika

- email: <ondrej@sika.io>
- web: <https://sika.io>
- twitter: [@ondrejsika](https://twitter.com/ondrejsika)
- linkedin: [/in/ondrejsika/](https://linkedin.com/in/ondrejsika/)
- Newsletter, Slack, Facebook & Linkedin Groups: <https://join.sika.io>

_Do you like the course? Write me recommendation on Twitter (with handle `@ondrejsika`) and LinkedIn (add me [/in/ondrejsika](https://www.linkedin.com/in/ondrejsika/) and I'll send you request for recommendation). **Thanks**._

Wanna to go for a beer or do some work together? Just [book me](https://book-me.sika.io) :)

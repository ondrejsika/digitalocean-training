[Ondrej Sika (sika.io)](https://sika.io) | <ondrej@sika.io>

# DigitalOcean Training

    Ondrej Sika <ondrej@ondrejsika.com>
    https://github.com/ondrejsika/digitalocean-training

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

- Cloud & DigitalOcean Intro
- DO's Structure
- DO's Management
- DO's Resources

## Main Resources

- Control Panel: https://cloud.digitalocean.com/
- Docs: https://www.digitalocean.com/docs/
- Terraform Provider Docs: https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs

## Cloud & DigitalOcean

### What is Cloud

Cloud computing is the on-demand availability of computer system resources, especially data storage and computing power, without direct active management by the user. -- [Wikipedia](https://en.wikipedia.org/wiki/Cloud_computing)

### What is Digit Ocean

DigitalOcean is a developer's cloud. We make it simple to launch in the cloud and scale up as you grow – with an intuitive control panel, predictable pricing, team accounts, and more. -- DigitalOcean

### DigitalOcean vs Others

- DigitalOcean vs AWS, GCP, Azure
- DigitalOcean vs Linode, Hetzner Cloud
- DigitalOcean vs hosting

## DigitalOcean Structure

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

## DigitalOcean Management

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

## DigitalOcean Resources

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

- Product: https://www.digitalocean.com/products/managed-databases/
- Docs: https://www.digitalocean.com/docs/databases/

Manage Databases:

- Postgres
- MySQL
- Redis

#### Web UI

Go to: https://cloud.digitalocean.com/databases

#### Terraform

Docs:

- DB Cluster - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/database_cluster
- DB Connection Pool - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/database_connection_pool
- DB User - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/database_user
- DB Database - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/database_db

```tf
resource "digitalocean_database_cluster" "pg" {
  name       = "demo-pg"
  engine     = "pg"
  version    = "11"
  size       = "db-s-1vcpu-1gb"
  region     = "fra1"
  node_count = 1
}

resource "digitalocean_database_user" "foo" {
  cluster_id = digitalocean_database_cluster.pg.id
  name       = "foo"
}

resource "digitalocean_database_db" "bar" {
  cluster_id = digitalocean_database_cluster.pg.id
  name       = "bar"
}

resource "digitalocean_database_connection_pool" "pool-01" {
  cluster_id = digitalocean_database_cluster.pg.id
  name       = "demo-pg-pool-01"
  mode       = "transaction"
  size       = 20
  db_name    = digitalocean_database_db.bar.name
  user       = digitalocean_database_user.foo.name
}
```

#### CLI

[Docs](https://www.digitalocean.com/docs/apis-clis/doctl/reference/databases/)

List Databases:

```
doctl databases list
```

List Database Backups:

```
doctl databases backups <database_cluster_id>
```

### Spaces (S3)

- Product: https://www.digitalocean.com/products/spaces/
- Docs: https://www.digitalocean.com/docs/spaces/

#### Web UI

Got to: https://cloud.digitalocean.com/spaces

#### Terraform

[Docs](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/spaces_bucket)

Spaces requure `SPACES_ACCESS_KEY_ID` and `SPACES_SECRET_ACCESS_KEY` in provider

```tf
provider "digitalocean" {
  token             = var.digitalocean_token
  spaces_access_id  = var.access_id
  spaces_secret_key = var.secret_key
}
```

```
resource "digitalocean_spaces_bucket" "data" {
  name   = "data"
  region = "fra1"
}
```

### Monitoring

### VPC

### Firewalls

#### Web UI

Go to: https://cloud.digitalocean.com/networking/firewalls

#### Terraform

[Docs](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/firewall)

```tf
resource "digitalocean_firewall" "example" {
  name = "only-22-80-and-443"

  droplet_ids = [
    digitalocean_droplet.example.id
  ]

  inbound_rule {
    protocol         = "tcp"
    port_range       = "22"
    source_addresses = ["0.0.0.0/0", "::/0"]
  }

  inbound_rule {
    protocol         = "tcp"
    port_range       = "80"
    source_addresses = ["0.0.0.0/0", "::/0"]
  }

  inbound_rule {
    protocol         = "tcp"
    port_range       = "443"
    source_addresses = ["0.0.0.0/0", "::/0"]
  }

  inbound_rule {
    protocol         = "icmp"
    source_addresses = ["0.0.0.0/0", "::/0"]
  }

  outbound_rule {
    protocol              = "tcp"
    port_range            = "53"
    destination_addresses = ["0.0.0.0/0", "::/0"]
  }

  outbound_rule {
    protocol              = "udp"
    port_range            = "53"
    destination_addresses = ["0.0.0.0/0", "::/0"]
  }

  outbound_rule {
    protocol              = "icmp"
    destination_addresses = ["0.0.0.0/0", "::/0"]
  }
}
```

#### CLI

[Docs](https://www.digitalocean.com/docs/apis-clis/doctl/reference/compute/firewall/)

List firewalls

```
doctl compute firewall list
```

### Domains (DNS)

#### Web UI

Go to: https://cloud.digitalocean.com/networking/domains

#### Terraform

Docs:

- Domain - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/domain
- Record - https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/record

```tf
resource "digitalocean_domain" "example_com" {
  name = "example.com"
}

resource "digitalocean_record" "example_com" {
  domain = digitalocean_domain.default.name
  type   = "A"
  name   = ""
  value  = "1.1.1.1"
}

resource "digitalocean_record" "www_example_com" {
  domain = digitalocean_domain.default.name
  type   = "CNAME"
  name   = "www"
  value  = "${digitalocean_record.example_com.fqdn}."
}
```

#### CLI

[Docs](https://www.digitalocean.com/docs/apis-clis/doctl/reference/compute/domain/)

Create domain

```
doctl compute domain create <domain>
```

List domains

```
doctl compute domain list
```

Delete domain

```
doctl compute domain delete <domain>
```

List Records

```
doctl compute domain records list <domain>
```

Create Record

```
doctl compute domain records create <domain>  --record-type <record type> --record-name <record name> --record-data <record data>
```

Delete record

```
doctl compute domain records delete <domain> <record id>
```

### App Platform

## DigitalOcean Czech Community

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

# Getting Started with Terraform
Terraform is an infrastructure as code (IaC) tool for building and managing infrastructure resources.  In this guide you will install Terraform and perform basic tasks to build an NGINX container on Docker.

## Prerequisites
This guide assumes little to no experience with Terraform. If you are new to Terraform, refer to [What is Terraform?](https://developer.hashicorp.com/terraform/intro) 
- [Docker](https://docs.docker.com/get-docker/)

## Download Terraform
You can download Terraform as a binary package or through a package manager.  Visit [Download Terraform](https://www.terraform.io/downloads) to see download options based on your Operating System (OS).
### Path Configuration (Optional)
You can make the Terraform binary available from any PATH on your system. This will be based on your OS: 
#### macOS
- Open Terminal and print a list of locations in your PATH
```shell
$ echo $PATH

/usr/local/bin/
```
- Move the Terraform binary to one of the listed locations from the command above. The assumption is that the binary is currently in your downloads folder and that your $PATH includes /usr/local/bin
```shell
$ mv ~/Downloads/terraform /usr/local/bin/
```
#### Windows
- Open Command Prompt and open the Advanced System Properties Window.
```CMD
sysdm.cpl ,3
```
- Select *Environment Variables*.
- In the *System variables*, find and click the *Path* variable, and then click *Edit*.
- Click *New* and add the path of the folder where the Terraform executable is located.
- Click *OK* to save your changes. 
#### Verify Path Configuration
Verify path configuration was successful by opening a new command line session and running a Terraform command. We can start by getting the version of Terraform
```shell
$ terraform -version

Terraform v1.3.4
on darwin__amd64
```
With Terraform now installed and configured, you can now begin creating your infrastructure.

## Write Configuration File
Files which are used to describe infrastructure in Terraform are known as Terraform configuration. You will create a working directory and Terraform configuration. 

### Create a Working Directory
Each Terraform configuration must be in its own working directory. Create a directory for your configuration.
```shell
mkdir terraform-demo
```
Change to the newly created directory
```shell
cd terraform-demo
```
### Create a Configuration File

A configuragtion file is used by Terraform to build and maintain your infrastructure. Terraform configuration files have a `.tf` extension.

Create a Terraform Configuration File. 
```shell
$ touch main.tf
```

Open `main.tf` in your text editor, paste the configuration below, and save the file. 
```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```
This is a deployable configuration with Terraform.

## Terraform Initialize
When a new configuration is created, Terraform needs to be initialized within the directory. Terraform will look for providers in the configuration file, and download the providers outlined. In this guide you will install the docker provider.  

Initialize Terraform within the directory with `terraform init`. 
```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.23.0...
- Installed kreuzwerker/docker v2.23.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## Terraform Plan
A Terraform Plan will preview any changes that are made to your infrastructure before you apply them. Terraform will also perform a validation on any dependencies of the infrastructure being created.  

Create a Terraform Plan with `terraform plan`

```shell
$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + gateway                                     = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = (known after apply)
      + init                                        = (known after apply)
      + ip_address                                  = (known after apply)
      + ip_prefix_length                            = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "training"
      + network_data                                = (known after apply)
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + image_id    = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

## Terraform Apply
Apply the configuration by running the apply command. Terraform will review the plan and execute accordingly to create, update, or destroy infrastructure resources.  

Apply the changes to your infrastructure with `terraform apply`. 

When prompted, enter '*yes*' to perform and accept the changes. Wait for Terraform to finish executing and it will respond with a display message indicating which resources were added. 

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + gateway                                     = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = (known after apply)
      + init                                        = (known after apply)
      + ip_address                                  = (known after apply)
      + ip_prefix_length                            = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "training"
      + network_data                                = (known after apply)
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + image_id    = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
╷
│ Warning: Deprecated attribute
│ 
│   on main.tf line 15, in resource "docker_container" "nginx":
│   15:   image = docker_image.nginx.latest
│ 
│ The attribute "latest" is deprecated. Refer to the provider documentation for details.
│ 
│ (and one more similar warning elsewhere)
╵

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 0s [id=sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39fnginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=6c4b613dcdecbe5d94340d4c59215fbee9c9029f0aba0d578b690b7597a1672a]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

You have now created infrastructure using Terraform! Curl http://localhost in Terminal or open the URL in your web browser to see the default NGINX page.

## Terraform Destroy
You can remove any infrastructure in your configuration file no longer need. Terraform will also perform a validation on any dependencies of the infrastructure being removed.  

To remove infrastructure from configuration, run the `terraform destroy` command. 

When prompted, enter '*yes*' to perform and accept the changes. Wait for Terraform to finish executing and it will respond with a display message indicating that the resources were destroyed.
```shell
$ terraform destroy

docker_image.nginx: Refreshing state... [id=sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39fnginx:latest]
docker_container.nginx: Refreshing state... [id=6c4b613dcdecbe5d94340d4c59215fbee9c9029f0aba0d578b690b7597a1672a]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach                                      = false -> null
      - command                                     = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - container_read_refresh_timeout_milliseconds = 15000 -> null
      - cpu_shares                                  = 0 -> null
      - dns                                         = [] -> null
      - dns_opts                                    = [] -> null
      - dns_search                                  = [] -> null
      - entrypoint                                  = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env                                         = [] -> null
      - gateway                                     = "172.17.0.1" -> null
      - group_add                                   = [] -> null
      - hostname                                    = "6c4b613dcdec" -> null
      - id                                          = "6c4b613dcdecbe5d94340d4c59215fbee9c9029f0aba0d578b690b7597a1672a" -> null
      - image                                       = "sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39f" -> null
      - init                                        = false -> null
      - ip_address                                  = "172.17.0.2" -> null
      - ip_prefix_length                            = 16 -> null
      - ipc_mode                                    = "private" -> null
      - links                                       = [] -> null
      - log_driver                                  = "json-file" -> null
      - log_opts                                    = {} -> null
      - logs                                        = false -> null
      - max_retry_count                             = 0 -> null
      - memory                                      = 0 -> null
      - memory_swap                                 = 0 -> null
      - must_run                                    = true -> null
      - name                                        = "training" -> null
      - network_data                                = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode                                = "default" -> null
      - privileged                                  = false -> null
      - publish_all_ports                           = false -> null
      - read_only                                   = false -> null
      - remove_volumes                              = true -> null
      - restart                                     = "no" -> null
      - rm                                          = false -> null
      - runtime                                     = "runc" -> null
      - security_opts                               = [] -> null
      - shm_size                                    = 64 -> null
      - start                                       = true -> null
      - stdin_open                                  = false -> null
      - stop_signal                                 = "SIGQUIT" -> null
      - stop_timeout                                = 0 -> null
      - storage_opts                                = {} -> null
      - sysctls                                     = {} -> null
      - tmpfs                                       = {} -> null
      - tty                                         = false -> null
      - wait                                        = false -> null
      - wait_timeout                                = 60 -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39fnginx:latest" -> null
      - image_id    = "sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39f" -> null
      - latest      = "sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39f" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:943c25b4b66b332184d5ba6bb18234273551593016c0e0ae906bab111548239f" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.
│ Warning: Deprecated attribute
│ 
│   on main.tf line 15, in resource "docker_container" "nginx":
│   15:   image = docker_image.nginx.latest
│ 
│ The attribute "latest" is deprecated. Refer to the provider documentation for details.
╵

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=6c4b613dcdecbe5d94340d4c59215fbee9c9029f0aba0d578b690b7597a1672a]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:76c69feac34e85768b284f84416c3546b240e8cb4f68acbbe5ad261a8b36f39fnginx:latest]
docker_image.nginx: Destruction complete after 1s

Destroy complete! Resources: 2 destroyed.
```


## Next Steps
In this guide you learned how to install Terraform, build infrastructure, and remove infrastructure.  For further learning on concepts used in this guide:
 - [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
 - Read about the Terraform configuration language in the [Terraform Language Documentation](https://developer.hashicorp.com/terraform/language).
 - Learn more about Terraform [Providers](https://developer.hashicorp.com/terraform/language/providers).

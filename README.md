# Docker on Windows 10 for work with Hashistack and AWS

This docker container is used for provisioning clusters in AWS using Terraform and Ansible. It creates a work and test environment for working with Infrastructure-as-Code.

Latest version of Centos is used to create this container.
In the container, awscli, ansible, terraform and consul are installed, consul local server is started and git2consul service is started to fetch configuration from another git repository. The latter comes in handy when automatization takes place and the parameters fed to the infrastructure-as-code scripts define the properties of the infrastructure you wish to build.

1. Start Docker in Windows.
2. Open PowerShell.
3. Step into `docker-on-wins-hashistack` folder.
4. Create a file `aws_cred.env` and put `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` in them. Use `key=value` format. This file is used in the `docker build` line.
5. Create a file `id_rsa` in folder `copy_files` and add the private key to connect to the EC2 instances in AWS. This file is copied to the container in DockerFile.
6. File `copy_files/encrypt_consul.json` holds a 16-bytes, Base64 encoded key for encrypting traffic between consul servers and agents.
7. Create image by running

    ```bash
    docker build . --tag=hashistack-image
    ```

8. Create container based on the image

    ```bash
    docker run -itd --rm -p8500:8500 --name hashistack --hostname terraformer -v C:\marko\GitHub:/local-git --env-file "env.list" --env-file "aws_cred.env" hashistack-image
    ```

    Option `-v` maps the github repository on Windows drive to a folder in the container.
    Port `8501` is opened so that the Consul web UI can be reached from a web browser in Windows.

9. Start container

    ```bash
    docker exec -it hashistack bash
    ```

Once in the container:

1. Check `localhost:8501` in your web browser to see if Consul is running.
2. Type `terraform` to see if terraform is installed.
3. Type `aws` to see if awscli is installed.
4. Type `ansible` to see if ansible is installed.

If container should be recreated based on an updated image, stop the container by running

```bash
docker stop hashistack
```

 Now the new container, with the same name, can be created.

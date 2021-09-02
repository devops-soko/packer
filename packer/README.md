# Packer

## 1. Related Knowledge
### 1) HashiCorp 
- HashiCorp is the company to develop multiple infrastructure management tools such as terraforms, packers, etc.

### 2) Image file 
- Iamge file includes all necessary files and settings(os, services, program, etc.)
- Image file contains everything user needs to install, so user no longer needs to install or set other things

### 3) Provisioning 
- configure & provide os, server, network, service, etc to meet users' needs



## 2. Basic Concepts of Packer
### 1) Fuction 
image creation -> users can used created images for cloud or infrasturcture(ex. aws AMI, vmware iso, vmware esxi image, ...)

### 2) How to use? 
```
AWS case,

AMI can be customized via downloaded tools like ruby, node, etc.
Packers can automate the process of customizing AMI

cf. AMI : The image providing the information which needs to create an instance in AWS
```

### 3) Process 
```
AWS case,

step 1. Packer reads templates written in json
step 2. Create an instance on AWS that reflects the template
step 3. if the instance is created successfully, Packer believes there is no problem with the template.
step 4. Create Image File
```

### 4) Features
- Packer does not start from zero base
- Instead, Packer customizes image files of existing platforms (ex. AWS, dockers, vmware)

### 5) Components of template
- template : JSON file consists of a series of Packer's declarations and commands
- builder (https://www.packer.io/docs/builders)
Create machines and generate images depends on platform(AWS, GCP, ...) and os(window, linux, ...)

- provisioners (https://www.packer.io/docs/provisioners)
Use builtin and third-party software(shell script, ansible, chef, ..) to configure the image after booting

- post-processors (https://www.packer.io/docs/post-processors)
Set up additional process to run after the image is built by the builder and provisioned 

### 6) Reference : https://www.packer.io/docs



## 3. Packer Installation
- Linux : https://learn.hashicorp.com/tutorials/packer/get-started-install-cli
- Windows (https://millionairedeveloper.tistory.com/63)
```
step 1. download a file (https://releases.hashicorp.com/packer/)
step 2. unzip downloaded file 
step 3. move the file to C:\packer
step 4. add C:\packer to path of [System Variables] of [Environment Variables] 
```



## 4. Packer CLI
- build : build image(s) from template 
```
cf. debug
$ packer build -debug [template name]
$ PACKER_LOG=1 packer build -debug [template name] > [log file name].txt 

```
- validate : Check the syntax and configuration of a template
- inspect : Provide a brief description of the template
- fix : Find incompatible parts of the latest packer version in the template and replace them to be compatible 
- version : Check packer's version




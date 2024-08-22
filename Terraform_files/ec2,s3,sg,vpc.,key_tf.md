 cat ec2-server.tf




resource "aws_key_pair" "my_ssh_key" {
     key_name= "terraform-auto-key"
     public_key= file("/home/ubuntu/terraform-auto-key.pub")

}

resource "aws_default_vpc" "default"{
}


 resource "aws_security_group" "my_security"{
        name= "server-sg"
        description = "this is security groups description"

        vpc_id= aws_default_vpc.default.id

        ingress{

        from_port= 22
        to_port=22
        protocol= "tcp"
        cidr_blocks=["0.0.0.0/0"]
        description= "this is for ssh"
}


        ingress{

        from_port= 80
        to_port=80
        protocol= "tcp"
        cidr_blocks=["0.0.0.0/0"]
        description= "this is for http"
}


         egress{

        from_port= 0
        to_port=0
        protocol= "-1"
        cidr_blocks=["0.0.0.0/0"]
         description= "this is for outside world"

}
}

resource "aws_instance" "my_instance" {


  ami             = "ami-0e86e20dae9224db8"
  instance_type   = "t2.micro"

     key_name         = aws_key_pair.my_ssh_key.key_name
        security_groups=[aws_security_group.my_security.name]


  tags = {
    Name = "myserver created by terraform server"
  }
}

resource "aws_ec2_instance_state" "my_state" {
       instance_id=aws_instance.my_instance.id
       state= "running"
}

output "my_ec2_ip" {

          value = aws_instance.my_instance.public_ip

}


=======================
 cat terraform.tf
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.63.1"
    }
  }
}
==================================
 cat providers.tf

provider "aws" {

  region = "us-east-1" # Secondary region, if needed
}
======================================
ubuntu@ip-172-31-36-177:~$ cat s3.tf



resource "aws_s3_bucket" "pc_bucket" {


              bucket="pc-bucket-1503976440070"


        tags = {
                  Name        = "pc bucket demo pool"
                   Environment = "Dev"
      }


}

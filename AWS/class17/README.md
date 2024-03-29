![Alt text](image.png)

> 參考連結：[連結](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance)

```
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "myvpc"{
    cidr_block="192.168.0.0/16"
    tags = {
        Name = "myvpc"
    }
}
```

![Alt text](image-1.png)

![Alt text](image-2.png)

```
resource "aws_subnet" "testvpc-web" {
    vpc_id = "${aws_vpc.testvpc.id}"
    cidr_block = "192.168.1.0/24"
    availability_zone = "us-east-1a"
    tags = {
        Name = "testvpc-web"
    }
}
```

![Alt text](image-3.png)

![Alt text](image-4.png)

```
resource "aws_internet_gateway" "igw" {
    vpc_id="${aws_vpc.myvpc.id}"
    tags = {
        Name = "myvpcvpc-igw"
    }
}
```

![Alt text](image-5.png)

![Alt text](image-6.png)

![Alt text](image-7.png)

![Alt text](image-8.png)

![Alt text](image-9.png)

```
resource "aws_route_table" "myrt" {
    vpc_id="${aws_vpc.myvpc.id}"

    route {
        cidr_block="0.0.0.0/0"
        gateway_id = "${aws_internet_gateway.igw.id}"
    }

    tags = {
        Name = "myvpc-rt"
    }
}

resource "aws_route_table_association" "myrt_assoc" {
    subnet_id = aws_subnet.myvpc-web.id
    route_table_id = aws_route_table.myrt.id
}
```

![Alt text](image-10.png)

```
resource "aws_security_group" "sg_myvpc" {
    name = "sg_myvpc"
    description="security group for myvpc"
    vpc_id="${aws_vpc.myvpc.id}"

    ingress {
       description = "HTTPS traffic"
       from_port = 443
       to_port = 443
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    ingress {
       description = "HTTP traffic"
       from_port = 80
       to_port = 80
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    ingress {
       description = "SSH traffic"
       from_port = 22
       to_port = 22
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    egress {
        from_port = 0
        to_port = 0
        protocol ="-1"
        cidr_blocks= ["0.0.0.0/0"]  
    }

    tags = {
        Name = "sg_myvpc"
    }
}
```

![Alt text](image-11.png)

```
resource "aws_network_interface" "webserver-nic" {
    subnet_id = aws_subnet.myvpc-web.id
    private_ips=["192.168.1.100"]
    security_groups = ["${aws_security_group.sg_myvpc.id}"]  
}

resource "aws_eip" "web-eip" {
    vpc = true
    network_interface = aws_network_interface.webserver-nic.id
    associate_with_private_ip = "192.168.1.100"
    depends_on = [aws_internet_gateway.igw] 
}
```

![Alt text](image-12.png)

```
resource "aws_instance" "webserver" {
  ami="ami-0c7217cdde317cfec"
  instance_type = "t2.micro"
  availability_zone = "us-east-1a"
  key_name = "myAWS"

  network_interface {
    device_index = 0
    network_interface_id = aws_network_interface.webserver-nic.id
  }
  
  user_data = <<-EOF
              #!/bin/bash
              sudo apt update -y
              sudo apt install apache2 -y
              sudo systemctl start apache2
              sudo bash -c 'echo your very first web server > /var/www/html/index.html'
              EOF   
  tags ={
    Name = "web-server"
  }
}
```

![Alt text](image-13.png)

![Alt text](image-14.png)

![Alt text](image-15.png)

```
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "myvpc"{
    cidr_block="192.168.0.0/16"
    tags = {
        Name = "myvpc"
    }
}

resource "aws_subnet" "myvpc-web" {
    vpc_id = "${aws_vpc.myvpc.id}"
    cidr_block = "192.168.1.0/24"
    availability_zone = "us-east-1a"
    tags = {
        Name = "myvpc-web"
    }
}

resource "aws_internet_gateway" "igw" {
    vpc_id="${aws_vpc.myvpc.id}"
    tags = {
        Name = "myvpc-igw"
    }
}

resource "aws_route_table" "myrt" {
    vpc_id="${aws_vpc.myvpc.id}"

    route {
        cidr_block="0.0.0.0/0"
        gateway_id = "${aws_internet_gateway.igw.id}"
    }

    tags = {
        Name = "myvpc-rt"
    }
}

resource "aws_route_table_association" "myrt_assoc" {
    subnet_id = aws_subnet.myvpc-web.id
    route_table_id = aws_route_table.myrt.id
}

resource "aws_security_group" "sg_myvpc" {
    name = "sg_myvpc"
    description="security group for myvpc"
    vpc_id="${aws_vpc.myvpc.id}"

    ingress {
       description = "HTTPS traffic"
       from_port = 443
       to_port = 443
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    ingress {
       description = "HTTP traffic"
       from_port = 80
       to_port = 80
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    ingress {
       description = "SSH traffic"
       from_port = 22
       to_port = 22
       protocol ="tcp"
       cidr_blocks = ["0.0.0.0/0"]   
    }

    egress {
        from_port = 0
        to_port = 0
        protocol ="-1"
        cidr_blocks= ["0.0.0.0/0"]  
    }

    tags = {
        Name = "sg_myvpc"
    }
}

resource "aws_network_interface" "webserver-nic" {
    subnet_id = aws_subnet.myvpc-web.id
    private_ips=["192.168.1.100"]
    security_groups = ["${aws_security_group.sg_myvpc.id}"]  
}

resource "aws_eip" "web-eip" {
    vpc = true
    network_interface = aws_network_interface.webserver-nic.id
    associate_with_private_ip = "192.168.1.100"
    depends_on = [aws_internet_gateway.igw] 
}

resource "aws_instance" "webserver" {
  ami="ami-0c7217cdde317cfec"
  instance_type = "t2.micro"
  availability_zone = "us-east-1a"
  key_name = "myAWS"

  network_interface {
    device_index = 0
    network_interface_id = aws_network_interface.webserver-nic.id
  }
  
  user_data = <<-EOF
              #!/bin/bash
              sudo apt update -y
              sudo apt install apache2 -y
              sudo systemctl start apache2
              sudo bash -c 'echo your very first web server > /var/www/html/index.html'
              EOF   
  tags ={
    Name = "web-server"
  }
}
```
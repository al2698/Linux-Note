# ALB + terraform

## vpc
```
variable "availability_zones" {
  default = ["us-east-1a", "us-east-1b"]
}

variable "ips" {
  default = ["192.168.1.0/24", "192.168.2.0/24"]
}


resource "aws_vpc" "vpc" {
  cidr_block="192.168.0.0/16"

  tags = {
    Name = "vpc"
  }
}

resource "aws_internet_gateway" "igw" {
    vpc_id="${aws_vpc.vpc.id}"
    tags = {
        Name = "igw"
    }
}

resource "aws_route_table" "rt" {
    vpc_id="${aws_vpc.vpc.id}"

    route {
        cidr_block="0.0.0.0/0"
        gateway_id = "${aws_internet_gateway.igw.id}"
    }

    tags = {
        Name = "rt"
    }
}

resource "aws_subnet" "subnets" {
  count             = length(var.availability_zones)
  vpc_id            = aws_vpc.vpc.id
  cidr_block        = var.ips[count.index]
  availability_zone = var.availability_zones[count.index]

  tags = {
    Name = "subnet${count.index}"
  }
}

resource "aws_route_table_association" "rt" {
    count = length(var.availability_zones)
    subnet_id = aws_subnet.subnets[count.index].id
    route_table_id = aws_route_table.rt.id
}
```

## ec2
```

resource "aws_instance" "ec2_web" {
  ami = "ami-079db87dc4c10ac91"
  instance_type = "t2.micro"
  count = length(var.availability_zones)
  associate_public_ip_address = "true"
  subnet_id = aws_subnet.subnets[count.index].id
  security_groups = ["${aws_security_group.sg_http.id}"]
  user_data = <<-EOF
     #!/bin/bash
     sudo su
     yum update -y
     yum install httpd -y
     systemctl start httpd
     systemctl enable httpd
     echo "<h1>web${count.index}</h1>" > /var/www/html/index.html
  EOF
  tags = {
    name ="ec2_web${count.index}"
  }
}
```

## alb
```
# target group
resource "aws_lb_target_group" "tg" {
  name        = "tg"
  port        = 80
  protocol    = "HTTP"
  target_type = "instance"
  vpc_id      = aws_vpc.vpc.id
}

# creating ALB
resource "aws_lb" "lb" {
  name               = "lb"
  internal           = false
  load_balancer_type = "application"
  subnets            = [for subnet in aws_subnet.subnets: subnet.id]
  security_groups    = [aws_security_group.sg_http.id]
  ip_address_type    = "ipv4"
  

  tags = {
    name = "lb"
  }
}

resource "aws_lb_listener" "alb-listener" {
  load_balancer_arn = aws_lb.lb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.tg.arn
  }
}

resource "aws_lb_target_group_attachment" "ec2_attach" {
  count            = length(aws_instance.ec2_web)
  target_group_arn = aws_lb_target_group.tg.arn
  target_id        =  aws_instance.ec2_web[count.index].id
}
```

## output website
```
output "elb-dns-name" {
  value = aws_lb.lb.dns_name
}
```
provider "aws"{
  region = "us-east-1"
  access_key = "AKIAYPXAF3TUXN7UWJEI"
  secret_key = "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"
}
resource "aws_instance" "myias" {
    ami = "ami-0715c1897453cabd1"
    key_name = "firstkeypair"
    instance_type = "t2.micro"
    subnet_id = aws_subnet.firstsn.id
    vpc_security_group_ids = [aws_security_group.my_sg1.id]

    tags = {
        name = "myias"
    }    
}
resource "aws_vpc" "myvpc" {
   cidr_block = "10.0.0.0/16"

}
resource "aws_subnet" "firstsn" {
  vpc_id     = aws_vpc.myvpc.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "firstsn"
  }
}
resource "aws_internet_gateway" "gw1" {
  vpc_id = aws_vpc.myvpc.id

  tags = {
    Name = "gw1"
  }
}
resource "aws_route_table" "rt1" {
  vpc_id = aws_vpc.myvpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw1.id
  }

  tags = {
    Name = "rt1"
  }
}
resource "aws_route_table_association" "rta1" {
  subnet_id      = aws_subnet.firstsn.id
  route_table_id = aws_route_table.rt1.id
}
resource "aws_security_group" "my_sg1" {
  name        = "my_sg1"
  vpc_id      = aws_vpc.myvpc.id


  ingress {
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "my_sg1"
  }
}

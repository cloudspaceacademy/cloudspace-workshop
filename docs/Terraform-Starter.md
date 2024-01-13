# Terraform Starter

### **Creating AWS Infrastructure with Terraform**

**Introduction**

This documentation provides step-by-step instructions on using Terraform to create an AWS EC2 instance in the eu-north-1 region. The example code specifies an AMI ID for ubuntu-focal-20.04-amd64 (ami-07ec4220c92589b40) and uses a t3.micro instance type.

### **Prerequisites**

**AWS Account:** Ensure that you have an AWS account. If you don't have one, you can sign up at [AWS Console](https://aws.amazon.com/)

**AWS CLI Installation:** Install the AWS CLI on your local machine. You can download it from the [AWS CLI official website](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

**Terraform Installation:** Install Terraform on your local machine. You can download Terraform from the [official Terraform website](https://developer.hashicorp.com/terraform/install)

**AWS Credentials:** Configure your AWS credentials on your machine using the AWS CLI. Run **aws configure** and provide your AWS Access Key, Secret Key, region (eu-north-1), and output format.

### **Terraform Configuration**

**Create a Terraform Configuration File:**
Create a file named **main.tf** and paste the following Terraform configuration:
```
# Specify the AWS provider and region
provider "aws" {
  region = "eu-north-1"  # Change this to your desired AWS region
}

# Define an AWS EC2 instance
resource "aws_instance" "example_instance" {
  ami           = "ami-07ec4220c92589b40"  # Change this to a valid AMI ID for your region
  instance_type = "t3.micro"                # Change this to your desired instance type

  tags = {
    Name = "example-instance"
  }
}
```

**Initialize Terraform:**

Open a terminal, navigate to the directory containing main.tf, and run:

```terraform init```

**Apply Terraform Configuration:**

Run the following command to apply the Terraform configuration:

```terraform apply```

Terraform will prompt you to confirm the plan. Type yes and press Enter.


### **Access AWS Console:**

Visit the AWS Console and log in with your AWS credentials.

**Verify EC2 Instance:**

Navigate to the EC2 service in the AWS Console. You should see a new EC2 instance with the specified configuration.

### **Conclusion**

You have successfully created an AWS EC2 instance using Terraform. This example can be expanded and customized based on your specific requirements.







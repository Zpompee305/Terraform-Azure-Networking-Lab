# Azure VNet-to-VNet Peering Lab with Terraform

## Project Description

This project demonstrates the creation and configuration of a VNet-to-VNet peering connection between two distinct Azure Virtual Networks (VNets) located in different Azure regions. The infrastructure is defined and deployed using Terraform, an Infrastructure as Code (IaC) tool.

The lab sets up:
* Two Azure Resource Groups (one in East US, one in West US).
* Two Virtual Networks, each with a dedicated application subnet and a gateway subnet (though no actual gateway is deployed for direct peering).
* A VNet Peering connection established bidirectionally between the two VNets.
* Two Windows Virtual Machines (VMs), one in each VNet, connected to their respective application subnets.
* Network Security Groups (NSGs) associated with each VM's Network Interface Card (NIC) to allow RDP access and inter-VNet communication.

The primary goal is to illustrate how private IP addresses can communicate across physically separate VNets by leveraging VNet Peering, effectively extending a single private network across different Azure regions.

## Architecture Diagram

[YOUR ARCHITECTURE DIAGRAM HERE]

* **Suggestion:** You can draw a simple diagram showing two boxes representing the VNets (VNet-EastUS-TF and VNet-WestUS-TF), with subnets and VMs inside each. Draw a line connecting the two VNets, labeled "VNet Peering." Include public and private IPs for the VMs. You can use tools like draw.io, Lucidchart, or even simple drawing software. Take a screenshot of your diagram and paste it here, or upload it to your GitLab project and link it.

## Prerequisites

Before deploying this infrastructure, ensure you have the following installed and configured:

1.  **Azure Free Account:** Provides credits and free services to get started.
2.  **Azure CLI:** Used for authenticating Terraform with your Azure subscription.
3.  **Terraform:** The Infrastructure as Code tool.
4.  **Git:** For version control and pushing your code to GitLab.
5.  **A Text Editor/IDE:** Such as Visual Studio Code.

## Deployment Steps (Using Terraform)

1.  **Clone this Repository (if you haven't already):**
    ```bash
    git clone [https://gitlab.com/your-username/azure-vnet-peering-lab.git](https://gitlab.com/your-username/azure-vnet-peering-lab.git)
    cd azure-vnet-peering-lab
    ```
    *(Note: If you're building this from scratch as we are, you'll create the directory and files manually first, then initialize git and push later.)*

2.  **Log in to Azure:**
    Open your command prompt/terminal and run:
    ```bash
    az login
    ```
    Follow the browser prompts to authenticate with your Azure account.

3.  **Initialize Terraform:**
    This command initializes the Terraform working directory and downloads the necessary Azure provider plugin.
    ```bash
    terraform init
    ```

4.  **Review the Plan (CRITICAL for Cost Control!):**
    Always review the plan before applying to understand what resources Terraform will create, modify, or destroy.
    ```bash
    terraform plan
    ```
    Review the output carefully to ensure it aligns with your expectations.

5.  **Apply the Configuration:**
    This command will deploy the infrastructure as defined in `main.tf` to your Azure subscription.
    ```bash
    terraform apply
    ```
    Type `yes` when prompted to confirm the deployment. This step will take several minutes to complete as VMs are provisioned.

6.  **Retrieve VM Information:**
    After a successful `terraform apply`, Terraform will output the public and private IP addresses of your VMs. Make a note of these.
    ```bash
    # Example output after apply:
    # Outputs:
    # vm_eastus_public_ip = "X.X.X.X"
    # vm_westus_public_ip = "Y.Y.Y.Y"
    # vm_eastus_private_ip = "10.0.1.X"
    # vm_westus_private_ip = "10.1.1.Y"
    ```

## Testing the VNet Peering

1.  **Connect to `VM-EastUS-TF`:**
    * Use Remote Desktop Protocol (RDP) to connect to `VM-EastUS-TF` using its `vm_eastus_public_ip` (from Terraform outputs).
    * Log in with the username `azureadmin` and the `vm_admin_password` you set in your `main.tf` file.

2.  **Configure Windows Firewall (IMPORTANT for Ping):**
    * Once logged into `VM-EastUS-TF`, open **Server Manager** -> **Tools** -> **Windows Defender Firewall with Advanced Security**.
    * Click "Windows Defender Firewall Properties".
    * For `Domain Profile`, `Private Profile`, and `Public Profile`, set "Firewall state" to `Off`.
        * **NOTE:** This is for lab purposes to easily test ping. In a production environment, you should create specific inbound rules for ICMP (ping) if needed, rather than disabling the firewall entirely.

3.  **Ping `VM-WestUS-TF`'s Private IP:**
    * Open **Command Prompt** inside `VM-EastUS-TF`.
    * Ping the private IP address of `VM-WestUS-TF` (e.g., `10.1.1.X` from your Terraform outputs):
        ```bash
        ping <vm_westus_private_ip_address>
        ```
    * You should observe successful replies, confirming that VNet Peering is correctly established and allowing private network communication between the two VNets.

## Cleanup (CRITICAL to Avoid Costs!)

Once you have completed your testing and captured your screenshots, it is **imperative** to destroy the deployed resources to avoid incurring ongoing Azure costs.

1.  **Return to your local terminal** (where you ran `terraform apply`).
2.  **Run the destroy command:**
    ```bash
    terraform destroy
    ```
3.  Type `yes` when prompted to confirm the deletion of all resources.
    * This process will take several minutes. Once complete, all Azure resources created by this Terraform configuration will be removed from your subscription, and billing for them will stop.

## Lessons Learned

[YOUR INPUT HERE]

* What did you learn about VNet Peering?
* What did you learn about Network Security Groups (NSGs)?
* What did you learn about deploying infrastructure with Terraform?
* Did you encounter any challenges, and how did you resolve them?
* What are the security implications of VNet Peering?
* How does this lab relate to AZ-104 objectives?

## Screenshots

[YOUR SCREENSHOTS HERE]

* **Screenshot 1:** Successful `terraform apply` output in your terminal.
* **Screenshot 2:** The VNet Peering status in the Azure portal (e.g., navigate to `VNet-EastUS-TF`, then "Peerings" under "Settings"). Show both peerings as "Connected".
* **Screenshot 3:** The successful `ping` results from `VM-EastUS-TF` to `VM-WestUS-TF`'s private IP address in the VM's command prompt.

---
\## Security Groups vs UFW

A Security Group sits on the outside of the EC2 instance entirely at the amazon infrastructure layer and filters traffic before it even reaches the instance's network interface. Whereas UFW runs inside the instance and filter traffic at the kernel level. Both are important because an attacker can get root on the instance but they cannot touch the security group because it does not live on the machine they compromised. In this setup Security Groups allow SSH inbound traffic from a single trusted  IP on port 22  and ufw provides a second layer of filtering at the OS level  meaning if security groups were not configured properly then ufw would still block unauthorized traffic before it reaches any service.



!\[Security Group inbound rules](evidence/security-group-inbound-rules.png)



\## What EC2 Actually Is

An EC2 instance is just like my Ubuntu VM, it has a Hypervisor that sits between physical hardware and the VMs running on top of it, intercepts every request a VM makes to hardware, and either fulfils it with real hardware or emulates it with software. The difference is the physical machine the VMs runs on belong to Amazon and sits in their data center instead of on my desk. Amazon run a hypervisor they built called Nitro, so when I launch an EC2 instance Amazon is handing me one of those VMs. I get a slice of the real physical server, so my own allocated RAM, CPU and disk that is fully isolated from every other customer's VM running on the same physical machine. When running `uname -a` in the VM it shows me system information like `7.0.0-1004-aws` which shows that this is a custom AWS kernel built not a standard Ubuntu kernel name, `Linux ip-172-31-45-65` showing the hostname which AWS derives from the private ip `172.31.45.65`, which can only be reached inside the VPC and using `curl ifconfig.me` to see the public ip address `13.221.35.64` which can be reached by internet.  



!\[EC2 instance details](evidence/ec2-instance-details.png)

!\[Terminal commands output](evidence/terminal-commands.png)



\## IAM Structure 

IAM which stands for Identity and Access Management is the permission system for AWS resources specifically. It answers questions like if a developer is allowed to launch an EC2 instance but cannot touch billing, if an automated script can read from this storage bucket but cannot write to it, if an intern can view infrastructure but cannot modify anything, and also that nobody except the root account can delete the entire AWS account, so every action taken in the cloud IAM checks whether the identity making that request has permission to do it. When I first set up my AWS account I immediately added MFA on the root account. Even if an attacker brute forces my password they still have to know the time-based code that my authenticator app generates locally  using a shared secret that was set up during MFA configuration. It never travels over the network, so intercepting my traffic is useless. Then I logged out of root and logged in as a new IAM user with a custom password and an attached AdministratorAccess policy. I did this because the root account gives a user maximum permissions so they can do anything like, delete the AWS Account, get access to billing, etc . So by creating a new IAM user attached with AdministratorAccess policy I can do almost anything but billing and account level setting are locked. This means if an attacker gets access to this account they can build and break infrastructure all day but cannot steal my payment methods or delete my AWS account.

!\[IAM user with AdministratorAccess policy](evidence/tofu-fingerprint.png)
!\[MFA enabled on root account](evidence/mfa-root.png)



\## SSH connection process 

I decided to ssh into my EC2 instance using my own key pairs instead of using the key pairs created by AWS. This is because when AWS generates keys the private key exists on Amazon's infrastructure for a moment before I download it, but by generating my own key pair it means that my private key was never anywhere besides my own machine from the moment it was created. So I created my own ed25519 key pair in WSL in my `\~/.ssh/` directory with 700 permissions because only I should be able to access this directory. Imported the public key into AWS and created the EC2 instance. When I first ssh into the EC2 instance I got a TOFU message asking if I wanted to trust the fingerprint of this server. To ensure it was the authentic server and not an attacker trying to carry out a man in the middle attack I went to the EC2 console, Actions, Monitor and Troubleshot, get system logs and  checked the server's fingerprint and compared it what the client received to ensure the fingerprint was authentic. Then I entered the passphrase for my private key and successfully ssh into the EC2 instance.

!\[TOFU fingerprint verification](evidence/tofu-fingerprint.png)
!\[Successful SSH connection](evidence/ssh-connection.png)


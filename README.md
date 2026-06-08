# Month 3 Cloud Security
Month 3 started off with a pentest report  to audit the Ubuntu server which showed that there were 5 vulnerabilities, each rated by risk level. Some risks were remediated while the others  were deliberately accepted and were documented . Month 3 proved that the hardened server in month 1 and a containerized API from month 2 could survive on real infrastructure and be reached by anyone on the public internet.

## Month 3 Week 1 (Week 9) Pentest Report
I built a Pentest report that audited my Ubuntu Server and found 5 vulnerabilities. Out of the five vulnerabilities the leaking of the OS fingerprint was fixed and a firewall was added with a default deny all incoming rule making server's ports state hidden from the outside unless there was a rule that was explicitly added.  Before hardening the servers ports state would show up as closed this is because the kernel was sending back a [RST.] telling Nmap that there is no service listening on this port whereas with a firewall enabled the port state shows up a filtered this is because the kernel is dropping traffic going to the port so Nmap does not get a response and eventually times out. 

[View Week 9 Pentest Report](./week9-pentest-report/README.md)


## Month 3 Week 2 (Week 10) AWS EC2 Instance
Created an AWS account and enabled MFA for the root account. Set up IAM and enabled MFA for my regular user account with administrator privileges, created my keys in WSL and deployed an AWS EC2 instance with my public key imported so that I could SSH into it but checked the Server's fingerprint out of band first before accepting TOFU prompt to prevent MITM attack. Understood the difference between UFW which runs inside the instance and is enforced at the kernel level and security groups which sit outside the instance entirely on the AWS infrastructure layer. Added a security group rule to only allows SSH traffic in from my host IP. This proved that just like in Month 1 where I SSHed into my Ubuntu VM I could do the same thing with an EC2 instance which is just a VM in the cloud running on AWS hardware.

[View Week 10 AWS EC2 Instance](./week10-aws-ec2/README.md)

## Month 3 Week 3 (Week 11) Public Flask REST API EC2
Docker was installed on my EC2 instance so that I could pull my Flask REST API image from Docker hub and create a container with port publishing to map the containers port 5000 to the EC2 instance's port 80. Added a security group rule to allow traffic into port 80 from anywhere, allocated an elastic ip and attached it to the EC2 instance so that any user on the public internet can send requests to my Flask REST API without the request breaking and receive a response back.

[View Week 11 Public Flask API EC2](./week11-public-flask-api-ec2/README.md)

## What All Three Months Covered
Month 1 built a hardened Linux Server, Month 2 built a Flask REST API that was built and containerized using Docker and uploaded to Docker hub which is Docker's public image registry, and Month 3 proved that I could apply what was learned and built in Month 1 and Month 2 in an AWS EC2 instance in the cloud that can be reached by anyone on the public internet.



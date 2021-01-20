# `zookeeper`

This configuration defines a simple Zookeeper AMI in Packer.

## How do I build one on localhost?

This configuration uses environment variables as inputs for building in an arbitrary account. You must provide these inputs via environment variables:
* `AWS_REGION`
* `VPC_ID`
* `SUBNET_ID` - the instance will launch in this subnet
* `SECURITY_GROUP_ID` - this security group will need to permit the SSH port inbound for building
* `IAM_INSTANCE_PROFILE` - the instance will launch with this IAM profile name. Right now, it needs no elevated permissions; this is in place only to prevent Packer from creating its own instance profile.
* `AWS_CREDENTIALS_PROFILE` - Packer will use this credentials profile to launch the instance. If your configuration has only the default credential profile defined, `default` works as an input here.

Then simply run:
```
packer build zookeeper.json
```

## How do I use the resultant AMI?
You will need to add connection details for each instance in the Zookeeper configuration during userdata runs. This configuration allows Zookeeper to find other nodes in its cluster. This looks something like this:

```
echo "#server.1=zookeeper1:2888:3888
#server.2=zookeeper2:2888:3888
#server.3=zookeeper3:2888:3888
" >> /etc/zookeeper/zoo.cfg
systemctl restart zookeeper
```

## Errata
Original owner ID `099720109477` is Canonical's Ubuntu account in at least us-east-1, fetched from the AMI launch page.

The Cloudfront distribution we fetch the Inspector GPG key from was fetched from [AWS's documentation](https://docs.aws.amazon.com/inspector/latest/userguide/inspector_verify-sig-agent-download-linux.html).

I would have loved to use [`exhibitor`](https://github.com/soabase/exhibitor) for cluster creation, but unfortunately it looks unmaintained and we don't have Java expertise in-house.

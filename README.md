# Kangaroo 🦘
A simple to use SSH Jump bastion. 

## Installation
Kangaroo can be installed by downloading it from apt and yum repo, more information on those repos is available [https://github.com/hamptonmoore/repo](https://github.com/hamptonmoore/repo)

## Running
```
kangaroo -c <config>
```

## Config
The config is a simple json file that looks like this:
```json
{
    "Port": 2222,
    "SSHKey": "~/.ssh/id_ed25519",
    "IPSet": {
        "All": ["::/0", "0.0.0.0/0"],
        "AllowAllRanges": [
            "192.168.1.0/24"
        ],
        "NetworkAdmins": [
            "192.168.0.2",
            "192.168.0.3"
        ]
    },
    "Policy": {
        "NetworkAdmins": {
            "Default": "allow",
            "OnL2": true
        },
        "All": {
            "Default": "deny",
            "Allow": ["AllowAllRanges"]
        }
    }
}
```
This example configuration listens on port 2222 and allows anybody to connect to the jump box to access the subnet 192.168.1.0/24.
In addition to that, the jump box allows the IPs 192.168.0.2 and 192.168.0.3 to jump to ANY ip address as long as they are on the same L2. 

## Layer 2
Currently L2 detection is done by sending out a ping and checking the iproute2 neigh table. Possible improvements could be sending ICMP with a TTL of 1 and just seeing if there is a response, or by spawning the SSH connection with a TTL of 1.

## IPSets
The IPSets are a simple list of IP addresses and CIDR ranges.  

## Policies
Policies are executed top to bottom as defined in the config. 
The name of the policy is the affected ipset.
The first policy that both matches an IP and takes an action on it will be applied with subsequent policies ignored.
It should be noted Default counts as an action in that case.
# AzureMetadata
Retrieving instance metadata
Instance metadata is available for running VMs created/managed using Azure Resource Manager. Access all data categories for an instance use the following URI

$ curl -H Metadata:true http://169.254.169.254/metadata/instance?api-version=2017-03-01
The default output for all instance metadata is of json format(content type Application/JSON)

Usage Examples
Following are set of examples and usage semantics for instance metadata service

Versioning
Instance metadata service is versioned. Versions are mandatory and the current preview version is 2017-03-01.

curl -H Metadata:true http://169.254.169.254/metadata/instance?api-version=2017-03-01
As we add newer versions, earlier version is made available in case your scripts has dependencies on data formats. Note, current preview version may not be available once the service is generally available

Data output
By default instance metadata returns data in JSON (content type=application/json).Different node elements can return data in different default format as applicable, following table is a quick reference for data formats

Element	default data format	Other formats
/instance	Json	text
/scheduledevents	Json	None
For text format use format=text in the request URL, for example

curl -H Metadata:true http://169.254.169.254/metadata/instance?api-version=2017-03-01\&format=text
Security
Instance metadata endpoint is accessible only from within the running instance on a non-routable 169.254.169.254 IP address. In addition, any request with X-Forwarded-For header is rejected by the metadata service. We also require requests to contain Metadata:true header being sent to ensure that the actual request was directly intended and not a part of unintentional redirection.

Error
If there is a data element not found or malformed requests Metadata Service returns standard HTTP Error, following are the few examples of return codes

HTTP Return Code	Reason
200	OK
400	Bad Request, missing header, pass -H Metadata:true
404	Not Found, requested element does’t exist
405	Method not supported
429	Too many requests, currently we only support maximum of 5 queries per second
Examples
Retrieving the network information
curl -H Metadata:true http://169.254.169.254/metadata/instance/network?api-version=2017-03-01

{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
Retrieving public IP address
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
Retrieving all instance metadata
curl -H Metadata:true http://169.254.169.254/metadata/instance?api-version=2017-03-01

{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}
Instance Metadata data categories
Following table has a list of all data categories available via Instance Metadata

Data	Description
location	Azure Region the VM is running
name	Name of the VM
offer	Offer information for the VM image, these values are present only for images deployed from Azure image gallery
publisher	Publisher of the VM image
sku	Specific SKU for the VM image
version	Version of the VM Image
osType	Linux or Windows
platformUpdateDomain	Update domain the VM is running in.
platformFaultDomain	Fault domain the VM is running in.
vmId	Unique identifier for the VM, more info here
vmSize	VM size
ipv4/Ipaddress	Local IP address of the VM
ipv4/publicip	Public IP address for the Instance
subnet/address	Address for subnet
subnet/dnsservers/ipaddress1	Primary DNS server
subnet/dnsservers/ipaddress2	Secondary DNS server
subnet/prefix	Subnet prefix, example 24
ipv6/ipaddress	IPv6 address for the VM
mac	VM mac address
scheduledevents	see scheduledevents

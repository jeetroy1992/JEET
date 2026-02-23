```java
________________________________________
🔹 Basic Operator
✅ Clean Working Code
print("HelloWorld")
print("Hello\nMy name is Jeet Roy")

First_Name = "Jeet"
Last_Name = "Roy"
Full_Name = First_Name + " " + Last_Name
print(Full_Name)

a = 2
b = 3
sum_value = ((a * b) + (b * a)) / 2
print(sum_value)
________________________________________
🔎 Explanation (Very Simple)
✅ print()
Purpose → Show output on screen
Like: showing CLI output in terminal.
Example:
print("show ip route")
________________________________________
✅ "\n"
New line
Networking Example:
print("interface vlan 10\n ip address 10.1.1.1 255.255.255.0")
________________________________________
✅ Variables
First_Name = "Jeet"
Variable = container storing value.
Networking thinking:
hostname = "dc-core-01"
________________________________________
✅ + (Concatenation)
Joins strings
Example:
site = "kol"
device = "rt"
print(device + "-" + site)
Output:
rt-kol
________________________________________
✅ Arithmetic Operators
Symbol	Meaning
+	Add
-	Subtract
*	Multiply
/	Divide
Networking Example:
vlans = 10
hosts_per_vlan = 254
total = vlans * hosts_per_vlan
________________________________________
🔹 Print Function for Networking
hostname = "rt-hec01-vpn-01a"
Vendor = "Cisco"
Model = "ASR1002HX"
Interface = "eth1/1"
IP_address = "192.168.10.1/24"

print("VPN device-", hostname)
print("device Type-", Vendor, Model)
print("Need to configure on", Interface, IP_address)
________________________________________
🔎 Explanation
This is basic automation formatting.
Better professional way:
print(f"Device {hostname} is {Vendor} {Model}")
✅ f-string
Modern formatting method
Used in automation everywhere.
________________________________________
🔹 Data Types
Device_name = "Switch-1"
num_port = 48
Device_temperature = 35.5
is_Active = True

AA_converting_data_type = "67"
BB_converting_data_type = int(AA_converting_data_type)

print(type(Device_name))
print(type(num_port))
print(type(Device_temperature))
print(type(is_Active))
print(BB_converting_data_type + 3)
________________________________________
🔎 Data Types (Very Important)
Type	Example	Networking Meaning
str	"Cisco"	hostname
int	4094	VLAN ID
float	35.5	Temp
bool	True	Interface up
________________________________________
✅ int()
Converts string → number
Why needed?
input() always returns string.
Without int:
"10" + 5  ❌ Error
With int:
int("10") + 5  ✅ 15
________________________________________
🔹 String Operator (Indexing & Slicing)
name = "JeetRoy"

str_1 = name[0]
str_2 = name[1]
str_3 = name[2]
str_4 = name[3]

print(str_1 + str_2 + str_3 + str_4)

char_1 = name[:1]
char_2 = name[:2]
char_3 = name[:3]
char_4 = name[:4]

print(char_1)
print(char_2)
print(char_3)
print(char_4)

char_a = name[-0:]
char_b = name[-1:]
char_c = name[-2:]

print(char_a)
print(char_b)
print(char_c)
________________________________________
🔎 Explanation
✅ Indexing
Python starts from 0
J e e t R o y
0 1 2 3 4 5 6
________________________________________
✅ Slicing
name[:2] → first 2 characters
Very useful in automation:
hostname = "rt-del-01"
print(hostname[-2:])
Output:
01
Used in:
•	Extract DC number
•	Extract site code
•	Extract router ID
________________________________________
🔹 len()
name = "JeetRoy"
name_length = len(name)
print(name_length)
print(len(name))
________________________________________
✅ len()
Counts length.
Networking example:
if len(hostname) > 15:
    print("Hostname too long")
________________________________________
🔹 Input Function
Vendor_name = input("Type the device Vendor- ")
Model_name = input("Type the Model- ")
print(Vendor_name + " " + Model_name)
________________________________________
✅ input()
Takes user input from keyboard.
Important:
Always returns string.
If number needed:
vlan = int(input("Enter VLAN: "))
________________________________________
🔹 IF & ELSE (VLAN Validation)
vlan_id = int(input("Enter the Vlan ID: "))

if vlan_id >= 1 and vlan_id <= 4094:
    print("vlan id is valid", vlan_id)
else:
    print("Vlan is not valid, please provide correct vlan-id")
________________________________________
🔎 Explanation
✅ if
Decision making
Networking example:
if interface_status == "up":
    print("Interface active")
else:
    print("Interface down")
________________________________________
🔹  IF / ELIF (Marks Section Fixed)
Marks = int(input("Enter the value="))

if Marks >= 90:
    print("Grade - A")
elif Marks >= 70:
    print("Grade - B")
else:
    print("Try to achieve more marks")
________________________________________
Important:
Python checks top to bottom.
________________________________________
🔹 List
Data_center_list = [1,2,3,4,5,6,7,8,9,10]

print(Data_center_list[0])
print(Data_center_list[1])
print(Data_center_list[7])
________________________________________
✅ List
Stores multiple values.
Networking Example:
devices = ["core1","core2","leaf1"]
________________________________________
🔹 🔟 IN Operator
DC = int(input("Enter the DC Number="))

if DC in Data_center_list:
    print("Match DC number", DC)
else:
    print("Error DC Number")
________________________________________
✅ in
Checks if value exists inside list.
Used in:
•	VLAN validation
•	Device validation
•	Allowed DC check
________________________________________
🔹 ipaddress Module (Very Important for You)
import ipaddress

Local_Network = input("Enter Local Network (e.g.,192.168.1.0/24) ").strip()

net = ipaddress.ip_network(Local_Network, strict=False)

DC_host = list(net.hosts())

print(DC_host[0])
print(DC_host[1])
print(DC_host[2])
________________________________________
🔎 Explanation
✅ ipaddress module
Used to:
•	Validate subnet
•	Get network address
•	Get broadcast
•	Get usable IPs
________________________________________
✅ hosts()
Removes:
•	Network address
•	Broadcast address
Very useful for:
•	SVI IP
•	HSRP IP
•	VARP IP
________________________________________
🔹 While Loop
count = 1

while count <= 3:
    print("Hello")
    count += 1
________________________________________
✅ while
Runs until condition becomes False.
Networking Example:
Retry login 3 times.
________________________________________
Infinite loop
while True:
    response = input("Enter exit to stop: ")
    if response == "exit":
        break
________________________________________
break
Stops loop immediately.
________________________________________
🔹 For Loop
devices = ["core1", "core2"]

for device in devices:
    print(device)
________________________________________
✅ for loop
Best for iterating list.
Always use for loop for:
•	Device list
•	VLAN list
•	IP list
________________________________________
🔹 Functions (VERY IMPORTANT)
def calc_sum(a, b):
    return a + b

sum_value = calc_sum(4,5)
print(sum_value)
________________________________________
✅ def
Creates reusable block.
Instead of writing same code 50 times.
Networking example:
def ping_device(ip):
    print(f"Pinging {ip}")
________________________________________
🔹 Tuple vs List
Tuple:
device = (10, "192.168.10.1")
Cannot change.
Used for:
•	Constant config values
List:
device = [10, "192.168.10.1"]
Can change.
________________________________________
🔹Dictionary (Very Important for Automation)
Correct version:
inventory_dict = {
    "192.168.0.1": {
        "name": "router1",
        "username": "admin",
        "password": "admin",
        "vendor": "cisco",
        "os": "ios"
    }
}
________________________________________
✅ Dictionary
Key → Value
Like:
IP → Device Details
Used in:
•	Netmiko
•	Ansible
•	Nornir
•	API





ভাই 🔥 এখন আমি পুরো automation flow network engineer mindset দিয়ে, একদম ভেতর থেকে বুঝিয়ে দিচ্ছি।
তুমি শুধু syntax না — architecture বুঝবে।
________________________________________
🧠 PART 1: YAML কী? কেন লাগে?
🔹 YAML মানে কী?
YAML = YAML Ain’t Markup Language
এটা একটা human-readable data format।
JSON এর মতো, কিন্তু বেশি readable।
________________________________________
🔹 Network Example দিয়ে বুঝি
ধরো তোমার কাছে ২০০টা device আছে।
তুমি কি এভাবে code-এ লিখবে?
host="192.168.1.1"
username="admin"
password="admin"
না।
কারণ:
•	Device বাড়বে
•	IP বদলাবে
•	Role বদলাবে
তাই আমরা device info আলাদা ফাইলে রাখি।
এইটাই YAML inventory।
________________________________________
🔵 তোমার YAML
devices:

  - name: HANA-SW
    role: access
    device_type: arista_eos
    host: 192.168.187.221
    username: admin
    password: admin
এখন structure বুঝি:
________________________________________
🔹 devices:
এটা একটা key।
মানে Python পড়লে data হবে:
data["devices"]
________________________________________
🔹 "-" মানে কী?
- মানে list item।
মানে devices হলো list।
Python এ গেলে এটা হবে:
[
   {device1 dictionary},
   {device2 dictionary}
]
________________________________________
🔹 প্রতিটা device আসলে কী?
Dictionary।
মানে:
{
   "name": "HANA-SW",
   "host": "192.168.187.221"
}
________________________________________
🧠 PART 2: তোমার Python Code পুরো explain
________________________________________
🔵 1️⃣ Import Section
import yaml
import os
from netmiko import ConnectHandler
from jinja2 import Environment, FileSystemLoader
🔹 yaml
YAML file পড়ার জন্য।
🔹 os
Path handle করার জন্য।
🔹 Netmiko
SSH login করে config push করার জন্য।
🔹 Jinja2
Template system।
________________________________________
🔵 2️⃣ Project Base Path
base_dir = os.path.dirname(os.path.dirname(__file__))
এটা কী করছে?
ধরো script আছে এখানে:
JEET_ROY/scripts/vlan_push.py
__file__ = এই file-এর path।
dirname দুইবার মানে:
scripts → JEET_ROY
মানে base project directory বের করছে।
Professional project এ hardcoded path ব্যবহার করা হয় না।
________________________________________
🔵 3️⃣ Inventory Path
inventory_path = os.path.join(base_dir, "inventory", "devices.yaml")
এটা বানাচ্ছে:
JEET_ROY/inventory/devices.yaml
________________________________________
🔵 4️⃣ Template Loader
env = Environment(loader=FileSystemLoader(os.path.join(base_dir, "templates")))
template = env.get_template("vlan_template.j2")
এটা কী করছে?
templates folder থেকে:
vlan_template.j2
লোড করছে।
________________________________________
🔵 5️⃣ YAML Load
with open(inventory_path) as file:
    data = yaml.safe_load(file)
এখন data হলো:
{
   "devices": [
      {...},
      {...}
   ]
}
________________________________________
🔵 6️⃣ Loop Through Devices
for device in data["devices"]:
এখন device হবে একেকটা dictionary।
যেমন:
device["host"]
device["username"]
________________________________________
🔵 7️⃣ SSH Connect
connection = ConnectHandler(...)
এটা internally করছে:
•	TCP connect
•	SSH handshake
•	Login
•	Enter enable (যদি লাগে)
________________________________________
🔵 8️⃣ Template Render
config = template.render(
    vlan_id=device["vlan_id"],
    vlan_name=device["vlan_name"]
)
ধরো template ছিল:
vlan {{ vlan_id }}
 name {{ vlan_name }}
Render করলে হবে:
vlan 100
 name SAP_VLAN
________________________________________
🔵 9️⃣ Config Push
output = connection.send_config_set(config.split("\n"))
split কেন?
কারণ send_config_set list নেয়:
["vlan 100", "name SAP_VLAN"]
________________________________________
🧠 Real Network Use Case
Use Case 1: ২০টা access switch-এ VLAN create
Without automation:
→ CLI login ২০ বার
With automation:
→ Script run ১ বার
________________________________________
Use Case 2: DC expansion
১০০টা new leaf add হলে:
Inventory update করো
Script run করো
Done.
________________________________________
Use Case 3: Standardization
সব device-এ একই naming convention enforce করা যায়।
________________________________________
🧠 Why YAML + Jinja + Netmiko?
Component	Purpose
YAML	Device database
Python	Logic engine
Jinja2	Config template
Netmiko	SSH transport
________________________________________
🧠 Enterprise Architecture View
Inventory (YAML)
        ↓
Template (Jinja2)
        ↓
Python Logic
        ↓
SSH Transport (Netmiko)
        ↓
Network Devices
________________________________________
🔵 এখন একটা উন্নত example দিচ্ছি
ধরো role based VLAN:
YAML:
vlan:
  id: 100
  name: USERS
Template:
{% if role == "access" %}
vlan {{ vlan_id }}
 name {{ vlan_name }}
{% endif %}
Automation এখন intelligent।
________________________________________
🧠 এখন YAML নিয়ে একটু গভীর কথা
YAML ৩টা structure support করে:
1️⃣ Key-Value
name: HANA-SW
2️⃣ List
- HANA
- STR
3️⃣ Nested
devices:
  - name: HANA
    mgmt:
      ip: 1.1.1.1
________________________________________
________________________________________
🧠 1️⃣ Concept First — Role Based Automation মানে কী?
ধরো তোমার কাছে:
•	২০টা Access Switch
•	১০টা Leaf
•	৪টা Spine
সব device-এ এক config হবে না।
Role	Example Config
Access	VLAN, Access Port
Leaf	VLAN + VTEP + EVPN
Spine	BGP Underlay only
👉 তাই inventory-তে আমরা role লিখি।
Automation script role দেখে template select করে।
এটাই role-based automation।
________________________________________
🏗 Final Project Structure (Professional)
JEET_ROY
│
├── inventory
│     └── devices.yaml
│
├── templates
│     ├── access_template.j2
│     └── leaf_template.j2
│
└── scripts
      └── role_push.py
________________________________________
🧾 2️⃣ Update inventory/devices.yaml
এখন role + VLAN info add করো।
devices:

  - name: HANA-SW
    role: access
    device_type: arista_eos
    host: 192.168.187.221
    username: admin
    password: admin
    vlan_id: 100
    vlan_name: USERS

  - name: STR-SW
    role: access
    device_type: arista_eos
    host: 192.168.187.222
    username: admin
    password: admin
    vlan_id: 200
    vlan_name: SERVERS

  - name: LEAF-1
    role: leaf
    device_type: arista_eos
    host: 192.168.187.211
    username: admin
    password: admin
    vlan_id: 300
    vlan_name: APP_VLAN
    loopback_ip: 10.255.1.11/32

  - name: LEAF-2
    role: leaf
    device_type: arista_eos
    host: 192.168.187.212
    username: admin
    password: admin
    vlan_id: 400
    vlan_name: DB_VLAN
    loopback_ip: 10.255.1.12/32
________________________________________
📄 3️⃣ access_template.j2
vlan {{ vlan_id }}
 name {{ vlan_name }}
Access switch শুধু VLAN create করবে।
________________________________________
📄 4️⃣ leaf_template.j2
vlan {{ vlan_id }}
 name {{ vlan_name }}

interface Loopback0
 ip address {{ loopback_ip }}
Leaf switch-এ VLAN + Loopback configure হবে।
________________________________________
🧠 5️⃣ Main Automation Script
scripts/role_push.py
import yaml
import os
from netmiko import ConnectHandler
from jinja2 import Environment, FileSystemLoader

# Base directory
base_dir = os.path.dirname(os.path.dirname(__file__))

# Inventory path
inventory_path = os.path.join(base_dir, "inventory", "devices.yaml")

# Template environment
env = Environment(loader=FileSystemLoader(os.path.join(base_dir, "templates")))

# Load inventory
with open(inventory_path) as file:
    data = yaml.safe_load(file)

# Loop devices
for device in data["devices"]:
    print(f"\nConnecting to {device['name']} ({device['role']})")

    try:
        connection = ConnectHandler(
            device_type=device["device_type"],
            host=device["host"],
            username=device["username"],
            password=device["password"],
        )

        # Select template based on role
        if device["role"] == "access":
            template = env.get_template("access_template.j2")
            config = template.render(
                vlan_id=device["vlan_id"],
                vlan_name=device["vlan_name"]
            )

        elif device["role"] == "leaf":
            template = env.get_template("leaf_template.j2")
            config = template.render(
                vlan_id=device["vlan_id"],
                vlan_name=device["vlan_name"],
                loopback_ip=device["loopback_ip"]
            )

        else:
            print("Unknown role")
            continue

        print("Generated Config:")
        print(config)

        # Push config
        output = connection.send_config_set(config.split("\n"))
        print(output)

        connection.disconnect()
        print("✅ Config pushed successfully")

    except Exception as e:
        print("❌ Failed")
        print(e)
________________________________________
🧠 কী হলো এখানে?
🔹 inventory driven
Device info YAML থেকে পড়ছে।
🔹 role detection
if device["role"] == "access"
🔹 dynamic template selection
Role অনুযায়ী template load করছে।
🔹 render
Jinja variable replace করছে।
🔹 config push
Netmiko দিয়ে CLI push করছে।
________________________________________
🔥 Execution
python scripts/role_push.py
________________________________________
🧠 Real DC Use Case
ধরো:
•	Access switches শুধু L2
•	Leaf switches L2 + VTEP + Loopback
•	Spine switches Underlay only
Role based automation ছাড়া impossible scale।
________________________________________
🧠 Enterprise Thinking
Later তুমি করতে পারো:
•	role: spine
•	role: border-leaf
•	role: wan-edge
Automation তখন automatically template select করবে।
________________________________________
🔥 Advanced Version (One Template Approach)
তুমি চাইলে একটাও template use করতে পারো:
{% if role == "access" %}
vlan {{ vlan_id }}
 name {{ vlan_name }}
{% endif %}

{% if role == "leaf" %}
vlan {{ vlan_id }}
 name {{ vlan_name }}

interface Loopback0
 ip address {{ loopback_ip }}
{% endif %}
________________________________________

ভাই 🔥 এখন আমরা network automation-এর core architecture বুঝবো।
তুমি already CLI expert — এখন automation mindset clear করবো।
আমি আগে concepts explain করবো, তারপর Underlay BGP + VXLAN EVPN automation দেখাবো।
________________________________________
🧠 PART 1: Important Concepts (Clear Foundation)
________________________________________
🔵 1️⃣ GitHub কী?
সহজ ভাষায়:
GitHub হলো code storage + version control + collaboration platform।
Network analogy:
GitHub = Config repository server
যেমন:
TFTP / FTP server → config backup রাখো
GitHub → automation script + template + inventory রাখো
________________________________________
কেন দরকার?
✔ Backup
✔ Change tracking
✔ Version control
✔ Team collaboration
✔ CI/CD integration
________________________________________
🔵 2️⃣ CI/CD কী?
CI = Continuous Integration
CD = Continuous Deployment
সহজ উদাহরণ:
তুমি GitHub-এ new VLAN template push করলে:
CI/CD automatically:
1️⃣ Test করবে script
2️⃣ Validate করবে YAML
3️⃣ Config generate করবে
4️⃣ Deploy করবে lab device-এ
Enterprise automation-এ এটা খুব common।
________________________________________
🔵 3️⃣ Jinja2 কী?
Jinja2 হলো template engine।
Network example:
Static config:
router bgp 65001
 neighbor 1.1.1.1 remote-as 65002
Dynamic template:
router bgp {{ local_as }}
 neighbor {{ neighbor_ip }} remote-as {{ remote_as }}
Python variable inject করবে।
________________________________________
🔵 4️⃣ Render মানে কী?
Render = template-এ variable replace করা।
Example:
Template:
vlan {{ vlan_id }}
Render করলে:
vlan 100
মানে:
Template + Data → Final Config
________________________________________
🔵 5️⃣ YAML কী আবার পরিষ্কারভাবে
YAML হলো structured data format।
Example:
devices:
  - name: LEAF1
    asn: 65001
    loopback: 1.1.1.1
Python পড়লে:
data["devices"][0]["asn"]
________________________________________
🧠 PART 2: Underlay BGP Automation Concept
________________________________________
DC Underlay Design
Typical Arista EVPN Fabric:
•	Leaf-1 AS 65101
•	Leaf-2 AS 65102
•	Spine-1 AS 65000
•	Spine-2 AS 65000
Leaf → eBGP to Spine
Spine → eBGP to Leaf
________________________________________
Inventory Example
devices:

  - name: LEAF1
    role: leaf
    device_type: arista_eos
    host: 192.168.187.211
    username: admin
    password: admin
    asn: 65101
    loopback: 1.1.1.1
    neighbors:
      - ip: 10.0.0.1
        remote_as: 65000
      - ip: 10.0.0.2
        remote_as: 65000

  - name: SPINE1
    role: spine
    device_type: arista_eos
    host: 192.168.187.101
    username: admin
    password: admin
    asn: 65000
    neighbors:
      - ip: 10.0.0.11
        remote_as: 65101
________________________________________
🧠 BGP Template (underlay_bgp.j2)
router bgp {{ asn }}
 router-id {{ loopback }}

{% for neighbor in neighbors %}
 neighbor {{ neighbor.ip }} remote-as {{ neighbor.remote_as }}
{% endfor %}
________________________________________
🧠 Python Logic
template = env.get_template("underlay_bgp.j2")

config = template.render(
    asn=device["asn"],
    loopback=device.get("loopback", ""),
    neighbors=device["neighbors"]
)
________________________________________
🔵 Render মানে এখানে কী হলো?
Template:
neighbor {{ neighbor.ip }}
Data:
10.0.0.1
Final config:
neighbor 10.0.0.1 remote-as 65000
________________________________________
🧠 PART 3: VXLAN EVPN Auto Generate Concept
________________________________________
Typical VXLAN Elements
•	VTEP Loopback
•	VLAN → VNI mapping
•	NVE interface
•	BGP EVPN
________________________________________
YAML Example
vlans:
  - id: 10
    vni: 10010
  - id: 20
    vni: 10020
________________________________________
VXLAN Template (vxlan_evpn.j2)
interface Loopback1
 ip address {{ vtep_ip }}

interface Vxlan1
 vxlan source-interface Loopback1

{% for vlan in vlans %}
 vlan {{ vlan.id }}
  vxlan vlan {{ vlan.id }} vni {{ vlan.vni }}
{% endfor %}
________________________________________
🔵 Final Output Render Example
vlan 10
 vxlan vlan 10 vni 10010
________________________________________
🧠 Why Automation Needed Here?
Without automation:
•	200 VLAN
•	200 VNI mapping
•	Manual error risk
With automation:
•	YAML update
•	Script run
•	Fabric updated
________________________________________
🧠 Architecture View
YAML Inventory
      ↓
Jinja Template
      ↓
Python Render
      ↓
Netmiko Push
      ↓
Fabric Ready
________________________________________
🔥 Real Enterprise Benefit
✔ No copy paste error
✔ Scalable
✔ Repeatable
✔ Standardized
✔ Fast deployment
________________________________________
🧠 Summary (Important Terms)
Term	Meaning
YAML	Device data storage
Jinja2	Config template engine
Render	Template → Final config
GitHub	Code repository
CI/CD	Auto test + auto deploy
Netmiko	SSH automation
________________________________________
🔥 এখন আমরা VRF-Aware IPsec Route-Based VPN (Cisco CSR1000v – WAN-1 / WAN-2) automation-friendly template বানাবো।
তুমি already IPsec/IKEv2 জানো — তাই আমি design + template + YAML structure + Jinja2 render logic সব explain করছি।
________________________________________
🧠 1️⃣ Concept Clear করি (VRF Aware Route Based VPN)
🎯 Goal
•	VRF: CUSTOMER_0001
•	WAN-1 & WAN-2 → Dual CPE
•	Route-based (Tunnel Interface based)
•	IKEv2
•	IPsec Profile attach to Tunnel
•	Static or BGP inside VRF
________________________________________
🧠 2️⃣ Architecture Overview
VRF CUSTOMER_0001
        │
   Tunnel10 (WAN-1)
        │
   Tunnel20 (WAN-2)
        │
IPSEC Profile (IKEv2)
________________________________________
🧠 3️⃣ Required Components (Cisco CSR1000v)
Component	Purpose
VRF definition	Tenant isolation
IKEv2 proposal	Encryption
IKEv2 policy	Match proposal
IKEv2 keyring	PSK
IKEv2 profile	Tie peer + VRF
IPsec transform-set	ESP encryption
IPsec profile	Attach to Tunnel
Tunnel Interface	Route-based VPN
________________________________________
🧾 4️⃣ YAML Structure (Inventory Driven)
devices:

  - name: WAN-1
    device_type: cisco_ios
    host: 192.168.187.140
    username: admin
    password: cisco

    vrf_name: CUSTOMER_0001
    local_wan_ip: 3.3.3.3
    peer_ip: 4.4.4.4
    tunnel_ip_local: 10.10.10.1
    tunnel_ip_peer: 10.10.10.2
    pre_shared_key: MySecret123

  - name: WAN-2
    device_type: cisco_ios
    host: 192.168.187.141
    username: admin
    password: cisco

    vrf_name: CUSTOMER_0001
    local_wan_ip: 4.4.4.4
    peer_ip: 3.3.3.3
    tunnel_ip_local: 10.10.10.2
    tunnel_ip_peer: 10.10.10.1
    pre_shared_key: MySecret123
________________________________________
🧾 5️⃣ Jinja2 Template (vrf_ipsec.j2)
! ========================
! VRF Definition
! ========================

vrf definition {{ vrf_name }}
 rd 65000:1
 address-family ipv4
 exit-address-family

! ========================
! IKEv2 Proposal
! ========================

crypto ikev2 proposal IKEV2_PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 14

crypto ikev2 policy IKEV2_POLICY
 proposal IKEV2_PROPOSAL

! ========================
! IKEv2 Keyring
! ========================

crypto ikev2 keyring IKEV2_KEYRING
 peer PEER1
  address {{ peer_ip }}
  pre-shared-key {{ pre_shared_key }}

! ========================
! IKEv2 Profile
! ========================

crypto ikev2 profile IKEV2_PROFILE
 match identity remote address {{ peer_ip }} 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local IKEV2_KEYRING

! ========================
! IPSEC Transform
! ========================

crypto ipsec transform-set IPSEC_TS esp-aes 256 esp-sha256-hmac
 mode tunnel

crypto ipsec profile IPSEC_PROFILE
 set transform-set IPSEC_TS
 set ikev2-profile IKEV2_PROFILE

! ========================
! Tunnel Interface
! ========================

interface Tunnel10
 vrf forwarding {{ vrf_name }}
 ip address {{ tunnel_ip_local }} 255.255.255.252
 tunnel source {{ local_wan_ip }}
 tunnel destination {{ peer_ip }}
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC_PROFILE

! ========================
! Static Route inside VRF
! ========================

ip route vrf {{ vrf_name }} 0.0.0.0 0.0.0.0 {{ tunnel_ip_peer }}
________________________________________
🧠 6️⃣ Render মানে কী এখানে?
Template-এ আছে:
{{ vrf_name }}
Python data inject করলে:
CUSTOMER_0001
Final config generated হবে automatically।
________________________________________
🧠 7️⃣ Python Render Logic
from jinja2 import Environment, FileSystemLoader
from netmiko import ConnectHandler
import yaml
import os

base_dir = os.path.dirname(os.path.dirname(__file__))

env = Environment(loader=FileSystemLoader(os.path.join(base_dir, "templates")))
template = env.get_template("vrf_ipsec.j2")

with open("inventory/devices.yaml") as file:
    data = yaml.safe_load(file)

for device in data["devices"]:
    connection = ConnectHandler(
        device_type=device["device_type"],
        host=device["host"],
        username=device["username"],
        password=device["password"],
    )

    config = template.render(device)

    print(config)  # safe preview

    connection.send_config_set(config.split("\n"))
    connection.disconnect()
________________________________________
🧠 8️⃣ Enterprise Use Case
✔ Multi-customer VPN provisioning
✔ Dual WAN redundancy
✔ VRF isolation
✔ Repeatable deployment
✔ Error-free tunnel creation
________________________________________
🧠 9️⃣ Advanced Version (BGP inside VRF)
Template add করতে পারো:
router bgp 65000
 address-family ipv4 vrf {{ vrf_name }}
  neighbor {{ tunnel_ip_peer }} remote-as 65001
 exit-address-family
________________________________________
🧠 10️⃣ Why Route-Based Better?
Policy-Based	Route-Based
Crypto map	Tunnel interface
Complex ACL	Clean routing
Hard scale	Easy scale
DC / Enterprise always route-based।
________________________________________
🚀 Next Advanced Option
আমি চাইলে এখন build করতে পারি:
1️⃣ Dual WAN failover automation
2️⃣ IP SLA tracking
3️⃣ BGP inside tunnel
4️⃣ Multiple customer VRF provisioning automation
5️⃣ Full SD-WAN style automation
বল কোনটা deep dive করবো 🔥

________________________________________


```

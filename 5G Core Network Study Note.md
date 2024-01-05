# 5G Core

**Definition**

5G core is a network consisted of several software that act as a controller for RAN and network devices. 5G core using virtualized approach to increase flexibility, decrease exclusivity to provide a versatile network system that support different usecases.


**Terminology**
UE : User equipment
Smartphone, Laptop, IoT device that request and receive information through 5G network.

RAN : Radio Access Network
Collection of software and hardware that perform layer 1 and layer 2 operation such as frame encapsulation, header compression, encoding, modulation, etc.

DN : Data Network
Refers to network or cloud system where most of the accessible data are stored


**5G Core Architecture**

* Service Based Architecture
![image](https://hackmd.io/_uploads/SkebQwQda.png)

Service Based Architecture allows every network function in 5G core network to interract with other fucntion through a Service Bus Interface even though the component come from various suppliers. The diagram also depicting separation between control plane and data plane.



**Core Network Function**
1. Acess and Mobility Function (AMF)

    Handling the registration and termination of UE to the 5G network, managing the mechanism of RAN node handover to handle UE mobility, ensuring the status and connectivity of UE.
    
    
3. User Plane Function (UPF)

    Applying Quality of Service by controlling network resource allocation, deciding actions for packet such as drop or forward to some interface based on packet header and defined policy, determine path taken by traffic, perform traffic data collection for further analysis
    
5. Session Management Function (SMF)

    Manage logical connection between UE and specific exit point to DN, manage UE IP address allocation, receive network policy from PCF to be applied by UPF, controlling UPF's
          
7. Policy Control Function (PCF)

    Provide a unified framework to simplify network orchestration, interract with AF to retrieve information about user and application requirements
   
9. Authentication Server Function (AUSF)
    
    allows the AMF to authenticate the UE and access services of the 5G core, including generate authentication key and interract with UDM to retrieve user subscription data, managing user security context.
    
11. Network Slice Selection Function (NSSF)
    
    store the information and monitoring each of network slice. AMF will call NSSF to determine particular network slice to serve specific device. Every network slice is optimized for specific usecase. 
    
13. Network Exposure Function (NEF)
    
    securely exposes 3GPP network function and capability, enable external application to provide data to the 3GPP network securely

15. Network Repository Function (NRF)
    
    act as database about network function in 5G core network. NRF store information such as IP address, registry status of available network function
    
17. Unified Data Management (UDM)

    Store user subscription data. Subscription data serves as a record of internet usage to calculate the user's subscription bill.
 
 18. Application Function
 
     Act as source of network performance information for application accessed by UE. 
    

**5G Core Interfaces**

![image](https://hackmd.io/_uploads/BJvS6QEup.png)

![image](https://hackmd.io/_uploads/HkSCyNEup.png)
![image](https://hackmd.io/_uploads/Hyo4l4E_T.png)


1. N1 : refers to interface between UE and AMF

    N1 interface are used during initiation between UE and 5G network. AMF will keep track the UE reachability using N1 interface. 
    
3. N2 : refers to interface between AMF and RAN (Radio Access Network)

    N2 interface are used by AMF to communicate with RAN. AMF will tell gNB about registered UE, UE mobility and connection management using NGAP protocol
    
5. N3 : refers to interface between RAN and UPF
    
    N3 interface serve as a channel that transmit information between RAN and UPF. Most of the requested user data will be transmitted through the N3 interface
    
7. N4 : refers to interface between UPF and SMF

    N4 interface serve as a control line between SMF and UPF. SMF will transmitting packet forwarding policies to UPF through N4 interface
    
11. N6 : refers to interface between UPF and Data Network (DN)

    N6 interface serve as gateway between 5G network and data network (DN) where most of the data and service hosted
    
13. N7 : refers to interface between PCF and SMF

    SMF requesting network policy to be applied by UPF through N7 interface
    
15. N8 : refers to interface between AMF and UDM

    AMF requesting user subsrciption information through N8 interface during UE registration
    
19. N11 : refers to interface between AMF and SMF
    
    SMF will receive data from AMF for PDU sessions generation, network resource allocation for requested usecase and application
21. N12 : refers to interface between AMF and AUSF

    AMF will forward UE authentication request through N12 interface
    
23. N13 : refers to interface between AUSF and UDM

    AUSF will query UDM about UE subscription data to generate authentication key

26. N15 : refers to interface between AMF and PCF

    PCF will provide information about access and mobility policies for UE registered at AMF





    



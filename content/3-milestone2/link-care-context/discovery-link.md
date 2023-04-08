+++
title = "Discovery & link"
date = 2023-04-03T04:10:00+05:30
weight = 3
chapter = true
pre = "<b>3.3.3 </b>"
+++

# Discovery & link

## Discovery of the patient’s information at the HIP

- When the gateway calls an HIP system and requests for a particular patient’s records with a set of verifiable Ids, the process of information discovery begins. 

- Upon receipt of the request, the HIP health repository reverts with a set of care context labels (in masked form). 

The following flow diagram details the flows that take place during patient information discovery from the HIP perspective:

{{< mermaid >}}
%%{init:{"fontSize": "1.0rem", sequence":{"showSequenceNumbers":true}}}%%
sequenceDiagram
title Discovery of patient's information at the HIP
Gateway->>Repository:POST/care-contexts/discover
activate Repository
Repository-->>HIP System:Discovery Request
HIP System-->>Repository:Response
deactivate Repository
Repository->>Gateway:POST/care-contexts/on-discover
{{< /mermaid >}}

The discovery process occurs when a consent manager sends out a request to the concerned HIP to identify the patient in the HIP system.

1. As part of the discovery request, the HIP will receive a list of verified identifiers along with name and demographic information, for example, the patient's mobile number, name, gender, year of birth and patient Id.
2. The matching logic is as depicted in the flowchart below and also in API specifications:
Note: Look for APIs with tag discovery and link

![Discovery Linking Flowchart](/abdm-docs/img/DiscoveryLinking.png)

3. As response to the discovery request, the HIP must send several masked details, as follows:
	- Patient reference: MHCXX111 - typically the identifier for the patient at the HIP.
Display: A description of the patient, comprising of the following information:
		- Name: John Doe
		- Email id: johndoe@example.com
		- Minor: Mother's name is Jane Doe
	Refrain from providing any critical information in the display area.
	- For the patient, you need to send one or more care-context detail, as follows:
		- Care context reference: ABXXX222
		- Display: General diagnosis
	Provide only a general hint of the patient's care context here, staying away from private diagnostic details. Here's an example:
		- TB program: Use your Nikshay program ID
		- PMJAY program: This context is linked with your PMJAY ID - PXXX239
		- Your last visit was with Dr. Shekhar on 21/03/2020
		- This is a child's immunization program

![Link Record Step 3](/abdm-docs/img/linkrecord-Step3.png)

4. Hospitals group patient data in the form of care context. Think of care-context as information about an association of a patient with an HIP for a period of time under which related healthcare activities may occur. Grouping of the care context can be based on the following:
	- Program-based: A patient visiting a hospital can sign up for any program, and belong to multiple programs as well. This kind of grouping is done generally in hospitals which deal with care programs for multiple ailments and specialities such as the general checkup service, cancer program, maternity care and so on.
	- Visit-based: Patient record can be tagged as inpatient, outpatient, or emergency, based on the purpose of the patient's visit.
	- Episode of Care: Some hospitals are capable of grouping patient records by the specific episode of care
	- Department-based: Patient records can be tagged by department as well, especially in large general hospitals.



## Verify the patient and link the carecontext as requested by the patient

- This flow begins once a patient initiates a link request to the HIP to link the care context to the patient’s Consent Manager’s User ID.

- To enable the linking, the HIP system returns a link reference number along with the authentication type and its associated parameters.

- The HIP system sends an OTP to the patient’s phone number. 
Note, the phone number for OTP communication from HIP may be the same as verified by the CM or maybe a different number that the patient has chosen as preferred mode of communication with HIP - meaning it's up to the HIP to choose the phone number it sends OTP to. 

- The patient, via patient app, submits the OTP received from the HIP system within the stipulated time. If the patient is successfully authenticated by the HIP, the linking is now complete. 

The following flow diagrams details the flows that take place while linking to a health repository representing an HIP:

{{< mermaid >}}
%%{init:{"fontSize": "1.0rem", sequence":{"showSequenceNumbers":true}}}%%
sequenceDiagram
title Linking (HIP side)
actor User
Gateway->>Repository:POST/v0.5/links/link/init
activate Repository
Repository-->>HIP System:Link init Request
HIP System-->>Repository: Response
Repository->>Gateway:POST/v0.5/links/link/on-init
HIP System-->>User: Send token out-of-band
Gateway->>Repository: POST/v0.5/links/link/confirm
Repository-->>HIP System:Link Confirm Request
HIP System-->>Repository: Response
Repository->>Gateway:POST/v0.5/links/link/on-confirm
deactivate Repository
{{< /mermaid >}}
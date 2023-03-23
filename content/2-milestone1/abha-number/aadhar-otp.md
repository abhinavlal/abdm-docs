+++
title = "Using Aadhaar OTP"
date = 2023-03-16T09:30:25+05:30
weight = 1
chapter = true
pre = "<b>2.1.1 </b>"
+++

# Registration via Aadhaar OTP

## Functionality Overview 

ABHA Number can be obtained via self-registration using Aadhaar as KYC with valid user consent where user is giving his consent to create ABHA number. **ABHA enrolment using Aadhaar has strong KYC validation.** Once user is successfully authenticated, the system will generate 14 digit ABHA number

Following are the steps to successfully integrate the ABHA registration via Aadhaar:

1. The User should input the Aadhaar number as an input.
2. To enable beneficiary registration using Aadhaar, an integrator needs to generate an OTP and send the same on the linked mobile number.
3. For the OTP verification process there is a primary mobile number which user wants to link with ABHA number.
4. Once the OTP is verified, system should returns the complete profile data along with 14 digit ABHA (Health ID) Number to the user.
5. if the OTP is verified:
    - System checks whether the primary mobile number is matching with Aadhaar linked mobile number. If the aadhaar number matches then it will automatically link with ABHA number.
    - If the primary mobile number is not matching with Aadhaar linked mobile, user must authenticate the mobile number via OTP authentication

**Note:** Mobile Number can be linked/verify with ABHA via using API  

**Sample User experience:**

![sample user experienece](/abdm-docs/img/aadhaar-otp.gif)


## API Sequence 

The sequence of APIs used via this method are shown in the diagram below:

{{< mermaid >}}
%%{init:{"fontSize": "1.0rem", "sequence":{"showSequenceNumbers":true}}}%%
sequenceDiagram
title ABHA Creation using Aadhar OTP
actor HIU/HIP/PHR
Note left of ABHA: Share encrypted Aadhaar number
HIU/HIP/PHR->>ABHA: (POST: api/v3/ABHA/request/otp)
ABHA->>UIDAI: Aadhaar number
UIDAI->>UIDAI:Verify Aadhaar number
UIDAI->>ABHA: Response 200
ABHA->>HIU/HIP/PHR: Response 200 (transaction ID)
UIDAI->>HIU/HIP/PHR:Receive OTP
Note right of HIU/HIP/PHR: Forward OTP & transaction ID to verify
HIU/HIP/PHR->>ABHA: (POST:api/v3/enrollment/enrol/byAadhaar)
ABHA->>UIDAI: Forward OTP
UIDAI->>UIDAI: Verify OTP
UIDAI->>ABHA: Share Aadhaar e-KYC details
ABHA->>ABHA: ABHA Creation
ABHA->>HIU/HIP/PHR: ABHA Number & Profile
{{< /mermaid >}}


## ABHA OTP Test Cases:

Applicable | Test Title | Test Summary | Optional or Mandatory | Test Scenario | API Sequence | Expected Result | Actual Result
| ------| ----------- | ----------- | ----- | -------------- | ----------- | ------------- | -------------- |
CRT_ABHA_02 - HRPs / HIPs|ABHA creation - Assisted |Hospital user will create ABHA using patient Aadhaar based mobile OTP|Optional|EMR/HMIS system will take the Aadhaar number, OTP and consent of the patient to create ABHA.|v1/registration/aadhaar/generateOtp, v1/registration/aadhaar/verifyOTP, v1/registration/aadhaar/generateMobileOTP, v1/registration/aadhaar/verifyMobileOTP, v1/registration/aadhaar/createHealthIdbyAadhar, v1/search/existsByHealthId"|Successful creation of ABHA |ABHA generated

## API Information Request Response 

**1. [Create Gateway Session Token](/abdm-docs/1-basics/verify_sandbox_access/#create-gateway-session-token)**


Bearer token is received as part of respose and should be passed a Authorization token for subsequent API calls.

**URL:** https://dev.ndhm.gov.in/gateway/v0.5/sessions


**2. Generate Aadhaar OTP on registrered mobile number**

Api accepts Aadhar Card Number and then Generates OTP for Registered Mobile Number

**URL:** https://abhasbx.abdm.gov.in/abha/api/v3/enrollment/request/otp

{{< swaggermin src="/abdm-docs/Yaml/abha_enrollment_api.yml" api="POST /enrollment/request/otp$" >}}



**3. Create ABHA Number using pre-verified Aadhaar & Mobile.**

API creates ABHA Number using Aadhaar & Mobile which are already Registered.

**URL:** https://abhasbx.abdm.gov.in/abha/api/v3/enrollment/enrol/byAadhaar

{{< swaggermin src="/abdm-docs/Yaml/abha_enrollment_api.yml" api="POST /enrollment/enrol/byAadhaar$" >}}


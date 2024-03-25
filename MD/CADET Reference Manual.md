# CADET Reference Manual <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [Introduction](#introduction)
- [User Identifier](#user-identifier)
- [Proof of Attestation (POA)](#proof-of-attestation-poa)
   - [The Two Types of POA](#the-two-types-of-poa)
      - [Digital Signature (SIG)](#digital-signature-sig)
      - [Delegated Digital Signature (DDS)](#delegated-digital-signature-dds)
         - [DDS Record](#dds-record)
            - [The SimplePresentation of a DDS](#the-simplepresentation-of-a-dds)
- [Claim Template (CLT)](#claim-template-clt)
   - [Examples of CLT:](#examples-of-clt)
      - [Example 1](#example-1)
      - [Example 2](#example-2)
- [Claim (CL):](#claim-cl)
- [Attested Claim (ACL)](#attested-claim-acl)
- [Claim Verification Form (CVF)](#claim-verification-form-cvf)
   - [Claim Verification Form Prototype (CVFP)](#claim-verification-form-prototype-cvfp)
- [Attestation (ATT)](#attestation-att)
- [ATT Package (ATTP):](#att-package-attp)
- [Examples](#examples)
   - [Example 1:](#example-1-1)
   - [Example 2](#example-2-1)
   - [Example 4](#example-4)
   - [Example 5](#example-5)
   - [Example 6](#example-6)
- [Creation and Verification of Attestations](#creation-and-verification-of-attestations)
   - [Creating Attestations](#creating-attestations)
- [Authentication of ATT](#authentication-of-att)
   - [Authenticating SATT](#authenticating-satt)
   - [Authenticating DATT](#authenticating-datt)
   - [Authenticating CATT](#authenticating-catt)
      - [Authetnticating a "circular conditional authorization"](#authetnticating-a-circular-conditional-authorization)
- [Validation of ATTP](#validation-of-attp)
---

> **Note**
>
>  - For any element in this document, `element.attribute` refers to the `element`'s attribute, while `element.method()` refers to calling its method.
>     - Attributes and methods can be public or private.
> 
>  - **SimplePresentation** of an element is a simple representation of that element in documents, books, or webpages.

- Related documents:
   - STASH Reference Manual
   - Legal Framework

## Introduction

CADET stands for _Cryptographic Attestation Documentation and Evidence Technology_. It defines the basic elements and structure used by Ahket Inc to define and interact with attestations.

Elements of CADET will be described and explained in the following sections.

## User Identifier

- A "user" is an entity, which can be an organization or a person, that uses the Ahket.
- A user must have a cryptographic public-private key pair (IDS and SK):
   - Identity Seal (IDS):
      - The public key.
      - This is the user's identifier.
   - Secret Key (SK).
      - The private key.

- The key pair is used for cryptographic operations, such as digital signature.

## Proof of Attestation (POA)

**Definition**:\
POA is an incontrovertible evidence that an entity ("signer")---identified by an IDS, has made a formal attestation to a "claim".

**Description**:
- POA serves as a compelling evidence that a signer has committed to a claim. 
   - An example for POA is the digital signature of a message that affirms the message's authenticity.
- Any entity can "verify" that a specific signer attested a claim using a POA. 
- POA might require a fee payment to do the verification.

**Attributes**:
- `Type`: The type of the POA. There are two types: `SIG` and `DDS`.
- `ID`: A unique alpha-numeral identifier associated with each instance of a POA.
- `IDS`: The public key of the signer that created the POA.
   - "IDS" and "signer" can be used interchangeably.

**Public Methods**:
- `getVerificationFee()`:\
   Returns the `VerificationFee` value required for verification.

**Creation**:
- `CreatePOA(POAType, SK, claim)`:\
  Creates and returns a POA, where `SK` is the private key of `IDS`. Its exact implementation depends on `POAType`.

### The Two Types of POA

There are two types of POA used in CADET: SIG and DDS.

#### Digital Signature (SIG)

**Description**:\
This is the commonly known digital signature created by a public-private key pair. A SIG does not require any fees for verification.

**Creation**:
- `CreatePOA(SIG, SK, claim)`:\
   The function creates an instant of PAO whose `Type` is `SIG` and `ID` is the result of the common digital signing function `sign(date, IDS)`. 
- `CreateSIG(SK, claim)`:\
   This function is the same as `CreatePOA(POAType, SK, claim)`.

**Methods**:\
The method `verify(claim,time)` returns the result of the commonly known digital signature verification `verify(claim,POA.IDS,POA.ID)`, regardless of value of the `time` parameter.

**Notation**:\
A signature created for a specific `claim` and signer `IDS` can be noted as `SIG[claim,IDS]`. This means that this signature is created by the `SK` associated with that `IDS`. 
Given that, the common digital signature verification function `verify(claim,IDS,SIG[claim,IDS])` is always `True`.

**SimplePresentation**: 
Is a concatenation of `SIG` and `ID` value. E.g., `SIG1234AB56`.

#### Delegated Digital Signature (DDS)

**Description**:
- A DDS is a representation of a SIG stored on STASH. It is used to _verify_ that there is such SIG from the signer and that the signer authorized to answer a verification request. 
- A DDS is a generalization of SIG with more flexibility due to its *unique properties*:
   - **Transferrable Ownership**: The DDS owner has the right to transfer the ownership to another entity.
   - **Revenue Generating**: It can generate revenue to its owner from verification fees.
   - **Amendable**: The result of a verification can be amended, but not retrospectively.
   - **Provisory**: The result of verification can be limited by some constrain.
   - **Traceable**: The creation, changes, transfers, verifications are recorded on STASH (see STASH Reference Manual) and can be traced.

**Creation**:
- `CreatePOA(DDS, SK, claim)`\
   This function creates through STASH a `DDS Record` associated with the `IDS` and `claim`---where the `IDS` is the public key associated with the `SK`. The STASH thus creates a POA whose `Type` is `DDS` and `ID` is a unique number representing the pair `(IDS,claim)`; i.e., for each new pair of `IDS` and `claim`, a new number is generated for the ID. Only STASH and the signer can relate DDS.ID to the `claim`. For example, it could be just a sequential number for each new `(IDS,claim)`.
- `CreateDDS(SK, claim)` is a shorter way to write `CreatePOA(DDS, SK, claim)`.

> **Note**
> 
> NO ONE can change the claim of a DDS once created.

##### DDS Record
- STASH is required to create, update and verify a DDS.
- A DDS references a DDS Record stored in STASH. 
- The DDS Record has the following private and public properties.
   - **Private DDS Information**: This information is held with the HIGHEST SECURITY in STASH.
      - The `claim` hash. STASH does not save the claim itself.
      - The SIG associated with the `claim` hash.

   - **Public DDS Information**:
      - `AuthorizationLog`: Is a sequence of events representing changes to the DDS authorization through creation or update requests to STASH. Each event is recorded with the time of change and a SIG issued by the signer (IDS) authenticating the change request. 
         - The change of the authorization could be to one of the following:
            - `Authorized`, which means that the DDS can be used for verification. When a DDS is created for the first time, its authorization is `Authorized`.
               - `Authorized` is a signed attestation by the signer at a specific time that "the signer is authorizing STASH to verify through DDS."
            - `Unauthorized`, which means that the DDS cannot be used for verification.
               - `Unauthorized` is a signed attestation by the signer at a specific time that "the signer is deauthorizing STASH to verify through DDS."
            - `ConditionalAuthorization`, which means that the DDS can be used for verification if some conditions are satisfied. The condition is described using a special Claim Verification Form Prototype (see CVFP below). In this case, the DDS is a "conditional DDS" (CDDS).
               - `ConditionalAuthorization` is a signed attestation by the signer at a specific time that "the signer is authorizing STASH to verify through DDS provided that certain condition is satisfied."
         - Because of events are recorded at the time of the signer request, the DDS is strongly timestamped by STASH.
  
      - `Owner`: An entity that earns a portion of the fees paid during verification to STASH. We call the owner of DDS the `DDS owner` or `DDSO`.
      
      - `OwnershipTransferrable`: Is a boolean value. If `True` and the owner is NOT the signer of the DDS, then the current owner can transfer the DDS to a new owner; i.e., change `Owner` to the new owner's IDS. It can only be changed by the signer.
      
      - `Holder`: An entity that has some rights. We call the holder of DDS the `DDS Holder` or `DDSH`.
      
      - `HoldTransferrable`: Is a boolean value. If `True` and the holder is NOT the signer of the DDS, then the current holder can transfer the DDS to a new holder; i.e., change `Holder` to the new holder's IDS. It can only be changed by the signer.
  
      - `VerificationFee`: The fee required for verification. See STASH.
      
      - `DDSP`: DDS Price. See STASH.
      
      - `UnprotectedRevenue`: Is a boolean value. See STASH. 
   
   - When a DDS Record is created for the first time, the `Owner` and `Holder` are `IDS`.
   
   - The DDS Record in STASH is what gives its associated DDS its _unique properties_.

**Limited-access DDS Methods**:
- `ChangeAuthorization(newState)`:\
  It uses STASH to add an event to `AuthorizationLog` at the event time and change the DDS authorization. The change of the DDS authorization applies from the event time forward. ONLY the DDS signer can call this method.
  
- `SetDDSP(value)`:\
   It sets the value of `DDSP`. Only the DDS owner can use this, if allowed by STASH.

**Public DDS Methods**:
- `getState(targetTime)`:\
  It returns the authorization state of a DDS at `targetTime`.
  It returns the authorization state after the latest change in `AuthorizationLog` on or before `targetTime`, or return `unauthorized` if the `targetTime` is before the creation of the DDS.

- `isConditional(targetTime)`:\
   It returns the boolean value of `True` only if `getState(targetTime)` is `ConditionalAuthorization`, otherwise it retuens `False`.

- `getFee()`:\
   It returns the value of `VerificationFee`.

###### The SimplePresentation of a DDS
It is the concatenation of `DDS` and `ID` number. E.g., `DDS01A9F943` for `ID=01A9F943`.

In case of a conditional DDS, the SimplePresentation of the condition (see CVFP below) is added at the end.
   - E.g., `DDS1034EB57 ?CVF(\Driver License of {name:tbd_VERIFIER} who lives in {address:tbd_IGNORE}\MD_DMV/, tbd_VTIME)`.

## Claim Template (CLT)

**Description**:\
CLT is a JSON schema describing the blueprint of a claim. It could be considered as an "empty form".

**Components**:
- `variables` that describe the variables of the template.

- `SimplePresentation`: Is a "string" that provides concise textual representation of the variables, each represented as `{variable name : variable type}`. It is used when demonstrating or educating about CADET structures.
   - Example: "Driver License of {name:text} who lives in {address:text}"
   - Example: "File Attachment {passport: attachment}"
   - Example: In case a variable that is `CL`, `ACL` or `ATT` (see below), "Attested Balance is {account balance: ACL}"

- `ReaderFriendlyFormat`: Is a reformatting of the SimplePresentation of an object using indentations and line breaks to make it easier to read. It is used when demonstrating or educating about CADET structures.

- `FormalText`: Is the formal layout to present a thorough textual representation. It ALWAYS includes a boilerplate clause that is appropriate for the type of document being templates. The boilerplate clause should reflect the legal and formal aspects relevant to the specific type of document. It is used as the primary presentation to the public.
   - Example: "Driver License \n Name: {name:text} \n Address: {address:text}".
   - Note that if `FormalText` is not provided, then the `ReaderFriendlyFormat` acts as `FormalText`, and ADDING to it a relevant boilerplate clause.

### Examples of CLT:

#### Example 1

```json
{
   "title": "Radiology Report",
   "type": "object",
   "properties": {
      "variables": {
         "type": "object",
         "properties": {
            "patientName": {
               "type": "string",
               "description": "Name of the patient"
            },
            "patientID": {
               "type": "string",
               "description": "Patient identification number"
            },
            "dateOfBirth": {
               "type": "string",
               "format": "date",
               "description": "Date of birth of the patient"
            },
            "examinationDate": {
               "type": "string",
               "format": "date",
               "description": "Date when the examination was performed"
            },
            "examinationType": {
               "type": "string",
               "description": "Type of radiological examination"
            },
            "findings": {
               "type": "string",
               "description": "Description of the findings from the examination"
            },
            "conclusion": {
               "type": "string",
               "description": "Radiologist's conclusion based on the findings"
            },
            "radiologist": {
               "type": "string",
               "description": "Name of the radiologist who interpreted the results"
            }
         },
         "required": ["patientName", "patientID", "dateOfBirth", "examinationDate", "examinationType", "findings", "conclusion", "radiologist"]
      },
      "SimplePresentation": {
         "type": "string",
         "description": "Concise representation of Radiology Report",
         "const": "Radiology Report of {patientName:string}, Patient ID: {patientID:string}, Date of Birth: {dateOfBirth:date}, Examination Date: {examinationDate:date}, Examination Type: {examinationType:string}, Findings: {findings:string}, Conclusion: {conclusion:string}, Radiologist: {radiologist:string}"
      },
      "FormalText": {
         "type": "string",
         "description": "Full representation of Radiology Report",
         "const": "Radiology Report \n Patient Name: {patientName:string}\n Patient ID: {patientID:string}\n Date of Birth: {dateOfBirth:date}\n Examination Date: {examinationDate:date}\n Examination Type: {examinationType:string}\n Findings: {findings:string}\n Conclusion: {conclusion:string}\n Radiologist: {radiologist:string}"
      }
   },
   "required": ["variables", "SimplePresentation", "FormalText"]
}
```

#### Example 2

```json
{
   "title": "Simple Purchase Agreement",
   "type": "object",
   "properties": {
      "variables": {
         "type": "object",
         "properties": {
         "seller": {
            "type": "string",
            "description": "Name of the seller"
         },
         "buyer": {
            "type": "string",
            "description": "Name of the buyer"
         },
         "item": {
            "type": "string",
            "description": "Description of the item being sold"
         },
         "price": {
            "type": "number",
            "description": "Sale price of the item"
         },
         "date": {
            "type": "string",
            "format": "date",
            "description": "Date of the agreement"
         },
         "additionalTerms": {
            "type": "string",
            "description": "Any additional terms of the agreement"
         }
         },
         "required": ["seller", "buyer", "item", "price", "date"]
      },
      "SimplePresentation": {
         "type": "string",
         "description": "Concise representation of Simple Purchase Agreement",
         "const": "Simple Purchase Agreement of {seller:string} and {buyer:string} for {item:string} at a price of {price:number} on {date:date}. Additional Terms: {additionalTerms:string}"
      },
      "FormalText": {
         "type": "string",
         "description": "Full representation of Simple Purchase Agreement",
         "const": "Simple Purchase Agreement \n Seller:{seller:string}\n Buyer:{buyer:string} \n Item:{item:string} \n Price:{price:number} \n Date of Purchase:{date:date}.\n Additional Terms: {additionalTerms:string}"
      }
   },
   "required": ["variables", "SimplePresentation"]
}
```

## Claim (CL):

**Description**: \
A sequence of bytes whose structure is defined by Claim Template. 

**Components**: \
- `CLT`: The CLT defining the Claim structure.
- `variableValues`: The values of the `variables` of the CLT and match the `variable type`.

`CL = define(CLT, variableValues)`, where `define` means the creation of a json object.

Here is a generic JSON form of CL:
```json
{
"CL": {
      "Claim Template": // String of the CLT title,
      "variableValues": {
         "variable1": "Value of variable1",
         "variable2": 12345
         // ... values for additional variables as needed
      }
   }
}
```

**SimplePresentation** of CL: 
- Is a modification of the SimplePresentation of its CLT, where each variable is shown as `{variable name:variable value}`.

- Example: The SimplePresentation of a driver license claim is `Driver License of {name:Jane Doe} who lives in {address:120 Amber St., Baltimore, MD}`.

- Example: `Driver License of {name:text} who lives in {address:text}`

- If a variable is a file attachment, it will show as `{variable name : attachment file name: hash of the file content}`.
   - Example: `{inspection: attachment Inspection_Report.pdf:ab450d}`
   - An attached file could be an image, audio, video, etc.

- If variable is another `CL`. In this case, it will show as `{variable name: SimplePresentation of CL}`. The same applies to `ACL` and `ATT`. (see below for examples)

A Claim could be considered a "filled form".

## Attested Claim (ACL)

**Description**: \
ACL associates a claim with a single entity that is called the "issuer" of that claim. We can also say that "the claim is attested by that issuer." We can also call the issuer the "attestor" of that claim.

**Components**:
- `CL`: The Claim.
- `IDS`: The identifier of the issuer of `CL`. It can be referred to as the "Claim Issuer", or the "attestor".

`ACL = define(CL, IDS)`

Here is the generic JSON form of an ACL:
```json
{
   "ACL": {
         "CL": {
            // JSON of CL
         },
         "IDS": "Identity Seal of the Claim Issuer"
      }
}
```

`extCL(ACL)` extracts CL from ACL.

`extIDS(ACL)` extracts IDS from ACL.

If a single Claim needs to be attested by multiple issuers, then an ACL must be created by each issuer for that Claim. We call the group of ATTs that have the same Claim a "composite ACL".

**SimplePresentation** of ACL: 
- Is in the form `\SimplePresentation of CL \IDS/`. 

- Example: The SimplePresentation of an ACL of a driver license is `\Driver License of {name:Jane Doe} who lives in {address:120 Amber St., Baltimore, MD} \MD_DMV/`.

- The SimplePresentation of a composite ACL is a single SimplePresentation with one claim and multiple issuers.
   - Example: The SimplePresentation of the composite ACL `\ CL SimplePresentation \IDS1/` and `\ CL SimplePresentation \IDS2/` is `\ CL SimplePresentation \IDS1 + IDS2/`.
   - ALWAYS USE this SimplePresentation for multiple ACs with the same Claim and different issuers. As in the case of a contract with multiple signers.

An ACL could be considered a "filled form" with a name representing the `IDS` attached to the end of the form.

## Claim Verification Form (CVF)

**Description**: \
It provides the needed information for verification.

**Components**:
- `CL`: A Claim.
- `IDS`: The Issuer identifier.
- `TT`: Target Time.

**Use**:
- It is used when verifying a POA as `POA.verify(CVF.CL,CVF.TT)`, given that `POA.IDS` is the same as `CVF.IDS`.

`CVF = define(CL,IDS,TT)`

The generic JSON of a CVF is:
```json
{
   "CVF": {
      "CL": {
         // JSON of CL
      },
      "IDS": "Identity Seal of the Issuer",
      "TT": "Target Time"
   }
}
```
`extCL(CVF)` extracts CL from CVF.

`extIDS(CVF)` extracts IDS from CVF.

`extTT(CVF)` extracts TT from CVF.

`extAC(CVF)` extracts the ACL `define(CL,IDS)` from CVF.

**SimplePresentation** of CVF
- Is the form `CVF(SimplePresentation of ACL, TT)`. 
- Example: `CVF(\Driver License of {name:Jane Doe} who lives in {address:120 Amber St., Baltimore, MD} \MD_DMV/, Dec 20, 2022)`.

### Claim Verification Form Prototype (CVFP)

**Description**: \
Is a special form of CVF, where some of its variable values are special *placeholders* whose actual values would be known at verification time; i.e., when using `POA.verify(CVF.CL,CVF.TT)`. A CVFP is used to define a group of VFs that match the placeholders.

The following are the only allowed placeholders to be used in CVFP.
   - `tbd_VERIFIER`: Will be replaced with the IDS of the verifier.
   - `tbd_VTIME`: Will be replaced with the verification time.
   - `tbd_ISSUER`: Will be replaced with the POA.IDS.
   - `tbd_HOLDER`: Will be replaced with the POA.Holder.
   - `tbd_OWNER`: Will be replaced with the POA.Owner.
   - `tbd_IGNORE`: Means that the variable can take any value.

Example: `CVF(Driver License of {name:tbd_VERIFIER} who lives in {address:tbd_IGNORE} \MD_DMV/, tbd_VTIME)`.

**Use**: \
`matchVFPrototype(CVFP,CVF)` checks if CVF values match those in the CVFP; if so, it will return `True`, otherwise `False`. 
   - **Example**: If CVFP is `CVF(\Driver License of {name:tbd_VERIFIER} who lives in {address:tbd_IGNORE} \MD_DMV/, tbd_VTIME)`, then CVF `CVF(\Driver License of {name:verifierIDS} who lives in {address:120 Amber St., Baltimore, MD} \MD_DMV/, Dec 20, 2022)` will be a `True` match if the `verifierIDS` is the actual verifier and Dec 20, 2022 is the verification time. It does not matter whatever the address is.


## Attestation (ATT)

**Description**: 
- It is a Claim attested to by a single issuer who commits by creating a POA. 
- It is an association of an ACL with a POA created by the issuer of that ACL; i.e., an ATT is a signed ACL. 
- The issuer of the attestation is both the issuer of the Claim and the signer of the POA.
- It could also be considered a "filled-and-signed form".

**Components**:
- `CL`: The Claim.
- `IDS`: The identifier of the issuer of CL, who should be the signer of the POA. It can be referred to as the "Issuer" or "Claim Issuer".
- `POA`: The POA associating the IDS with the CL. It can be a SIG, a DDS, or CDDS.
   - Simple Attestation (SATT) is an attestation whose POA is SIG.
   - Delegated Attestation (DATT) is an attestation whose POA is DDS.
   - Conditional Attestation (CATT) is an attestation whose POA is CDDS.

**Creation**:
- The issuer of ACL creates a POA using `CreatePOA(POAType, SK, claimData)`, where `claimData` is the CL of the ACL or its hash.
- The ATT is created from the ACL and the created POA.

- In the case of a "composite ACL", an ATT must be created for each ACL by its issuer. Similarly, we call the collection of those ATTs a "composite ATT".

`ATT = define(ACL, POA)`

Generic JSON form of an ATT:
```json
{
   "ATT": {
      "ACL": {
         // JSON of ACL
      },
      "POA": {
         "Type": "Type of the POA (e.g., SIG or DDS)",
         "ID": "Unique identifier of the POA",
         "IDS": "Identity Seal of the POA signer, which should match the ACL's IDS"
      }
   }
}
```

`extAC(ATT)` extracts the ACL from ATT.

`extPOA(ATT)` extracts the POA from ATT.

`extCL(ATT) = extCL(extAC(ATT))`

`extIDS(ATT) = extIDS(extAC(ATT))`

**SimplePresentation** of ATT: 
- Is the SimplePresentation of the ACL, but replacing the `IDS` with `IDS[POA]`.
   - E.g., `\Driver License of {name:Jane Doe} who lives in {address:120 Amber St., Baltimore, MD} \MD_DMV[SIG14393A93]/`.

- In case of a composite ATT, the same replacement is done for each issuer IDS in the SimplePresentation of its composite ACL.
   - E.g., `\ CL SimplePresentation \IDS1[POA1] + IDS2[POA2]/`. This is the **preferred** `SimplePresentation` for a single Claim with multiple signatures, such as multi party contract.

- From the perspective of a verifying entity ("verifier"), an ATT's authenticity is unknown without verification. If a POA of an ATT is verified by E, we can postfix `:Authenticated by verifier` or `:False by verifier` to the `POA`, indicating that E verified the ATT's POA to be `True` or `False`, respectively. 
   - E.g., `\Driver License of {name:Jane Doe} who lives in {address:120 Amber St., Baltimore, MD}\MD_DMV[SIG14393A93:Authenticated by John Darly]/`.


## ATT Package (ATTP):

**Description**: \
Is a package that contains the Attestation (ATT) and all associated files referenced in as attachment in the variables of the ATT's claim. If there are no files, then an ATTP is identical to ATT.

**Components**:
- `ATT`: The Attestation, which is a signed ACL.
- `Files`: All files, documents, or supplementary materials in the attachment fields of the ATT's CL. These files are essential for verifiability. Each file is identified by its name.

**SimplePresentation** of ATTP: 
- Is the SimplePresentation of the ATT followed by a list of attachments, each denoted by its file name. The format is `SimplePresentation of ATT | Files: [File1, File2, ...]`.

- For example, if an ATT for a driver's license has two attached files (a photo and a supporting document), its SimplePresentation could be: \
   `\Driver License of {name:Jane Doe}, {photo:attachment Photo.jpg:5d41402abc4b2a76b9719d911017c592} who lives in {address:120 Amber St., Baltimore, MD}. Included {Supporting Material: attachment Document.pdf:202cb962ac59075b964b07152d234b70} \MD_DMV[SIG14393A93]/ Files: [Photo.jpg, Document.pdf]`.

`extAT(ATTP)` extracts the ATT from ATTP.

`extFiles(ATT)` extracts the files from ATTP.

## Examples

### Example 1: 
Here is the SimplePresentation:

`\Car Purchase Agreement for a  {car:Toyota Camry} {VIN#:123456789ABCDEF }between {seller:Jack} and {buyer:John} \Jack[DDS12345XYZ ?CVF(\Approval from {name:Jack's Wife} \Jack's Wife/,tbd_IGNORE): Authenticated by Sid}] + John[SIG67890ABC:Authenticated by Tim]/` 

It is a representation of an agreement for the purchase of a Toyota Camry, with a specific VIN number, between the seller Jack and the buyer John. 
Jack's part of the agreement is backed by a DDS (DDS12345XYZ) which is conditional upon the approval from Jack's wife. 
DDS12345XYZ has been verified as valid by someone named Sid. 
The agreement also includes John's part, supported by his digital signature (SIG67890ABC), which has been verified as valid by someone named Tim. 
The agreement effectively uses a composite structure to integrate multiple ACLs (from Jack, Jack's wife, and John) into a single document.

ReaderFriendlyFormat:
```
ATT - Car Purchase Agreement:
   Car: Toyota Camry
   VIN#: 123456789ABCDEF
   Seller: Jack
   Buyer: John
Attested by:
   Jack[POA ID: DDS12345XYZ] 
   Requires:
      CVFP - Approval
         Name: Jack's Wife
      Attested by:
         Jack's Wife
      Target Time: tbd_IGNORE 
   Authenticationd by: Sid
Attested by:
   John [POA ID: SIG67890ABC]
   Authenticationd by: Tim
```

### Example 2 

Here is the SimplePresentation of a valid USA passport:

`\Passport of {name:Jane Doe} with {nationality:USA}, {passport number:US987654321}, {date of birth:Jan 1, 1990}, {place of birth:New York, USA}, {issue date:Jan 1, 2023}, {expiration date:Jan 1, 2033} \US Department of getState[SIG12345ABC]/`

ReaderFriendlyFormat:
```
ATT - Passport Information:
   Name: Jane Doe
   Nationality: USA
   Passport Number: US987654321
   Date of Birth: Jan 1, 1990
   Place of Birth: New York, USA
   Issue Date: Jan 1, 2023
   Expiration Date: Jan 1, 2033
Attested by: 
   US Department of getState [POA ID: SIG12345ABC]
```

### Example 4

An example CLT of a property sales agreement (an example of `nested ATTs`):
```json
{
   "title": "Property Sale Agreement",
   "type": "object",
   "properties": {
      "variables": {
         "type": "object",
         "properties": {
            "property": {
               "type": "string",
               "description": "Address and description of the property being sold"
            },
            "history": {
               "type": "ATT",
               "description": "Attestation detailing the property's history"
            },
            "seller": {
               "type": "string",
               "description": "Name of the seller of the property"
            },
            "buyer": {
               "type": "string",
               "description": "Name of the buyer of the property"
            }
         },
         "required": ["property", "history", "seller", "buyer"]
      },
      "SimplePresentation": {
         "type": "string",
         "description": "Concise representation of the agreement details",
         "const": "Property Sale Agreement for {property:string} with History: {history:ATT}, between Seller: {seller:string} and Buyer: {buyer:string}"
      }
   },
   "required": ["variables", "SimplePresentation"]
}
```

The SimplePresentation format:
`\Property Sale Agreement for {property:House at 123 Oak Street, Anytown} ({history:\Property Ownership History of {property:House at 123 Oak Street, Anytown} previously owned by {previous owner:John Doe}, {date of last sale:Jan 1, 2020} \John Doe[DDS12345DEF]/}) between {seller:Alice} and {buyer:Bob} \Alice[SIG67890XYZ] + Bob[SIG12345ABC]/`.  

The ReaderFriendlyFormat:
```
ATT - Property Sale Agreement:
   Property: House at 123 Oak Street, Anytown
   History:
      ATT - Property Ownership History:
         Property: House at 123 Oak Street, Anytown
         Previously owned by: John Doe
         Date of last sale: Jan 1, 2020
      Attested by: John Doe [POA ID: DDS12345DEF]
   Seller: Alice
   Buyer: Bob
Attested by:
   Alice [POA ID: SIG67890XYZ]
Attested by:
   Bob [POA ID: SIG12345ABC]
```

### Example 5

Here is the SimplePresentation of a nested ATTs package:

`\Property Sale Agreement for {property:House at 456 Maple Drive, Lakeside} ({history:\Property Ownership History of {property:House at 456 Maple Drive, Lakeside} previously owned by {previous owner:Lucas}, {date of last sale:June 15, 2018} {Previous Sale Deed: attachment Sale_Deed_2018.pdf:def456} \Lucas[DDS67890DEF]/}) between {seller:Emily} and {buyer:Mark} {inspection: attachment Inspection_Report.pdf:ab450d} \Emily[SIG12345XYZ] + Mark[SIG98765ABC]/ Files: [Inspection_Report.pdf, Sale_Deed_2018.pdf]`

ReaderFriendlyFormat:
```
ATT - Property Sale Agreement:
   Property: House at 456 Maple Drive, Lakeside
   History:
      ATT - Property Ownership History:
         Property: House at 456 Maple Drive, Lakeside
         Previously owned by: Lucas
         Date of last sale: June 15, 2018
         Previous Sale Deed: attachment Sale_Deed_2018.pdf (def456)
      Attested by: Lucas [POA ID: DDS67890DEF]
   Seller: Emily
   Buyer: Mark
   Inspection: attachment Inspection_Report.pdf (ab450d)
Attested by:
   Emily [POA ID: SIG12345XYZ]
Attested by:
   Mark [POA ID: SIG98765ABC]
Files: 
   [Inspection_Report.pdf, Sale_Deed_2018.pdf]
```

### Example 6

The ReaderFriendlyFormat: 

`\Medical Record of {name:Jane Doe}, {age:30}, {blood type:O+}, {allergies:\Allergy Information {allergen:Peanuts} \Allergy Specialist[DDS67890ABC]/}, {last visit:January 1, 2023} \Central Hospital[DDS12345XYZ]/` 

The ReaderFriendlyFormat
```
ATT - Medical Record:
   Name: Jane Doe
   Age: 30
   Blood Type: O+
   Allergies:
      ATT - Allergy Information:
         Allergen: Peanuts
      Attested by: Allergy Specialist [POA ID: DDS67890ABC]
   Last Visit: January 1, 2023
Attested by: 
   Central Hospital [POA ID: DDS12345XYZ]
```

## Creation and Verification of Attestations

### Creating Attestations

An entity *attests* a claim means that the entity creates an attestation ATT = \Claim\IDS[POA]/, where IDS is the identifier of that entity. This is the same as saying that the entity *signed* the claim, or the entity *issued* an attestation. For this, we call that entity attestor, signer, and issuer, respectively.

The signer has a number of decisions and options:
- Decision 1: based on POA Type
   - Option 1.1: SIG
   - Option 1.2 (default): DDS
      - Decision 1.2.1 based on revenue
         - Option 1.2.1.1: Not generating revenue: In this case, DDSP is set to zero value.
         - Option 1.2.1.2 (default): Generating revenue: DDSP is set to a non-zero value. 
            - Decision 1.2.1.2.1 based on revenue protection
               - Option 1.2.1.2.1.1 (default): Protected Revenue
                  - `UnprotectedRevenue` is `False`
               - Option 1.2.1.2.1.2: Unprotected Revenue
                  - `UnprotectedRevenue` is `True`
      - Decision 1.2.2: based on Registry
         - Option 1.2.2.1: Use an Issuer Registry:
            - The issuer creates and manages their own Registry to handle their attestations.
         - Option 1.2.2.2 (default): Use a non-Issuer Registry:
            - The issuer uses an existing Registry.
      - Decision 1.2.3: based on ownership transferability:
         - Option 1.2.3.1 (default): Transferrable ownership
            - `OwnershipTransferrable` is `True`
         - Option 1.2.3.2: Non-Transferrable ownership
            - `OwnershipTransferrable` is `False`
      - Decision 1.2.3: based on hold transferability:
         - Option 1.2.3.1 (default): Transferrable hold
            - `HoldTransferrable` is `True`
         - Option 1.2.3.2: Non-Transferrable hold
            - `HoldTransferrable` is `False`


## Authentication of ATT

Authentication is the process of verifying the authenticity of a POA associated with an ATT. Note that composite attestation is actually separate attestations, and a composite attestation is authentic if, and only if, all the separate attestations are authentic.

### Authenticating SATT
By using `SATAuthentication(SATT)`.

Implementation:
   Returns the result of `verify(extIDS(SATT),extCL(SATT),extSIG(SATT))`, where `verify` is the common digital signature verification function.

### Authenticating DATT
By using `DATAuthentication(TT,DATT; VerificationFee)`, which is the same as `DATAuthentication(TT,ACL,DDS; VerificationFee)`. `VerificationFee` is the amount required for verification.

Implementation:
   1. If DATT is CATT, fail and instruct using Authenticating CATT.
   2. (BEXACTSC) Check DDS authorization at TT.
      2.1 If not authorized, returns 'False'
      2.2 If authorized, proceed.
   3. (Registry) Check if the SIG in DDS verifies ACL.
      3.1 If it does, returns 'True'.
      3.2 If not, returns 'False'.

> **Note**
> 
> BEXACTSC: Means to be handled by BEXACTSC (see STASH Reference Manual).
> 
> Registry: Means to be handled by the right STASH's Registry (see STASH Reference Manual).

### Authenticating CATT

By using `CATTAuthentication(TT,CATT,ATTr; VerificationFee)`, which is the same as `CATTAuthentication(TT,ACL,DDS?CVFP, ATTr; VerificationFee)`. ATTr is required ATT that should match.

Implementation 1: 
   1. (BEXACTSC) If CATT is DATT, fail and instruct to use Authenticating DATT.
   2. (BEXACTSC) If hash(CVFP) does not match the DDS State, returns 'False'.
   3. (BEXACTSC) If extAC(CVFP) matches extAC(ATTr) using the matching function, if not return 'False'.
   4. (BEXACTSC) If ATTr is SATT
      4.1 If ATTr is not valid, return 'False'
      4.2 If ATTr is valid, then authorize, go to 7.
   5. (BEXACTSC) If ATTr is DATT
      5.1 If extDDS(ATTr)== DDS, go to 7.
      5.2 Check if extDDS(ATTr) is authorized. If not, return 'False'.
      5.3 Rely on Registry to Authentication ATTr.
   6. (Registry) check if ATTr is valid. DATAuthentication(tbd_TT,ATTr)
      - If not valid, return 'False'.
   7. (Registry) check if you hold the '\ extCL(CATT) \ extIDS(CATT)[SIG] /'.
      7.1 If true, return 'True'.
      7.2 Otherwise return 'False'.

Implementation 2:
   1. (BEXACTSC) If CATT is DATT, fail and instruct using Authenticating DATT.
   2. (Registry) If hash(CVFP) does not match the DDS State, returns 'False'.
   3. (Registry) If extAC(CVFP) matches extAC(ATTr) using the matching function, if not return 'False'.
   4. (Registry) If ATTr is SATT
      4.1 If ATTr is not valid, return 'False'
      4.2 If ATTr is valid, then authorize, go to 7.
   5. (Registry) If ATTr is DATT
      5.1 If extDDS(ATTr)== DDS, go to 7. // "Circular conditional authorization" intended to match format only.
      5.2 Check if extDDS(ATTr) is authorized. If not, return 'False'.
      5.3 Rely on Registry to Authentication ATTr.
   6. (Registry) check if ATTr is valid. DATAuthentication(tbd_TT,ATTr)
      - If not valid, return 'False'.
   7. (Registry) check if you hold the `\ extCL(CATT) \extIDS(CATT)[SIG]/`.
      7.1 If true, return 'True'.
      7.2 Otherwise return 'False'.

#### Authetnticating a "circular conditional authorization"

CATT = `\ ID {Name: Jane Doe} {Expiration Date: Oct 12,2024} \IDS_gov[DDSid? CVF(\ ID {Name:tbd_IGNORE} {Expiration Date:::}}tbd_VTIME} \IDS_gov/, tbd_VTIME )]/` 
Authentication as follows:
   `CDATAuthentication(VTIME, ACL, DDSid?CVF(\ ID {Name:tbd_IGNORE} {Expiration Date:::}}tbd_VTIME} \IDS_gov/, tbd_VTIME), \ ID {Name: Jane Doe} {Expiration Date: Oct 12,2024} \IDS_gov[DDSid]/; VerificationFee)`
   
Note that the rAT is the same as the original CATT, which is a circular condition. 
In this case, the rAT will be considered authenticated, so that we benefit from the matching of the CVF Prototype 
`CVF(\ ID {Name:tbd_IGNORE} {Expiration Date:::}}tbd_VTIME} \IDS_gov/, tbd_VTIME )`. 
The matching makes sure that the verification time is prior to the expiration date. Otherwise, the DDS will be unauthorized.

In this example, we can check that the VTIME is before the expiration date, without having to change authorization at the expiration date.

## Validation of ATTP

Validation of ATTP includes the authentication of the attestation as well as the validation of the attached files. I.e.:
   - Check that file attachments are included in the ATTP. If not, terminate and return `False`.
   - Check that the file hash mentioned in the claim matches that of corresponding included file. If not, terminate and return `False`.


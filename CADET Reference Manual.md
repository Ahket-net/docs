# CADET Reference Manual

- Related documents:
   - STASH Reference Manual
   - Legal Framework
 
> **Notes About This Document**
>  Any object described will have public and private attributes, as well as public and private methods.
>  Object.attribute refers to an attribute, and Object.method() refers to calling a method.
>  *SimplePresentation* is used to present any object or item in a document, book, or webpage that explain or demonstrate their use and applications.

## About CADET

CADET stands for "Cryptographic Attestation Documentation and Evidence Technology". It defines the basic elements and structure developed by Ahket Inc.


## User Identifier

A user is an entity, which could be an organization or a person, must have a cryptographic public-private key pair (IDS and SK):
- Identity Seal (IDS):
   - The public key.
   - This is the user's identifier.
- Secret Key (SK).
   - The private key.

The key pair is used for cryptographic operations, such as digital signature.

## Proof of Attestation (POA)

Definition:
An incontrovertible evidence that an entity ("signer") has made a formal attestation to a "claim".

Description:
POA serves as compelling evidence that a signer has committed to a claim. E.g., the digital signature of a message that affirms the message's authenticity. Any entity can "verify" that the signer attested a claim using the POA. POA might require a fee payment to do the verification.

Attributes:
- `Type`: The category of the POA. There are two types: `SIG` and `DDS`.
- `ID`: A unique alpha-numeral identifier associated with each instance of a POA.
- `IDS`: The public key of the signer that created the POA. We can refer to IDS or signer interchangebly.

Public Methods:
- `getVerificationFee()`: Returns the `VerificationFee` value required for verification.

Creation:
- `CreatePOA(POAType, SK, claim)`: Creates and returns a POA, where `SK` is the private key of `IDS`. Its exact implementation depends on `POAType`.

### Two types of POA

#### Digital Signature (SIG)

Description:
The common digital signature created by a public-private key pair. There are no fees to verify a SIG.

Creation:
- `CreatePOA(SIG, SK, claim)` creates an instant of PAO whose `Type` is `SIG` and `ID` is the result of the common digitial siging function `sign(date, IDS)`. 
- `CreateSIG(SK, claim)` is the same as `CreatePOA(POAType, SK, claim)`.

Methods:
- The same methods as POA.
   - The method `verify(claim,time)` returns the result of the common digital signature verification `verify(claim,POA.IDS,POA.ID)`, regardless of `time`.

Notation: 
- A signature created for a specific `claim` and signer '`IDS` can be noted as `SIG[claim,IDS]`. This means that this signature is created by the `SK` associated with that `IDS`. This means that the common digital signature verification function `verify(claim,IDS,SIG[claim,IDS])` is always `True`.

**SimplePresentation**: 
Is the concatenation of `SIG` and `ID` value. E.g., `SIG1234AB56`.

#### Delegated Digital Signature (DDS)

Description:
- It is a representation of a SIG stored on STASH. It is used to *verify* that there is such SIG and that it is authentic---provided that it is authorized by the signer to answer a verification request. It can be considered a generalization of SIG with more flexibility because of the following *unique properties*:
   - **Transferrable Ownership**: The DDS owner has the right to transfer the ownership to another entity.
   - **Revenue Generating**: It can generate revenue to its owner from verification fees.
   - **Amendable**: The result of a verification can be amended, but not retrospectibly.
   - **Provisory**: The result of verification can be limited by some constrain.
   - **Traceable**: The creation, changes, transfers, verifications are recorded on STASH (see STASH Reference Manual) and can be traced.

Creation:
- `CreatePOA(DDS, SK, claim)` creates through STASH the `DDS Record` associated with the `IDS` and `claim`---where the `IDS` is the public key associated with the `SK`. The STASH thus creates a POA whose `Type` is `DDS` and `ID` is a unique number representing the pair `(IDS,claim)`; i.e., for each new pair of `IDS` and `claim`, a new number is generated for the ID. Only STASH and the signer can relate DDS.ID to the `claim`. For example, it could be just a sequential number for each new `(IDS,claim)`.
- `CreateDDS(SK, claim)` is a shorter way to write `CreatePOA(DDS, SK, claim)`.
- Note that NO ONE can change the claim of a created DDS once created.

DDS Record and STASH:
- STASH is required to create, update and verify a DDS.
- `DDS Record`: Each DDS references a DDS Record stored in STASH. The record has a private and public components.
   - **Private DDS Information**: This information is held with highes security in STASH.
      - The `claim` hash. STASH does not save the claim itself.
      - The SIG associated with the `claim` hash.
      
   - **Public DDS Information**:
      - `AuthorizationLog`: Is a sequence of events representing changes to the DDS authorization through creation or update requests to STASH. Each event is recorded with the time of change and a SIG issued by the signer (IDS) authenticating the change request. 
         - The change of the authorization could be one of the following:
            - `Authorized`: The DDS can be used for verification. When a DDS is created for the first time, its authorization is `Authorized`.
               - Authorized is an implicit attestation by the signer at a specific date "is authorizing STASH to verify through DDS."
            - `Unauthorized`: The DDS cannot be used for verification.
               - Unauthorized is an implicit attestation by the signer at a specific date "is unauthorizing STASH to verify through DDS."
            - `ConditionalAuthorization`: The DDS can be used for verification if some conditionis satisfied. THe condition is described using a sepcial Verification Form Prototype (see VF$ below). In this case, the DDS is a "conditional DDS" (CDDS).
               - `ConditionalAuthorization` is an implicit attestation by the signer at a specific date "is authorizing STASH to verify through DDS provided that certain condition is satisfied."
         - Because of events are recorded at the time of the signer request, the DDS is strongly timestamped by STASH.
      - `Owner`: An entity that earns some of the fees paid during verification to STASH. We call the owner of DDS the `DDS owner` or `DDSO`.
      - `OwnershipTransferrable`: Is a boolean value. If `True` and the owner is NOT the signer of the DDS, then the current owner can transfer the DDS to a new owner; i.e., change `Owner` to the new owners's IDS. It can only be changed by the signer.
      - `Holder`: An entity that has some rights. We call the holder of DDS the `DDS Holder` or `DDSH`.
      - `HoldTransferrable`: Is a boolean value. If `True` and the holder is NOT the signer of the DDS, then the current holder can transfer the DDS to a new holder; i.e., change `Holder` to the new holder's IDS. It can only be changed by the signer.
      - `VerificationFee`: The fee required for verification. See STASH.
      - `DDSP`: DDS Price. See STASH.
      - `UnprotectedRevenue`: Is a boolean value. See STASH. 
   - When created for the first time, the `Owner` and `Holder` are `IDS`.
   - The DDS Record in STASH is what gives DDS its **unique properties**.

**Limited-access Methods**:
- `ChangeAuthorization(newState)`: It uses STASH to add an event to `AuthorizationLog` at the event time and change the DDS authorization. The change of the DDS authorization applies from the event time forward. ONLY the DDS signer can call this method.
- `SetDDSP(value)` sets the value of `DDSP`. Only the DDS owner can use this, if allowed by STASH.

**Public Methods**:
- `getState(targetTime)`: Returns the authorization state of a DDS at some point of time (`targetTime`). It returns the authorization state after the latest chage in `AuthorizationLog` on or before `targetTime`. If the `targetTime` is before the creation of the DDS, then the state is `Unauthorized`.

- `isConditional(targetTime)`: Is a boolean value. It returns `True` only if `getState(targetTime)` is `ConditionalAuthorization`.

- `getFee()` returns the value of `VerificationFee`.

**SimplePresentation**: 
Is the concatenation of `DDS` and `ID` number. E.g., `DDS01A9F943` for `ID=01A9F943` .
   - In case of conditional DDS, a suffix `?ConditionalAuthorization` is added at the end.
      - E.g., `DDS1034EB57 ?VF(\Driver License of <name:$VERIFIER> who lives in <address:$IGNORE>\MD_DMV/, $VTIME)`.


## Claim Template (CT)

Description:
CT is a JSON schema describing the blueprint of a claim. It could be considered as an "empty form".

Components:
- `variables` that describe the variables of the template.
- `SimplePresentation`: Is a "string" that provides concise textual representation of the variables, each represented as `<variable name : variable type>`. It is used when demonstrating or educating about CADET structures.
   - Example: "Driver License of <name:text> who lives in <address:text>"
   - Example: "File Attachment <passport: attachment>"
   - Example: In case a variable that is `CL`, `AC` or `AT` (see below), "Attested Balance is <account balance: AC>"
- `ReaderFriendlyFormat`: Is a reformatting of the SimplePresentation of an object using indentations and line breaks to make it easier to read. It is used when demonstrating or educating about CADET structures.
- `FormalText`: Is the formal layout to present a thorough textual representation. It ALWAYS include a boilerplate clause that is appropriate for the type of document being templated. The boilerplate clause should reflect the legal and formal aspects relevant to the specific type of document.It is used as the primary presentation to the public.
   - Example: "Driver License \n Name: <name:text> \n Address: <address:text>".
   - Note that if `FormalText` is not provided, then the `ReaderFriendlyFormat` acts as `FormalText`, and ADDING to it a relevant boilerplate clause.

### Examples of CT:

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
         "const": "Radiology Report of <patientName:string>, Patient ID: <patientID:string>, Date of Birth: <dateOfBirth:date>, Examination Date: <examinationDate:date>, Examination Type: <examinationType:string>, Findings: <findings:string>, Conclusion: <conclusion:string>, Radiologist: <radiologist:string>"
      },
      "FormalText": {
         "type": "string",
         "description": "Full representation of Radiology Report",
         "const": "Radiology Report \n Patient Name: <patientName:string>\n Patient ID: <patientID:string>\n Date of Birth: <dateOfBirth:date>\n Examination Date: <examinationDate:date>\n Examination Type: <examinationType:string>\n Findings: <findings:string>\n Conclusion: <conclusion:string>\n Radiologist: <radiologist:string>"
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
         "const": "Simple Purchase Agreement of <seller:string> and <buyer:string> for <item:string> at a price of <price:number> on <date:date>. Additional Terms: <additionalTerms:string>"
      },
      "FormalText": {
         "type": "string",
         "description": "Full representation of Simple Purchase Agreement",
         "const": "Simple Purchase Agreement \n Seller:<seller:string>\n Buyer:<buyer:string> \n Item:<item:string> \n Price:$<price:number> \n Date of Purchase:<date:date>.\n Additional Terms: <additionalTerms:string>"
      }
   },
   "required": ["variables", "SimplePresentation"]
}
```


## Claim (CL):

Description:
A sequence of bytes whose structure is defined by Claim Template. 

Components:
- `CT`: The CT defining the Claim structure.
- `variableValues`: The values of the `variables` of the CT and match the `variable type`.

CL = define(CT,variableValues), where `define` means the creation of a json object.
Generic JSON form of CL:
```json
{
"CL": {
      "Claim Template": // String of the CT title,
      "variableValues": {
         "variable1": "Value of variable1",
         "variable2": 12345
         // ... values for additional variables as needed
      }
   }
}
```

**SimplePresentation** of CL: 
- Is a modification of the SimplePresentation of its CT, where each variable is shown as `<variable name:variable value>`.
- Example: The SimplePresentation of a driver license claim is `Driver License of <name:Jane Doe> who lives in <address:120 Amber St., Baltimore, MD>`.
- Example: `Driver License of <name:text> who lives in <address:text>`
- If a variable is a file attachment, it will show as `<variable name : attachment file name: hash of the file content>`.
   - Example: `<inspection: attachment Inspection_Report.pdf:ab450d>`
   - An attached file could be an image, audio, video, etc.
- If variable is another `CL`. In this case, it will show as `<variable name: SimplePresentation of CL>`. The same applies to `AC` and `AT`. (see below for examples)

A Claim could be considered a "filled form".


## Attested Claim (AC)

Description:
AC associates a claim with a single entity that is called the "issuer" of that claim. We can also say that "the claim is attested by that issuer." We can also call the issuer the "attestor" of that claim.

Components:
- `CL`: The Claim.
- `IDS`: The identifier of the issuer of `CL`. It can be referred to as the "Claim Issuer", or the "attestor".

AC = define(CL,IDS)
Generic JSON form of an AC:
```json
{
   "AC": {
         "CL": {
            // JSON of CL
         },
         "IDS": "Identity Seal of the Claim Issuer"
      }
}
```

`extCL(AC)` extracts CL from AC.
`extIDS(AC)` extracts IDS from AC.

- If a single Claim needs to be attested by multiple issuers, then an AC must be created by each issuer for that Claim. We call the group of ATs that have the same Claim a "composite AC".

**SimplePresentation** of AC: 
- Is in the form `\SimplePresentation of CL \IDS/`. 
- Example: The SimplePresentation of an AC of a driver license is `\Driver License of <name:Jane Doe> who lives in <address:120 Amber St., Baltimore, MD> \MD_DMV/`.
- The SimplePresentation of a composite AC is a single SimplePresentation with one claim and multiple issuers.
   - Example: The SimplePresentation of the composite AC `\ CL SimplePresentation \IDS1/` and `\ CL SimplePresentation \IDS2/` is `\ CL SimplePresentation \IDS1 + IDS2/`.
   - ALWAYS USE this SimplePresentation for multiple ACs with the same Claim and different issuers. As in the case of a contract with multiple signers.

An AC could be considered a "filled form" with a name representing the `IDS` attached to the end of the form.


## Verification Form (VF)

Description: 
It provides the needed information for verification.

Components:
- `CL`: A Claim.
- `IDS`: The Issuer identifier.
- `TT`: Target Time.

Use:
- It is used when verifying a POA as `POA.verify(VF.CL,VF.TT)`, given that `POA.IDS` is the same as `VF.IDS`.

VF = define(CL,IDS,TT)
- The generic JSON of a VF is:
```json
{
   "VF": {
      "CL": {
         // JSON of CL
      },
      "IDS": "Identity Seal of the Issuer",
      "TT": "Target Time"
   }
}
```
`extCL(VF)` extracts CL from VF.
`extIDS(VF)` extracts IDS from VF.
`extTT(VF)` extracts TT from VF.
`extAC(VF)` extracts the AC `define(CL,IDS)` from VF.

**SimplePresentation** of VF
- Is the form `VF(SimplePresentation of AC, TT)`. 
- Example: `VF(\Driver License of <name:Jane Doe> who lives in <address:120 Amber St., Baltimore, MD> \MD_DMV/, Dec 20, 2022)`.

### Verification Form Prototype (VF$)

Description: 
Is a special form of VF, where some of its variable values are special *placeholders* whose actual values would be known at verification time; i.e., when using `POA.verify(VF.CL,VF.TT)`. A VF$ is used to define a group of VFs that match the placeholders.

The following are the only allowed placeholders to be used in VF$.
   - `$VERIFIER`: Will be replaced with the IDS of the verifier.
   - `$VTIME`: Will be replaced with the verification time.
   - `$ISSUER`: Will be replaced with the POA.IDS.
   - `$HOLDER`: Will be replaced with the POA.Holder.
   - `$OWNER`: Will be replaced with the POA.Owner.
   - `$IGNORE`: Means that the variable can take any value.

Example: `VF(Driver License of <name:$VERIFIER> who lives in <address:$IGNORE> \MD_DMV/, $VTIME)`.

Use: 
- `matchVFPrototype(VF$,VF)` checks if VF values match those in the VF$; if so, it will return `True`, otherwise `False`. 
   - Example: If VF$ is `VF(\Driver License of <name:$VERIFIER> who lives in <address:$IGNORE> \MD_DMV/, $VTIME)`, then VF `VF(\Driver License of <name:verifierIDS> who lives in <address:120 Amber St., Baltimore, MD> \MD_DMV/, Dec 20, 2022)` will be a `True` match if the `verifierIDS` is the actual verifier and Dec 20, 2022 is the verification time. It does not matter whatever the address is.


## Attestation (AT)

Description: 
- It is a Claim attested to by a single issuer who commits by creating a POA. 
- It is association of an AC with a POA created by the issuer of that AC; i.e., an AT is a signed AC. 
- The issuer of the attestation is both the issuer of the Claim and the signer of the POA.
- It could also be considered a "filled-and-signed form".

Components:
- `CL`: The Claim.
- `IDS`: The identifier of the issuer of CL, who should be the signer of the POA. It can be referred to as the "Issuer" or "Claim Issuer".
- `POA`: The POA associating the IDS with the CL. It can be a SIG, a DDS, or CDDS.
   - Simple Attestation (SAT) is an attestation whose POA is SIG.
   - Delegated Attestation (DAT) is an attestation whose POA is DDS.
   - Conditional Attestation (CAT) is an attestation whose POA is CDDS.

Creation:
- The issuer of AC creates a POA using `CreatePOA(POAType, SK, claimData)`, where `claimData` is the CL of the AC or its hash.
- The AT is created from the AC and the created POA.

- In the case of a "composite AC", an AT must be created for each AC by its issuer. Similarly, we call the collection of those ATs a "composite AT".

AT = define(AC,POA)
- Generic JSON form of an AT:
```json
{
   "AT": {
      "AC": {
         // JSON of AC
      },
      "POA": {
         "Type": "Type of the POA (e.g., SIG or DDS)",
         "ID": "Unique identifier of the POA",
         "IDS": "Identity Seal of the POA signer, which should match the AC's IDS"
      }
   }
}
```

`extAC(AT)` extracts the AC from AT.
`extPOA(AT)` extracts the POA from AT.
`extCL(AT) = extCL(extAC(AT))`
`extIDS(AT) = extIDS(extAC(AT))`

**SimplePresentation** of AT: 
- Is the SimplePresentation of the AC, but replacing the `IDS` with `IDS[POA]`.
   - E.g., `\Driver License of <name:Jane Doe> who lives in <address:120 Amber St., Baltimore, MD> \MD_DMV[SIG14393A93]/`.
- In case of a composite AT, the same replacement is done for each issuer IDS in the SimplePresentation of its composite AC.
   - E.g., `\ CL SimplePresentation \IDS1[POA1] + IDS2[POA2]/`. This is the **preferred** `SimplePresentation` for a single Claim with multiple signatures, such as multi party contract.
- From the perspective of a verifying entity ("verifier"), an AT's authenticity is unknown without verification. If a POA of an AT is verified by E, we can postfix `:Authenticated by verifier` or `:False by verifier` to the `POA`, indicating that E verified the AT's POA to be `True` or `False`, respectively. 
   - E.g., `\Driver License of <name:Jane Doe> who lives in <address:120 Amber St., Baltimore, MD>\MD_DMV[SIG14393A93:Authenticated by John Darly]/`.


## AT Package (ATP):

Description: 
Is a package that contains the Attestation (AT) and all associated files referenced in as attachment in the vairables of the AT's claim. If there are no files, then an ATP is identical to AT.

Components:
- `AT`: The Attestation, which is a signed AC.
- `Files`: All files, documents, or supplementary materials in the attachment fields of the AT's CL. These files are essential for verifiability. Each file is identified by its name.

**SimplePresentation** of ATP: 
- Is the SimplePresentation of the AT followed by a list of attachments, each denoted by its file name. The format is `SimplePresentation of AT | Files: [File1, File2, ...]`.
- For example, if an AT for a driver's license has two attached files (a photo and a supporting document), its SimplePresentation could be:
   `\Driver License of <name:Jane Doe>, <photo:attachment Photo.jpg:5d41402abc4b2a76b9719d911017c592> who lives in <address:120 Amber St., Baltimore, MD>. Included <Supporting Material: attachment Document.pdf:202cb962ac59075b964b07152d234b70> \MD_DMV[SIG14393A93]/ Files: [Photo.jpg, Document.pdf]`.

`extAT(ATP)` extracts the AT from ATP.
`extFiles(AT)` extracts the files from ATP.

## Examples

### Example 1: 

`\Car Purchase Agreement for a  <car:Toyota Camry> <VIN#:123456789ABCDEF >between <seller:Jack> and <buyer:John> \Jack[DDS12345XYZ ?VF(\Approval from <name:Jack's Wife> \Jack's Wife/,$IGNORE): Authenticated by Sid>] + John[SIG67890ABC:Authenticated by Tim]/` is a representation of an agreement for the purchase of a Toyota Camry, with a specific VIN number, between the seller Jack and the buyer John. Jack's part of the agreement is backed by a DDS (DDS12345XYZ) which is conditional upon the approval from Jack's wife. DDS12345XYZ has been verified as valid by someone named Sid. The agreement also includes John's part, supported by his digital signature (SIG67890ABC), which has been verified as valid by someone named Tim. The agreement effectively uses a composite structure to integrate multiple ACs (from Jack, Jack's wife, and John) into a single document.

ReaderFriendlyFormat:
```
AT - Car Purchase Agreement:
   Car: Toyota Camry
   VIN#: 123456789ABCDEF
   Seller: Jack
   Buyer: John
Attested by:
   Jack[POA ID: DDS12345XYZ] 
   Requires:
      VF$ - Approval
         Name: Jack's Wife
      Attested by:
         Jack's Wife
      Target Time: $IGNORE 
   Authenticationd by: Sid
Attested by:
   John [POA ID: SIG67890ABC]
   Authenticationd by: Tim
```

### Example 2 

`\Maryland Birth Certificate of <name:Jane Doe>, born on <date of birth:April 10, 1990>, at <place of birth:Baltimore, Maryland>, child of <parent1:John Doe> and <parent2:Mary Smith>, certificate number <certificate number:MD123456>, issued on <issue date:April 20, 1990> \Maryland Department of Health[SIG12345ABC]/` is an example of a verifiable birth certificate.

### Example 3

`\Passport of <name:Jane Doe> with <nationality:USA>, <passport number:US987654321>, <date of birth:Jan 1, 1990>, <place of birth:New York, USA>, <issue date:Jan 1, 2023>, <expiration date:Jan 1, 2033> \US Department of getState[SIG12345ABC]/` is an example of a valid USA passport.

ReaderFriendlyFormat:
```
AT - Passport Information:
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

An example CT of a property sales agreement (an example of `nested ATs`):
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
               "type": "AT",
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
         "const": "Property Sale Agreement for <property:string> with History: <history:AT>, between Seller: <seller:string> and Buyer: <buyer:string>"
      }
   },
   "required": ["variables", "SimplePresentation"]
}
```

The SimplePresentation format:
`\Property Sale Agreement for <property:House at 123 Oak Street, Anytown> (<history:\Property Ownership History of <property:House at 123 Oak Street, Anytown> previously owned by <previous owner:John Doe>, <date of last sale:Jan 1, 2020> \John Doe[DDS12345DEF]/>) between <seller:Alice> and <buyer:Bob> \Alice[SIG67890XYZ] + Bob[SIG12345ABC]/`.  

The ReaderFriendlyFormat:
```
AT - Property Sale Agreement:
   Property: House at 123 Oak Street, Anytown
   History:
      AT - Property Ownership History:
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

`\Property Sale Agreement for <property:House at 456 Maple Drive, Lakeside> (<history:\Property Ownership History of <property:House at 456 Maple Drive, Lakeside> previously owned by <previous owner:Lucas>, <date of last sale:June 15, 2018> <Previous Sale Deed: attachment Sale_Deed_2018.pdf:def456> \Lucas[DDS67890DEF]/>) between <seller:Emily> and <buyer:Mark> <inspection: attachment Inspection_Report.pdf:ab450d> \Emily[SIG12345XYZ] + Mark[SIG98765ABC]/ Files: [Inspection_Report.pdf, Sale_Deed_2018.pdf]` is an example of a nested ATs package.

ReaderFriendlyFormat:
```
AT - Property Sale Agreement:
   Property: House at 456 Maple Drive, Lakeside
   History:
      AT - Property Ownership History:
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

The ReaderFriendlyFormat of `\Medical Record of <name:Jane Doe>, <age:30>, <blood type:O+>, <allergies:\Allergy Information <allergen:Peanuts> \Allergy Specialist[DDS67890ABC]/>, <last visit:January 1, 2023> \Central Hospital[DDS12345XYZ]/` is:
```
AT - Medical Record:
   Name: Jane Doe
   Age: 30
   Blood Type: O+
   Allergies:
      AT - Allergy Information:
         Allergen: Peanuts
      Attested by: Allergy Specialist [POA ID: DDS67890ABC]
   Last Visit: January 1, 2023
Attested by: 
   Central Hospital [POA ID: DDS12345XYZ]
```
## Creation and Verification of Attestations

### Creating Attestations

An entity *attests* a claim means that the entity creates an attestation AT = \Claim\IDS[POA]/, where IDS is the identifier of that entity. This is the same as saying that the entity *signed* the claim, or the entity *issued* an attestation. For this, we call that entity attestor, signer, and issuer, respectively.

The signer has a number of descisions and options:
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
      - Decision 1.2.3: based on ownership transferrability:
         - Option 1.2.3.1 (default): Transferrable ownership
            - `OwnershipTransferrable` is `True`
         - Option 1.2.3.2: Non-Transferrable ownership
            - `OwnershipTransferrable` is `False`
      - Decision 1.2.3: based on hold transferrability:
         - Option 1.2.3.1 (default): Transferrable hold
            - `HoldTransferrable` is `True`
         - Option 1.2.3.2: Non-Transferrable hold
            - `HoldTransferrable` is `False`


## Authentication of AT

Authentication is the process of verifing the authenticity of a POA associated with an AT. Note that composite attestation is actually separate attestations, and a composite attestation is authentic if, and only if, all the separate attestations are authentic.

### Authenticating SAT
By using `SATAuthentication(SAT)`.

Implementation:
   Returns the result of `verify(extIDS(SAT),extCL(SAT),extSIG(SAT))`, where `verify` is the common digital signature verification function.

### Authenticating DAT
By using `DATAuthentication(TT,DAT; VerificationFee)`, which is the same as `DATAuthentication(TT,AC,DDS; VerificationFee)`. `VerificationFee` is the amount required for verification.

Implementation:
   1. If DAT is CAT, fail and instruct to use Authenticating CAT.
   2. (BEXACTSC) Check DDS authorization at TT.
      2.1 If not authorized, returns 'False'
      2.2 If authorized, proceed.
   3. (Registry) Check if the SIG in DDS verifies AC.
      3.1 If it does, returns 'True'.
      3.2 If not, returns 'False'.

Note:
BEXACTSC: Means to be handled by BEXACTSC (see STASH Reference Manual).
Registry: Means to be handled by the right STASH's Registry (see STASH Reference Manual).

### Authenticating CAT
By using `CATAuthentication(TT,CAT,ATr; VerificationFee)`, which is the same as `CATAuthentication(TT,AC,DDS?VF$, ATr; VerificationFee)`. ATr is required AT that should match.

Implementation 1: 
   1. (BEXACTSC) If CAT is DAT, fail and instruct to use Authenticating DAT.
   2. (BEXACTSC) If hash(VF$) does not match the DDS State, returns 'False'.
   3. (BEXACTSC) If extAC(VF$) matches extAC(ATr) using the matching function, if not return 'False'.
   4. (BEXACTSC) If ATr is SAT
      4.1 If ATr is not valid, return 'False'
      4.2 If ATr is valid, then authorize, go to 7.
   5. (BEXACTSC) If ATr is DAT
      5.1 If extDDS(ATr)== DDS, go to 7.
      5.2 Check if extDDS(ATr) is authorized. If not, return 'False'.
      5.3 Rely on Registry to Authentication ATr.
   6. (Registry) check if ATr is valid. DATAuthentication(TT$,ATr)
      - If not valid, return 'False'.
   7. (Registry) check if you hold the '\ extCL(CAT) \ extIDS(CAT)[SIG] /'.
      7.1 If true, return 'True'.
      7.2 Otherwise return 'False'.

Implementation 2:
   1. (BEXACTSC) If CAT is DAT, fail and instruct to use Authenticating DAT.
   2. (Registry) If hash(VF$) does not match the DDS State, returns 'False'.
   3. (Registry) If extAC(VF$) matches extAC(ATr) using the matching function, if not return 'False'.
   4. (Registry) If ATr is SAT
      4.1 If ATr is not valid, return 'False'
      4.2 If ATr is valid, then authorize, go to 7.
   5. (Registry) If ATr is DAT
      5.1 If extDDS(ATr)== DDS, go to 7. // "Circular conditional authorization" intended to match format only.
      5.2 Check if extDDS(ATr) is authorized. If not, return 'False'.
      5.3 Rely on Registry to Authentication ATr.
   6. (Registry) check if ATr is valid. DATAuthentication(TT$,ATr)
      - If not valid, return 'False'.
   7. (Registry) check if you hold the `\ extCL(CAT) \extIDS(CAT)[SIG]/`.
      7.1 If true, return 'True'.
      7.2 Otherwise return 'False'.


   Example of "circular conditional authorization":
      CAT = `\ ID <Name: Jane Doe> <Expiration Date: Oct 12,2024> \IDS_gov[DDSid? VF(\ ID <Name:$IGNORE> <Expiration Date:::>>$VTIME> \IDS_gov/, $VTIME )]/` 
      Authentication as follows:
         `CDATAuthentication(VTIME, AC, DDSid?VF(\ ID <Name:$IGNORE> <Expiration Date:::>>$VTIME> \IDS_gov/, $VTIME), \ ID <Name: Jane Doe> <Expiration Date: Oct 12,2024> \IDS_gov[DDSid]/; VerificationFee)`
      Note that the rAT is the same as the original CAT, which is a circular condition. In this case, the rAT will be considered authenticated, so that we benefit from the matching of the VF Prototype `VF(\ ID <Name:$IGNORE> <Expiration Date:::>>$VTIME> \IDS_gov/, $VTIME )`. The matching makes shure that the verification time is prior to the expiration date. Otherwise, the DDS will be unauthorized.
      In this example, we can check that the VTIME is before the expiration date, without having to change authorization at the expiration date.

## Validation of ATP

Validation of ATP includes the authentication of the attestation as well as the validation of the attached files. I.e.:
   - Check that file attachments are included in the ATP. If not, terminate and return `False`.
   - Check that the file hash mentioned in the claim matches that of corresponding included file. If not, terminate and return `False`.


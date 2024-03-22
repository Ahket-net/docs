# Legal Framework

- [Legal Framework](#legal-framework)
   - [Introduction](#introduction)
   - [AT as an Executed Document](#at-as-an-executed-document)
      - [Digital Signature (SIG)](#digital-signature-sig)
         - [Signature Block](#signature-block)
   - [Claim's Boilerplate Clauses](#claims-boilerplate-clauses)
      - [Case 1: Attestation Document](#case-1-attestation-document)
      - [Case 2: Contract](#case-2-contract)
   - [DDS and CDDS](#dds-and-cdds)

---
- Related documents:
   - CADET Reference Manual
      - In which the terms IDS, SK, POA, SIG, DDS, CL, ACL, ATT, SATTT, DATT, CATT, CVF, CVFP, ATTP, and others are defined.

## Introduction

The legal framework outlined in the document mainly discusses the legal aspects of CADET (Cryptographic Attestation Documentation and Evidence Technology), focusing on attestations, digital signatures, and contracts.

## AT as an Executed Document

The legality of a SATT, described as `\ Claim \IDS[SIG]/`, depends on the claim. A SATT represents an "executed document", and the type of the executed document depends on the context of the claim. We can consider two types of claims here: attestation and agreement.
When a claim is digitally signed by one, or more, entity, it is actually an executed document. For simplicity, we will mostly discuss an AT, but the discussion applies to composite SATT as well.

### Digital Signature (SIG)

From a legal perspective, digital signatures offer several significant benefits:

1. **Legally Binding**: In many jurisdictions, digital signatures have the same legal standing as traditional handwritten signatures, making them enforceable in a court of law.

2. **Evidence in Legal Disputes**: Digital signatures can serve as evidence in legal disputes, providing proof of the origin, identity, and status of an electronic document.

3. **Compliance with Regulations**: They help organizations comply with various laws and regulations that require secure and verifiable signatures for certain types of transactions.

4. **Non-Repudiation**: The principle of non-repudiation in digital signatures means that a party cannot deny the authenticity of their signature on a document, which is important in legal scenarios.

5. **Audit Trails**: Digital signatures often come with audit trails, which record the time and date of signing and other relevant information, aiding in legal processes and compliance.

It's important to note that the legal acceptance of digital signatures varies by country and the specific legal context. Therefore, it's advisable to consult legal experts in the relevant jurisdiction when dealing with legal matters involving digital signatures.

#### Signature Block

The signature block in a legal contract is an important section where the parties involved formally execute the document. Here are more details about its structure and function:

1. **Placement**: The signature block is typically located at the end of the contract, following all the terms and provisions.

2. **Party Identification**: Each party to the contract should have a designated space for their signature. This section often includes lines for the signature itself, the printed name of the signer, their title (if applicable), and the date of signing.

3. **Signer's Information**: For each party, the following information is usually included:
   - **Signature Line**: A line where the individual physically signs the document.
   - **Name Line**: A line beneath the signature for the printed name of the signer. This clarifies who the signature belongs to, which is especially important if the signature is not legible.
   - **Title Line**: If the signer is representing an organization or entity, their professional title or role is included.
   - **Date Line**: The date when the signature was affixed. This can be important for determining the effective date of the contract and any timelines within it.

4. **Witness or Notary Public**: In some cases, especially where required by law or for added legal formality, a witness's or notary public’s signature may also be included. They attest to the validity of the other signatures.

5. **Corporate Seal**: For corporations, the signature block might include a space for the corporate seal, although this is less common in modern practice.

6. **Multiple Parties**: In contracts with multiple parties, there should be a distinct signature block for each party, ensuring that everyone’s agreement and execution of the contract are clearly evidenced.

7. **Electronic Signatures**: In digital contracts, electronic signatures are often used. These can include typed names, digital images of handwritten signatures, or signatures created via a digital signing platform. The structure of the signature block remains the same, but the method of signing differs.

The signature block is a critical component, as it symbolizes the parties' agreement to the contract's terms and makes the document legally binding (assuming all other legal requirements for a valid contract are met).

## Claim's Boilerplate Clauses

Every Claim Template is advised to have its "boilerplate clause". We consider two cases here: Attestation Document and Contract, and we suggest the boilerplate clause that must be part of the CT's `FormalText`.

### Case 1: Attestation Document

In this case, the boilerplate clause is the "Attestation Clause" (AttestationClause). It could have the following form:

AttestationClause = '''I, [IDS], hereby attest and affirm that the statement provided herein, along with all associated attachments and exhibits, including digital files, are true and accurate to the best of my knowledge and belief. The attachments are identified as follows: [List Attachments with Descriptions or Identifiers]. This attestation is made on [Date].'''

Therefore, the CT of a SATT should have this AttestationClause as part of its CL.

### Case 2: Contract

In this case, the boilerplate clause is the "Acknowledgment and Agreement" (AcknowledgmentandAgreement). It could have the following form:

AcknowledgmentandAgreement = '''The parties to this agreement, hereby acknowledge and affirm the following:

1. **Confirmation of Understanding**: Each party confirms that they have read, fully understand, and are fully informed about the contents of this agreement and all related digital file attachments, which form an integral part of this agreement. They acknowledge that they have had the opportunity to review all aspects of this agreement and its attachments, including seeking independent legal advice if necessary.

2. **Voluntary Agreement**: The parties affirm that they are entering into this agreement and its attachments voluntarily and of their own free will. They declare that they are not under any undue pressure, coercion, or influence and that they have the full capacity and authority to enter into this agreement.

3. **Acceptance of Terms**: By affixing their digital signatures to this agreement, each party explicitly accepts and agrees to all the terms and conditions outlined herein and in all associated digital file attachments. They acknowledge that these terms are fair, reasonable, and mutually beneficial.

4. **Legal Binding**: The parties understand and agree that this document, along with its digital attachments, once digitally signed, is legally binding. They expressly intend this agreement and its attachments to be enforceable and to create legally binding obligations in accordance with their terms.

5. **Final Affirmation**: This acknowledgment and agreement serve as a final affirmation of the parties' understanding, consent, and commitment to the entire agreement, including all digital file attachments. This clause, placed deliberately near the digital signature lines, emphasizes the serious and binding nature of the parties' commitments under this agreement.

Digitally Signed:

```
[Party 1 Digital Signature]     Date: [____/____/____]
[Party 2 Digital Signature]     Date: [____/____/____]
```

Legally, the "Acknowledgment and Agreement" section in a binding legal document, such as a contract, serves a few specific purposes:

1. **Confirmation of Understanding**: This section is where the parties acknowledge that they have read, understood, and are fully informed about the contents and implications of the document.

2. **Voluntary Agreement**: It signifies that the parties are entering into the agreement voluntarily, without any undue pressure or influence.

3. **Acceptance of Terms**: This part explicitly states that the parties accept and agree to the terms and conditions laid out in the document.

4. **Legal Binding**: By acknowledging and agreeing to the document, the parties are indicating their intent to be legally bound by its terms. This is crucial for enforceability in a court of law.

5. **Final Affirmation**: It acts as a final affirmation of the entire agreement, often positioned near the signature lines to underscore the seriousness and binding nature of the signatories' commitments.

In essence, the "Acknowledgment and Agreement" section is a critical component of legal documents, ensuring that all parties are aware of, and agree to, the terms and conditions they are committing to.

## DDS and CDDS

The basic principle in CADET is the SATT as it represents an executed document. However, when the signer of the SATT decides to convert it to a DDS and CDDS, the signer is entrusting a Registry to 1) store the SIG part of the SATT in a private storage, and 2) authorizing the Registry to answer requests to verify the authenticity of the ACL of the SATT. The process of creating the DDS (or CDDS) for the first time includes the following SATT:

`\ I, [IDS], hereby grant authorization to Registry [RegistryID] to validate and authenticate the attested claim linked with the Delegated Digital Signature [DDS.ID] on [date]. \ IDS [SIG]/`

Whenever the signer would like to disable the authentication, it will use the following SATT:

`\ I, [IDS], hereby revoke and nullify any previous authorization to Registry [RegistryID] to validate and authenticate the attested claim linked with the Delegated Digital Signature [DDS.ID] on [date], irrespective of any prior authorizations granted, revoked, or otherwise altered. This authorization supersedes and renders null any previous directives to the contrary. \ IDS [SIG]/`

The signer can reauthorize again using the following SATT:

`\ I, [IDS], hereby grant authorization to Registry [RegistryID] to validate and authenticate the attested claim linked with the Delegated Digital Signature [DDS.ID] on [date], irrespective of any prior authorizations granted, revoked, or otherwise altered. This authorization supersedes and renders null any previous directives to the contrary. \ IDS [SIG]/`

The same if the authorization is to be conditional in case of CDDS:

`\ I, [IDS], hereby grant authorization to Registry [RegistryID] to validate and authenticate the attested claim linked with the Delegated Digital Signature [DDS.ID] on [date], contingent upon the fulfillment of the stipulated condition [VF$], irrespective of any prior authorizations granted, revoked, or otherwise altered. This authorization supersedes and renders null any previous directives to the contrary. \ IDS [SIG]/`
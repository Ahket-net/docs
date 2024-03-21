# STASH Reference Manual

## Table of Contents <!-- omit from toc -->
- [STASH Reference Manual](#markdown-header-stash-reference-manual)
   - [STASH (System for Truthful and Accountable Secrets-Holding)](#markdown-header-stash-system-for-truthful-and-accountable-secrets-holding)
   - [STASH Components:](#markdown-header-stash-components)
      - [Registries](#markdown-header-registries)
         - [Issuer Registry](#markdown-header-issuer-registry)
      - [Body of EXACT Smart Contracts (BEXACTSC)](#markdown-header-body-of-exact-smart-contracts-bexactsc)
         - [EXACT Protocol (EXACTP)](#markdown-header-exact-protocol-exactp)
            - [Insurance Protocol (Insurance EXACTP)](#markdown-header-insurance-protocol-insurance-exactp)
               - [Basic Insurance](#markdown-header-basic-insurance)
               - [Revenue Insurance](#markdown-header-revenue-insurance)
            - [Registration Protocol (Registration EXACTP)](#markdown-header-registration-protocol-registration-exactp)
               - [Oblivious Transfer](#markdown-header-oblivious-transfer)
               - [Exception](#markdown-header-exception)
            - [Verification Protocol (Verification EXACTP)](#markdown-header-verification-protocol-verification-exactp)
               - [Ambiguous Result](#markdown-header-ambiguous-result)
               - [Exception](#markdown-header-exception-1)
            - [Arbitration Protocol (Arbtiration EXACTP)](#markdown-header-arbitration-protocol-arbtiration-exactp)
               - [Types of Challenges](#markdown-header-types-of-challenges)
         - [BEXACTSC](#markdown-header-bexactsc)
            - [DBN Contract](#markdown-header-dbn-contract)
            - [Tickets Contract](#markdown-header-tickets-contract)
            - [Registries Contract](#markdown-header-registries-contract)
            - [Payments Contract](#markdown-header-payments-contract)
            - [Arbitration Contract](#markdown-header-arbitration-contract)
   - [Achieving EXACT Through Financial Enforcement](#markdown-header-achieving-exact-through-financial-enforcement)
      - [Financial Rewards](#markdown-header-financial-rewards)
      - [Financial Penalties](#markdown-header-financial-penalties)
   - [Interacting with STASH](#markdown-header-interacting-with-stash)
      - [DDS Interactions](#markdown-header-dds-interactions)
         - [Creating a DDS](#markdown-header-creating-a-dds)
         - [Changing an existing DDS](#markdown-header-changing-an-existing-dds)
         - [Verifying a DDS](#markdown-header-verifying-a-dds)
         - [DDS Arbitration](#markdown-header-dds-arbitration)
      - [Registry Management Interactions](#markdown-header-registry-management-interactions)
   - [STASH Accounting](#markdown-header-stash-accounting)
         - [STASH DBN Treasury](#markdown-header-stash-dbn-treasury)
         - [STASH DBN Account](#markdown-header-stash-dbn-account)



> **Notes About This Document**
>  Markdown is used. Also, the marker '$' is used to mark latex mathematics and formulas.
>  Many Ahket-specific terminologies, acronyms, and abbreviations are used in the Reference Manual.

- Related documents:
   - CADET Reference Manual
      - In which the terms IDS, SK, POA, SIG, DDS, CL, ACL, ATT, SATT, DATT, CATT, CVF, CVF$, ATTP, and others are defined.

## STASH (System for Truthful and Accountable Secrets-Holding)
- STASH is the underlying system created and used by Ahket Inc. It is responsible for the creation, update, management, and verification of a DDS.
- Ahket Inc. has filed a provisional US patent for STASH.

## STASH Components:
STASH has two components: Registries and BEXACTSC.

### Registries
- A STASH can have one or more Registries. 
- Ahket Inc owns and manages the first Registry, which is called "The Ahket Registry".
- In general, a Registry does not have to be owned or managed by Ahket Inc. A Registry can be created, owned, and managed by another entity, but it must comply with the "EXclusivity, Accuracy, Consistency, and Transparency (EXACT)" Protocols. Only EXACT compliant Registries are admitted into Ahket Inc.'s STASH. Failure of compliance will result in penalties and expulsion from Ahket's STASH.
- A Registry must work according to the "EXACT Protocols".
- A Registry is responsible for:
   - Participating in the creation and management of a DDS.
      - Securely Storing the `DDS Record` associated with a DDS.
   - Creating and updating a DDS on behalf of its DDS signer. (see the Registration EXACT Protocol)
   - Answering a verification request by DDS verifier of a DDS.(see the Verification EXACT Protocol)
      - The Registry earns a share of VerificationFee paid for the verification.
   - Depositing the required RID. (see the Insurance EXACT Protocol)
   - Responding to challenges. (see the Arbitration EXACT Protocol)

#### Issuer Registry
It is a special Registry owned and managed by a DDS signer and ONLY handles DDSs created by that DDS signer. It offers the following advantages to the signer:
   - It does not require the Exclusivity assurance in EXACT, since there is no threat of the Registry leaking the SIGs and harming DDS signer--since DDS signer and the Registry are the same.
   - It does not require the revenue component of RID since the Exclusivity requirement is not necessary. This lowers the RID amount required from an Issuer Registry.
   - The DDS signer that owns the Registry maximizes the revenue from verification since it is the sum of the DDS owner and Registry shares in VerificationFees.

**Important** It is possible to transfer the ownership of a DDS to a another owner; however, DDS signer should provide legal assurances of not leaking the SIGs in the DDS Record. For example, an Ownership Tranfer Agreement.

### Body of EXACT Smart Contracts (BEXACTSC)
BEXACTSC is the collection of "EXACT Smart Contract"s (EXACTSC) running on a blockchain and governs the operation of STASH.
"EXACTSC" is a blockchain smart contract that automate and enforce some rules and guidelines required from STASH.
The BEXACTSC implements the EXACT Protocols (EXACTPs).

#### EXACT Protocol (EXACTP)
An EXACTP is comprehensive set of rules and guidelines to enforce the compliance of the various users and Registries.
The collection of EXACTPs ensure that all participants adhere to the core principles of EXACT, thus maintaining the reliability and integrity of creating, updating, and verifying DDSs. 
The function of the EXACT Protocols include:
   - **Arbitration**: The EXACT Protocols establish a framework for arbitration and dispute resolution to address any issues or conflicts that may arise between the system's stakeholders. This ensures that all parties are held accountable for their actions and that the system operates fairly and transparently.
   - **Auditing**: The EXACT Protocols include measures for auditing the creation, updating, and verification of any DDS. These checks ensure that the system is functioning as intended and that all participants are adhering to the required standards.
   - **Security and Cryptography**: An EXACTP describe various cryptographic techniques and secure communication channels required to protect the privacy, authenticity, and integrity of STASH. Such as the use of digital signatures, encryption, and secure protocols for data transmission and storage.

There are four core protocols.

##### Insurance Protocol (Insurance EXACTP)
This protocol manages the "Registry Insurance Deposit" (RID), which is the monitary insurance (denominated in DBN) required from a Registry in order to operate in STASH. It is the reserve paid by a Registry in order to penalize it in case of a misbehavior (as described in the Arbitration Protocol). 
The RID is the sum of two quantities: "Basic Insurance" and "Revenue Insurance".
###### Basic Insurance
- Any DDS held by a Registry requires a Basic Insurance to be deposited by the Registry.
- This insurance should cover all compliance of EXACT by the Registry.
- Basic Insurance is included in $BASE_R$ component of the VerificationFee.

###### Revenue Insurance
This component is calculated from the "Cumulative Future Revenue" (CFR) generated from a DDS. CFR of a DDS is an estimate of the future revenue that DDS owner expects from a DDS based on "Adjusted Cumulative Past Revenue" (jCPR). jCPR is continuously calculated as follows:
   - When a DDS is created at $t_0$, $jCPR(t_0) = 0$
   - When a DDS is verified at time $t_n$:
      $jCPR(t_n) = jCPR(t_{n-1}) \times 2^{(-(t_n - t_{n-1})/T_H)} + R(t_n)$, where
         - $t_{n-1}$ is the last verification time before $t_n$, or $t_0$ if there were no verifications before.
         - $R(t_n)$ is the DDS owner's revenue (reward) from verification at $t_n$.
         - $ T_H $ is a time constant that determines the discount of previous returns over time—which we call the Owner Reward Half Time. I.e., the value of jCPR of a DDS will be halved after $T_H$ time.
   - At any point of time $t_n =< t < t_{n+1}$, 
      - $jCPR(t) = jCPR(t_n) \times 2^{(-(t - t_n)/T_H)}$

The CFR is calculated as follows:
   - $CFR(t) = RHI \times jCPR(t)$
      where RHI is some predefined multiplier (most probably is set to 100%) that determines the amount of jCPR that is covered when a loss occurs.
The insured value of a DDS at time $t$ is its $CFR(t)$.

The "Registry Cumulative Future Revenue" (RCFR) of a Registry is the sum of the RCFs of all the DDSs held in the Registry at time $t$. It is calculated from the "Registry Adjusted Cumulative Past Revenue" (RjCPR), which is the total sum of the $jCPR(t)$ values of all the held DDSs. Therefore,
   - $RCFR(t) =  RHI \times RjCPR(t)$

RjCPR can be calculated in a more efficient way as follows:
   - When the registry is created at $t_0$, $RjCPR(t_0) = 0$
   - When any DDS is verified at time $t_n$, its $RjCPR$ changes as follows: 
      $RjCPR(t_n) = RjCPR(t_{n-1}) \times 2^{(-(t_n - t_{n-1})/T_H)} + R(t_n)$, where
         - $t_{n-1}$ is the last verification time before $t_n$, or $t_0$ if there were no verifications before,
         - $R(t_n)=DDSP$, from the verification VerificationFee at time $t_n$,
   - At any point of time $t$, such that $t_n =< t < t_{n+1}$,
      $RjCPR(t) = RjCPR(t_n) \times 2^{(-(t - t_n)/T_H)}$

For any Registry that is not an Issuer Registry, RID at any time $t$ must be greater than the sum of the required Basic and Revenue Insurances. That is,
   $RID(t) > BI(t) + k \times RCF(t)$,
      where $BI$ is the Basic Insurance, and $k>1$ is some factor representing the risk of failure.

The Registry's shares in VerificationFee and IFEE are deposited into its RID. I.e.,
   $RID(t_n) = RID(t_{n-1}) + RegistryShare$

When a Registry is challenged or penalized (See the Arbitration Protocol) at time $t$, a "Challenge Penalty" (CP) will be withdrawn from RID. I.e.,
   $RID(t_n) = RID(t_{n-1}) - CP(t)$

A Registry can withdraw an amount $w(t)$ at any time $t$ from the RID with the following limitation:
   $w(t) < RID(t) - BI(t) - k \times RCF(t)$.
   - This is the Registry's insurance surplus, or profit.
   - Because of the half-time effect, $w(t)$ will increase with time if there are no verification activities.

A Registry can create a DDS for a DDS signer, it must have sufficient RID to do so. 
   - A Registry can create the DDS if the following condition is true:
      $RID(t) - BI(t) - k \times RCF(t) >  k \times RHI \times DDSP$.
   - If the condition is not satisfied, BEXACTSC will stop the creation of the DDS.
   - A Registry, therefore, must make sure that the condition is satisfied to create a new DDS. This can be done by depositing some amount to the RID. This amount can be considered as an investment by the Registry.

For Issuer Registry, the Revenue Insurance is not required. Therefore, $RID(t) > BI(t)$ is sufficient. The computation of $w(t)$ is the same as all Registries except for dropping the revenue part.

If a DDS is generating no revenue, as in the case DDSP is zero, only the Basic Insurance still has be funded by Registry.

##### Registration Protocol (Registration EXACTP)
This protocol describes the process of creating or updating a DDS in Registries. The protocol is to uphold the EXclusivity Assurance to by making preventing the leak of the held SIG to the public.

###### Oblivious Transfer
Since a leak of a SIG could be from, either, the signer of that DDS or the Registry, it is important to identify the culprit when a leak occurs. To do so, a Zero-Knowledge cryptographic technique called 1-n Oblivious Transfer (1-nOT) is used. 
This is required in the communication between the DDS signer and the Registry, which is not recorded on the blockchain.

Specifications:
   - When DDS signer creates a DDS it has to send its SIG to the Registry. DDS signer is required to create a number of salted SIGs $NSA$ for the same DDS.
   - Using 1-nOT, the Registry receives only one out of these $NSA$ salted SIGs, and DDS signer does not know which one it is.
   - If DDS signer acted in bad faith and decided to leak the SIG to falsely blame the Registry of leaking through the Arbitration EXACTP, it will not know which one is stored in the Registry.
   - If DDS signer picks randomly one of the salted SIG for arbitration, there is only $1/NSA$ chance of being that selected by the Registry.
   - Therefore, if the selected salted SIG is leaked, it has only a chance of $1/NSA$ to be leaked by DDS signer and the Registry is condemned. Assuming of course there is only one, and only one, chance of accusing the Registry of leaking through the Arbitration EXACTP.
   - $NSA$ is one of STASH Parameters. The larger the number, the more secured is the system, but it means slower 1-nOT.

###### Exception
There is no need for 1-nOT in case of Issuer Registry, since the DDS signer and Registry are the same in the latter.

##### Verification Protocol (Verification EXACTP)
The goal of the Verification EXACTP is to guarantee that the Registry does not provide a strong proof (such as the SIG of the DDS) because this will be considered a leak, while at the same trying to provide a public information for auditing. 
Thus, only the DDS verifier would receive an answer on a separate channel from the Registry, while the Registry will only post an "Ambiguous Result" on the blockchain (for traceability).

###### Ambiguous Result
When a DDS verifier requests to verify a DATT, it will provide the answer in the following steps.
   1. The Registry creates three random numbers (n1,n2,n3), and then sends n1 to DDS verifier.
   2. DDS verifier commits to n1 by creating a digital S1 of n1 using its Secret Key. DDS verifier then sends S1 to the Registry.
   3. The Registry creates `hash(n1|S1|SIG|n3)`, where SIG is that stored in the Registry. Note that only the Registry knows this, and DDS signer of the DDS knows that it is one of `NSA` that were created---but does not know which one exactly.
   4. The Registry posts the following on the blockchain:
      - `hash(n1|S1|SIG|n3)`.
      - `(n1,n2)` if the answer is True, or `(n2,n1)` if the answer is False.

DDS verifier can check the blockchain and recognize from the position of n1 if the answer is True of False.
Any other person will not know the answer just by reading the blockchain.
Even if DDS verifier told anyone else about the number, it is as good as telling the answer. DDS verifier cannot present an evidence.
The only way for a person to have a definitive answer on the blockchain is to demonstrate the content of `hash(n1|S1|SIG|n3)`, but that means that the Registry must leak `SIG`, which will be detectable then.

###### Exception
If the owner of the DDS decided to make their revenue unprotected, there will be no need for an ambiguous verification result. In this case, the Registry can post the result of `True` or `False` on the blockchain. To do so, the owner sets the DDS's `unprotectedRevenue` to `False`. **Note** that once `unprotectedRevenue` is turned off, it cannot be turned on again. 

##### Arbitration Protocol (Arbtiration EXACTP)
The Arbitration EXACTP is the way the System enforces the compliance of the Registries and the Users and uphold the EXACT Assurances. The principles are:
   - EXACT Challenger (`challenger`) is an individual or entity, who challenges the Registry of failing to uphold the EXACT Assurances. This is done by submitting a Challenge from a predefined set of Challenges after depositing some money for seriousness.
   - The Challenge is considered to be True, unless the Registry can respond by submitting one of the predefined Responses associated with the accusation and the relevant information and before a certain period of time (Reponse Period). 
      - If the response is valid, the Registry is exonerated and challenger loses the deposited money.
      - If the Registry fails to respond, then the Registry is penalized by extracting a penalty amount (depending on the Challenge) to be given to challenger in addition to the initially deposited amount.

###### Types of Challenges
   - Challenge of leaking any of SIGs in the DDS Record
   - Challenge of wrong answer
   - Challenge of inconsistent answer
   - Challenge of denying registration
   - Challenge of dening verification 

#### BEXACTSC
Ahket Inc is the admin of the smart contracts.

BEXACTSC responsibilities include:
   - Admitting and overseeing the operations of Registries.
   - Distribution of fees, which are:
      - IFEE, where STASH Issuance Share (SISHARE) is 100%.
      - Verification fee (VerificationFee)
   - Storing all interactions (creation, updating, and verification of DDS; transfer of ownership; fee distributions). 
   The following are the EXACTSCs in BEXACTSC.

##### DBN Contract
- Responisble of minting and burning DBN (fungible tokens).
- DBN is a cryptocurrency underlying the economics of STASH.
- Admin: Ahket Inc

##### Tickets Contract
- Admin: Ahket Inc
- This is smart contract for non-fungible tokens, which call `tickets`.
- Tickets are needed for interactions with the Registries. They show the commitment of the user, including proof of paying for the required fees.

##### Registries Contract
- The interface between Registries and STASH.
- Registries only interact with the Registries Contract.
- Responsible for:
   - Adding New Registries
   - Penalizing Registries
   - Creating/updating DDSs by Registries
   - Verifying DDSs
   - Calculating jCPR, CFR, RjCPR, and RID
   - Managing RID
- Admin: Ahket Inc

##### Payments Contract
- Admin: Ahket Inc
- The interface between users (DDS owner and DDS verifier) and STASH.
- Receive fees (VerificationFee and IFEE) and creates tickets. A ticket must be created in order to issue (register) or verify a DDS that will hold the required fees for the operation.
- The creation of a ticket requires, in most cases, depositing DBN into it.
- The owner of a ticket is called the "Ticket Owner". The Ticket Owner does not have to be the DDS signer or DDS verifier.
- There are different kind of tickets
   1. DDS Registration Ticket
      - This is used to issue or amend a DDS.
      - The ticket is created by the Ticket Owner, who specifies on it the DDS signer that will create the DDS.
      - Its information is public, since it is on the blockchain. Information includes:
         - History of all the changes to the DDS's authorization state and the dates of these changes.
         - For each change, some important metadata is included to prove the commitment of the Registry according to the Registration EXACT Protocol.
      - It is closed when the DDS is created or amended. 
      - As long as the Registry did not start the registration process, the Ticket Owner can cancel the ticket and retrieve the paid IFEE.
   2. DDS Verification Ticket
      - This is used to verify a DDS.
      - The Ticket Owner that creates the ticket will specify on it the DDS verifier. Therefore, a person can verify a DDS while a different person creates the DDS Verification ticket and fund it for verification.
      - All information on the ticket are public.
      - The Ticket Owner creates the information that specifies:
         - The DDS to be verified.
         - The DDS verifier that wants to verify the DDS.
         - A Transferability Flag 
            - If True then the current DDS verifier can transfer the ticket to another DDS verifier.
            - If False, then the current DDS verifier cannot transfer the ticket. 
      - The ticket is closed when the DDS is verified. 
         - As long as the Registry did not start the verification process, the Ticket Owner can cancel the ticket and retrieve the paid VerificationFee.

Distributing the shares to Registries, DDS owner, and STASH is calculated as follows:
   - Determining the VerificationFee and its distribution is as follows:
      - **DDS Price** (DDSP)
         - DDSP is the reward (revenue) that DDS owner expects from verifications.
         - Only DDS's owner (DDSO) can set its DDSP, and the owner has two different options:
            - Individual Priced DDS: The DDSP is specified directly in the DDS.
            - Group Priced DDS: The DDSP is determined by referencing a specific Reference DDSP.
      - VerificationFee of a DDS is calculated from DDSP as follows:
         - VerificationFee = DDSP + SHARE_R + SHARE_S, where
               - SHARE_R is the Registry's share in DBN.
                  - SHARE_R = BASE_R + DDSP * TaxRate_R.
                  - BASE_R is the required minimum to cover the Registries running cost---including paying any needed blockchain gas fees, and the Basic Insurance needed.
                  - TaxRate_R is the Registry Tax Rate as a percentage.
               - SHARE_S is the STASH's share in DBN.
                  - SHARE_S = BASE_S + DDSP * TaxRate_S
                     - BASE_S is the required minimum to contribute to the STASH's running cost.
                     - TaxRate_S is the STASH Tax Rate as a percentage.
            - Example: 
               - SHARE_R = 0.2 5DBN
               - TaxRate_R = 30%
               - SHARE_S = 0.25 DBN
               - TaxRate_S = 20%
               - If DDSP is 2 DBN, then:
                  - SHARE_R = 0.25 + 2*30% = 0.85 DBN
                  - SHARE_S = 0.25 + 2*20% = 0.65 DBN
                  - VerificationFee = 2 + 0.85 + 0.65 = 3.5 DBN

##### Arbitration Contract
- Implements the Arbitration EXACTP.
- Used to investigate misbehavior by parties and penalize.

## Achieving EXACT Through Financial Enforcement
STASH achieves EXACT Assurance through Financial Enforcement by rewards and penalties.

### Financial Rewards
Financial Rewards in the System serve as incentives for DDS signer and Registries to actively participate and commit to the platform. For example, VerificationFees paid by Verifiers for authentication services are distributed among DDS owner and Registries encourage the creation of DDSs as source of revenue, as well as rewarding the Registries for their operational risks.

### Financial Penalties
Registries are penalized by deducting DBNs from their RIDs when they fail to uphold EXACT Assurances according to Arbtiration EXACTP.

## Interacting with STASH
Any entity can interact with the STASH Registries, BEXACTSC, or both.

### DDS Interactions
In most cases, tickets must be created first through the Payments Contract and before interacting with a Registry. This helps in tracing the activities and making stakeholders and the Registry accountable.

#### Creating a DDS
This is done by DDS signer. It requires a ticket.
In case of Issuer Registry, the signer interacts with the Payments Contract.
In case of non Issuer Regisrtries,  signer interacts with the Payment Contract to create a ticket and the Registry that will hold the private DDS information.

#### Changing an existing DDS
This includes the following interactions:
   - Changing Authorization, by DDS signer. Requires a ticket.
   - Changing Verification Fee, by DDS owner.
   - Changing the Owner, by current DDS owner.
   - Changing the Holder, by DDS holder.
   - Terminate a DDS, by DDS owner.
   - Convert DDS to SIG, by DDS owner.

#### Verifying a DDS
This is done by a DDS verifier. It requires a ticket.

#### DDS Arbitration
This is done by a DDS verifier. It requires a ticket.

### Registry Management Interactions
This includes the following interaction:
   - Adding New Registry

## STASH Accounting
All payments, deposits, fees, etc used in STASH are in DBN token.
   - E.g., a VerificationFee of 10.0 means 10.0 DBN were to be paid as a verification fee.
   - DBN is set so that 1 DBN can be exchanged with 1 US Dollar (USD), and vice versa.

#### STASH DBN Treasury
All the STASH's share of the issuance and verification fees will be deposited into the STASH DBN Treasury.

#### STASH DBN Account
This account holds all The RIDs of all the Registries in STASH.
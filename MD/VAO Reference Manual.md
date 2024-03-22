# VAO Reference Manual <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [About VAO](#about-vao)
- [VAO's Glossary Peculiarities](#vaos-glossary-peculiarities)
   - [Simple Attestation](#simple-attestation)
   - [Rewarding Attestation](#rewarding-attestation)
   - [Attestation Owner in VAO](#attestation-owner-in-vao)
   - [Points](#points)
      - [Activity Points (AP)](#activity-points-ap)
      - [Reward Points (RP)](#reward-points-rp)
   - [Points Redemption Rate (PRR)](#points-redemption-rate-prr)
      - [RP Redemption Plan](#rp-redemption-plan)
- [VAO Operation](#vao-operation)
   - [Creating and Verifying Attestations](#creating-and-verifying-attestations)
- [Ahket Inc Revenue](#ahket-inc-revenue)
- [Access to Ahket's STASH](#access-to-ahkets-stash)

---

- Related documents:
   - CADET Reference Manual
      - In which the terms IDS, SK, POA, SIG, DDS, CL, ACL, ATT, SATT, DATT, CATT, CVF, CVFP, ATTP, and others are defined.
   - STASH Reference Manual
   - VAO User Manual

## About VAO

VAO is a WEB2 webapp of Ahket, that is:
- built on top of STASH.
- the interface between Ahket's users and STASH. It isolates the users from STASH complexity.
- owned, managed, and under the full control of Ahket Inc.
- WEB2 product of Ahket Inc.
- the portal for users to be issuers, verifier, owners, and beneficiaries of attestations.

## VAO's Glossary Peculiarities

VAO uses different and simpler terms than those used in the CADET and STASH Reference Manuals.

### Simple Attestation

Simple Attestation corresponds to SATT.
This attestation does not generate any revenue to the issuer. When a VAO user creates a Simple Attestation, VAO creates a SATT, and when a VAO user verifies a Simple Attestation, VAO verifies the SATT.

### Rewarding Attestation

Rewarding Attestation corresponds to DATT.
Based on the pricing, we have two subtypes:

- *Individual-Price* Rewarding Attestation
   
   - A user can create, amend and verify Single-Price Attestations if the user's APB holds enough APs to cover the STASH fees.
   
   - When a user creates a Single-Price Attestation, VAO creates a DATT on behalf of the user on STASH.
   
   - A user can amend a Single-Price Attestation via STASH.
   
   - When a user verifies a Single-Price Attestation, VAO verifies the corresponding DATT through STASH.
   
   - Rewards earned by the corresponding DATT on STASH are added as RPs to the owner's RPB.

- *Group-Price* Rewarding Attestation
   
   - A user that joins the Shared-Reward Program can create, amend and verify as many Group-Price Attestations as the user's Subscription allows.
   
   - When a program is established, a Reference Price is set on the STASH System (Payments Contract).
   
   - When a user creates a Group-Price Attestation, VAO creates a DATT on behalf of the user on STASH who is Group Priced to the Reference Price.


### Attestation Owner in VAO

An attestation "owner" in VAO is not the actual owner of its DDS. Ahket Inc is the actual DDS owner. Ahket Inc is a custodian of all attestation owners in VAO, and the owner of a rewarding attestation in VAO is owner "in name only." The account is actually under the full control of Ahket Inc---the real DDS owner. \
The owner of a Rewarding Attestation in VAO is also not allowed by VAO to transfer the ownership to another user.

A VAO user can become a "full owner" of an attestation (i.e., the owner of the attestation's DDS) when the WEB3 is activated. In this case, the following steps are required:

- Ahket Inc opens STASH for direct access by the public through DAPPS,

- VAO Owner creates an account using crypto wallet, and

- Based on the attestation owner request, Ahket Inc transfers the attestation ownership to the wallet's account.

### Points

In VAO, points are abstractions of DBN in STASH. Each point represents one DBN. Users of VAO don't have direct access to DBNs.
A user cannot transfer points to another user; therefore, the underlying DBNs cannot be transferred from a user to another.
There are two kinds of points:

#### Activity Points (AP)
   
- It represents the DBNs that a user have that can be used for:
   - Creating and updating rewarding attestations (paying IFEE).
   - Verifying rewarding attestations (paying VerificationFee).

- APs will have some expiration date. When they expire, the underlying DBNs will be transferred to Ahket Inc's General DBN Account (GDBNA).

- When APs are created for the first time, an equivalent amount of DBNs are minted.

#### Reward Points (RP)

- It represents the DBNs earned by the user as the owner share in verification fees.

- RPs will have some expiration date. When they expire, the underlying DBNs will be transferred to Ahket Inc's GDBNA.

- RPs can be redeemed by users according to RP Redemption Plan.
   - When RPs are redeemed, an equivalent amount of DBNs are burnt.

> **Note**
>  
> A Registry that needs to add DBN to increase its RID can do this by depositing USD into GUSDA to mint DBNs, which are deposited into its RID.

### Points Redemption Rate (PRR)

This represents the value of RPs in US Dollars (USD).
The PRR at any time is calculated by dividing the amount of USD in General USD Account (GUSD) divided by the Total Minted DBN (TMDBN) at that time.

#### RP Redemption Plan

A user can redeem RPs for some gift award value in USD calculated from the PRR at that time.

## VAO Operation

A VAO user must sign up and create a "VAO account" to be able to:
- Issue and verify an unlimited number of Simple Attestations.
- Purchase APs directly from Ahket Inc, which will be added to the user's APB.
- Use APs from their APBs to issue, amend, and verify Individual-Price Rewarding Attestations.
- Earn RPs that can be redeemed for some value.
- Join a Shared-Reward Program.

A VAO user will have two STASH accounts, each holds a DBN amount equal to its balance:

- **AP Balance** (APB)
   - Increases by adding AP.
   - Decreases when paying IFEE and VerificationFee, or when APs expire.

- **RP Balance** (RPB)
   - Increases when a user earns the owner's share in VerificationFees.
   - Decreases when RPs are redeemed or expire.   

### Creating and Verifying Attestations

VAO creates and manages attestations as detailed in the Creation and Verification of Attestations section of the CADET Reference Manual. This requires the interaction with STASH if needed.

## Ahket Inc Revenue

- DBN Revenue is from:
   - All the DBNs in the STASH DBN Account.
   - The Registry Share of the Registries owned by Ahket Inc.
   - All the DBNs in the GDBNA.
- The revenue in USD is calculated from the DBN Revenue based on the PRR.

> **Notes**
> 
> - All DBNs are redeemable from GUSDA by burning DBNs and withdraw the equivalend USDs.
> 
> - The RPR is variable according to the activities.
>
> - In case of reduced collected SFs---in case of promotion and free trials, the PRR adjusts accordingly.

## Access to Ahket's STASH

Ahket's STASH can be accessed by users as follows:

   - VerifyAtOnce (VAO)
      - VAO is the main product of Ahket Inc.
      - It is a web application.
      - This is a conventional Web 2.0 (WEB2) solution. Therefore it is a centralized service.
      - Further details is provided in the CADET, STASH, and VAO User Manuals.
   
   - Later, through Decentralized Application (DAPP)
      - These are Web 3.0 (WEB3) solutions.
      - First DAPPs will be created by Ahket Inc.
      - There will be a number of them, each accessible and functioning using the DBN token.
      - Others will be able to their own DAPPs and WEB3 solutions when Ahket Inc. makes the documentation of STASH available and open source the code.
      - VAO will still provide easy access to users reluctant, or unauthorized, to use DAPPs.
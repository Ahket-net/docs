# VAO User Manual

## Table of Contents <!-- omit from toc -->
- [VAO User Manual](#vao-user-manual)
   - [Product Overview](#product-overview)
      - [Key Benefits](#key-benefits)
      - [Other Standout Features](#other-standout-features)
   - [Manual](#manual)

---

- Related documents:
   - CADET Reference Manual
      - In which the terms IDS, SK, POA, SIG, DDS, CL, ACL, ATT, SATT, DATT, CATT, CVF, CVFP, ATTP, and others are defined.
   - STASH Reference Manual
   - VAO Reference Manual

## Product Overview

### Key Benefits

- Revenue-sharing
   - Incentives for issuers (revenue is the verification fee paid by verifiers)
- Liquidity
   - Transferable Revenue Rights.
- EXACT Assurances:
   - Ahket's technology is EXlusive, Accurate, Consistent, and Traceable (EXACT):
   - EXclusive: Every attestation from issuers is exclusively verifiable via Ahket. In other words, all the stored digital signatures must be secured within Ahket and cannot be leaked.
   - Accurate: All verification outcomes directly align with the attestations provided by issuers.
   - Consistent: Regardless of how many times a verification is repeated, the result remains consistent without any contradictions or discrepancies.
   - Traceable: Every activity, whether it's an attestation, a verification, a transfer of Revenue Rights, or even a distribution of verification fees, is recorded for transparency and easy tracking by relevant users.
- The EXACT Assurances are enforced by smart contracts and blockchain technology, as described in STASH Reference Manual.

### Other Standout Features

- Multimodality
   - Multimodal Claim Verification
      - Letters, Transcripts, college degrees, awards
      - Testimonials
      - Reviews
      - Media
      - Legal Documents
      - Employment
   - One-stop solution
- Universality
   - Globally accessible
- Privacy
   - No AHKETI content is saved on Ahket, only digital signatures that link contents to issuers
- Seamless Integration
   - Multi-platform compatibility
      1. Documents
      2. Webpages (e.g. LinkedIn)
- Affordability
   - Low-cost, real-time verification

## Manual

- User Roles
   - User of VAO are verifiers, issuers, owners, holders, and beneficiaries.
         - Beneficiary holds a 'beneficial' attestation and is neither an issuer, nor a current owner of that attestation.
         - E.g., John can hold a rewarding attestation issued by a college attesting that he earned some degree. He can share that attestation with future employers so that they can verify the earned degree as a part of background check.

- A first time user of VAO must sign up.
   - Sign up will require a username, email address, and login password.
   - By signing up, a user can verify attestations shared with by other beneficiaries, owners, and issuers.

- A user becomes an attestation owner, if the attestation's ownership is transferred to him by the previous owner of that attestation.
   
- To be able to create an Attestation, a user must become an issuer. This is done by going through Issuer Orientation process.
   - Issuer Orientation:
      - The user will learn about Attestations and how they are created.
      - The user will be required to add external links to the user’s profile. Up to 3 external links can be created to public web pages, including LinkedIn and Twitter.
      - The external links help AHKETO trust the IDS matches the intended issuer.

- VAO UI has four tabs, which will appear as a column on the left in landscape orientation or a wide screen, or as row at the bottom in portrait orientation (in case of mobile or small screen). These tabs are:
   - Dashboard tab: Showing balance as a result of earning points from verification fee of owned Attestation and paying the fees for verifications. It also shows the Activity List: a list of all the activities (attesting verifiable attestations, verifications, and transferring of ownership). When clicking on any item in the Activity List, a dialogue will be opened showing the details of that activity.
   - Verifications tab: Shows the Verifications List; a list of all the verifications done by the user as a AHKETV. Clicking on any item (SomeItem) in the Verifications List opens a dialogue that shows the details of that verification. The dialogue also has the ‘verify again’ button, which when clicked, will launch the Verification Request Dialogue. The Target Time of the Verification Request Dialogue will be set to that of SomeItem.
   - Trusted Issuers tab: Shows the Trusted Issuer List; a list of some issuers' identifiers that have been confirmed, by the user, to be the true ones. When clicking on any issuer in the list, the profile page of that issuer will be opened. The user can change the status of that issuer to Trusted or Untrusted.
   - Attestations tab: Shows the Attestations List; a list of all the VACs relevant to the user. This includes VACs issued by the user, DVACs owned by the user, and VAC beneficial to the user. The list can be filtered to show items owned by, issued by, or AHKETB to the user. When clicking on any item in the list, a dialogue will show the details of that VAC.

- The "+" Icon
   - The + icon that shows in the bottom right corner of the UI is used to launch the Attestation Action dialogue. The Attestation Action Dialogue shows two groups of buttons. The upper group is used for verifications. The lower group is used to create VAC.
   - Using the lower group, a user can create an NVAC or a DVAC. The process for both are similar.
      - After an Attestation is created, a menu appears with options to share or embed the created VAC.
         - When choosing to embed, a URL is generated that specifies all the information needed to verify the Attestation.
            - The user can insert the URL as a link into a document created, such as a CV. The link can have the text '\V/erify'---or just '\V/' in case of space limitation---that indicates an Ahket's verification link. This link, when clicked, goes to the Ahket VAO page and fills the needed fields for verification, such as the IDS and CL. The user can then follow the steps to verify that Attestation. 
   - The user’s name is shown at the top left. When the user clicks on it, it opens the Profile page. The Profile page also has two buttons:
         - Settings button, which opens the Settings page.
         - Logout button, to logout.